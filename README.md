<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D&D 5e - DM Wiki</title>
    <style>
        :root {
            --bg-color: #121212;
            --surface-color: #1e1e1e;
            --primary-color: #d32f2f;
            --text-primary: #e0e0e0;
            --text-secondary: #aaaaaa;
            --border-color: #333333;
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-primary);
            margin: 0;
            display: flex;
            height: 100vh;
            overflow: hidden;
        }

        /* Sidebar Design */
        aside {
            width: 250px;
            background-color: var(--surface-color);
            border-right: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
        }

        .logo {
            padding: 20px;
            font-size: 1.5em;
            font-weight: bold;
            color: var(--primary-color);
            text-align: center;
            border-bottom: 1px solid var(--border-color);
        }

        nav button {
            width: 100%;
            background: none;
            border: none;
            color: var(--text-primary);
            padding: 15px 20px;
            text-align: left;
            font-size: 1.1em;
            cursor: pointer;
            transition: background 0.2s;
            border-bottom: 1px solid var(--border-color);
        }

        nav button:hover, nav button.active {
            background-color: rgba(211, 47, 47, 0.1);
            color: var(--primary-color);
            border-left: 4px solid var(--primary-color);
        }

        /* Main Content Design */
        main {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        header {
            padding: 20px 40px;
            background-color: var(--surface-color);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .search-bar {
            width: 100%;
            max-width: 500px;
            padding: 12px 20px;
            border-radius: 25px;
            border: 1px solid var(--border-color);
            background-color: var(--bg-color);
            color: var(--text-primary);
            font-size: 1em;
            outline: none;
        }

        .search-bar:focus {
            border-color: var(--primary-color);
        }

        #content-area {
            padding: 40px;
            overflow-y: auto;
            flex: 1;
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .section-title {
            font-size: 2em;
            color: var(--primary-color);
            margin: 0;
        }

        /* Data Visualization */
        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
        }

        .card {
            background-color: var(--surface-color);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 20px;
            transition: transform 0.2s;
        }

        .card:hover {
            transform: translateY(-2px);
            border-color: var(--primary-color);
        }

        .card h3 {
            margin-top: 0;
            color: var(--primary-color);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background-color: var(--surface-color);
        }

        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid var(--border-color);
        }

        th {
            color: var(--primary-color);
            font-weight: bold;
        }

        .tag {
            display: inline-block;
            background-color: #333;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.8em;
            margin-right: 5px;
            margin-top: 5px;
        }

        /* Utilidades para los detalles de Razas y Clases */
        .race-traits ul { margin-top: 5px; padding-left: 20px; line-height: 1.5; }
        .race-traits li { margin-bottom: 8px; }
        .race-subrace { background-color: rgba(255,255,255,0.05); padding: 15px; margin-top: 15px; border-radius: 8px; border-left: 4px solid var(--primary-color); }
        .race-subrace h5 { font-size: 1.2em; margin-top: 0; color: var(--primary-color); }
        
        .level-progression { width: 100%; margin-top: 15px; }
        .level-card { background-color: rgba(255,255,255,0.02); border: 1px solid var(--border-color); border-radius: 6px; padding: 15px; margin-bottom: 10px; }
        .level-card h5 { margin: 0 0 10px 0; color: var(--text-primary); border-bottom: 1px solid var(--border-color); padding-bottom: 5px; }
    </style>
</head>
<body>

    <aside>
        <div class="logo">DM Wiki Engine</div>
        <nav id="nav-menu">
            </nav>
    </aside>

    <main>
        <header>
            <input type="text" id="global-search" class="search-bar" placeholder="Búsqueda global en toda la Wiki (Ej. Espada, Elfo, Fuego)...">
        </header>
        <div id="content-area">
            </div>
    </main>

    <script>
        // Variables globales para manejar el estado de selección
        let currentSelectedRace = null;
        let currentSelectedClass = null;

        /**
         * MATEMÁTICA Y LÓGICA DE DATOS
         */
        const database = {
            "Armaduras": [
                { nombre: "Acolchada", coste: "5 po", ca: "11 + DES", fuerza: "-", sigilo: "Desventaja", peso: "8 lb.", tipo: "Ligera" },
                { nombre: "Cuero", coste: "10 po", ca: "11 + DES", fuerza: "-", sigilo: "-", peso: "10 lb.", tipo: "Ligera" },
                { nombre: "Cuero tachonado", coste: "45 po", ca: "12 + DES", fuerza: "-", sigilo: "-", peso: "13 lb.", tipo: "Ligera" },
                { nombre: "Pieles", coste: "10 po", ca: "12 + DES (máx. +2)", fuerza: "-", sigilo: "-", peso: "12 lb.", tipo: "Media" },
                { nombre: "Camisote de mallas", coste: "50 po", ca: "13 + DES (máx. +2)", fuerza: "-", sigilo: "-", peso: "20 lb.", tipo: "Media" },
                { nombre: "Cota de escamas", coste: "50 po", ca: "14 + DES (máx. +2)", fuerza: "-", sigilo: "Desventaja", peso: "45 lb.", tipo: "Media" },
                { nombre: "Coraza", coste: "400 po", ca: "14 + DES (máx. +2)", fuerza: "-", sigilo: "-", peso: "20 lb.", tipo: "Media" },
                { nombre: "Semiplacas", coste: "750 po", ca: "15 + DES (máx. +2)", fuerza: "-", sigilo: "Desventaja", peso: "40 lb.", tipo: "Media" },
                { nombre: "Cota de anillas", coste: "30 po", ca: "14", fuerza: "-", sigilo: "Desventaja", peso: "40 lb.", tipo: "Pesada" },
                { nombre: "Cota de mallas", coste: "75 po", ca: "16", fuerza: "Fue 13", sigilo: "Desventaja", peso: "55 lb.", tipo: "Pesada" },
                { nombre: "Bandas", coste: "200 po", ca: "17", fuerza: "Fue 15", sigilo: "Desventaja", peso: "60 lb.", tipo: "Pesada" },
                { nombre: "Placas", coste: "1500 po", ca: "18", fuerza: "Fue 15", sigilo: "Desventaja", peso: "65 lb.", tipo: "Pesada" },
                { nombre: "Escudo", coste: "10 po", ca: "+2", fuerza: "-", sigilo: "-", peso: "6 lb.", tipo: "Escudo" }
            ],
            "Armas": [
                { nombre: "Bastón", coste: "2 pp", dano: "1d6 contundente", peso: "4 lb.", propiedades: "Versátil (1d8)", tipo: "Armas c/c simples" },
                { nombre: "Daga", coste: "2 po", dano: "1d4 perforante", peso: "1 lb.", propiedades: "Arrojadiza (20/60), ligera, sutil", tipo: "Armas c/c simples" },
                { nombre: "Golpe desarmado", coste: "—", dano: "1 contundente", peso: "—", propiedades: "—", tipo: "Armas c/c simples" },
                { nombre: "Gran clava", coste: "2 pp", dano: "1d8 contundente", peso: "10 lb.", propiedades: "A 2 manos", tipo: "Armas c/c simples" },
                { nombre: "Hacha de mano", coste: "5 po", dano: "1d6 cortante", peso: "2 lb.", propiedades: "Arrojadiza (20/60), ligera", tipo: "Armas c/c simples" },
                { nombre: "Hoz", coste: "1 po", dano: "1d4 cortante", peso: "2 lb.", propiedades: "Ligera", tipo: "Armas c/c simples" },
                { nombre: "Jabalina", coste: "5 pp", dano: "1d6 perforante", peso: "2 lb.", propiedades: "Arrojadiza (30/120)", tipo: "Armas c/c simples" },
                { nombre: "Lanza", coste: "1 po", dano: "1d6 perforante", peso: "3 lb.", propiedades: "Arrojadiza (20/60), versátil (1d8)", tipo: "Armas c/c simples" },
                { nombre: "Martillo ligero", coste: "2 po", dano: "1d4 contundente", peso: "2 lb.", propiedades: "Arrojadiza (20/60), ligera", tipo: "Armas c/c simples" },
                { nombre: "Maza", coste: "5 po", dano: "1d6 contundente", peso: "4 lb.", propiedades: "—", tipo: "Armas c/c simples" },
                { nombre: "Clava", coste: "1 pp", dano: "1d4 contundente", peso: "2 lb.", propiedades: "Ligera", tipo: "Armas c/c simples" },
                { nombre: "Arco corto", coste: "25 po", dano: "1d6 perforante", peso: "2 lb.", propiedades: "A 2 manos, munición (80/320)", tipo: "Armas a distancia simples" },
                { nombre: "Ballesta ligera", coste: "25 po", dano: "1d8 perforante", peso: "5 lb.", propiedades: "A 2 manos, munición (80/320), recarga", tipo: "Armas a distancia simples" },
                { nombre: "Dardo", coste: "5 pc", dano: "1d4 perforante", peso: "¼ lb.", propiedades: "Arrojadiza (20/60)", tipo: "Armas a distancia simples" },
                { nombre: "Honda", coste: "1 pp", dano: "1d4 contundente", peso: "—", propiedades: "Munición (30/120)", tipo: "Armas a distancia simples" },
                { nombre: "Alabarda", coste: "20 po", dano: "1d10 cortante", peso: "6 lb.", propiedades: "A 2 manos, alcance, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Atarraga", coste: "10 po", dano: "2d6 contundente", peso: "10 lb", propiedades: "A 2 manos, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Cimitarra", coste: "25 po", dano: "1d6 cortante", peso: "3 lb.", propiedades: "Ligera, sutil", tipo: "Armas c/c marciales" },
                { nombre: "Espada corta", coste: "10 po", dano: "1d6 cortante", peso: "2 lb.", propiedades: "Ligera, sutil", tipo: "Armas c/c marciales" },
                { nombre: "Espada larga", coste: "15 po", dano: "1d8 cortante", peso: "3 lb.", propiedades: "Versátil (1d10)", tipo: "Armas c/c marciales" },
                { nombre: "Espadón", coste: "50 po", dano: "2d6 cortante", peso: "6 lb.", propiedades: "A 2 manos, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Espada ropera", coste: "25 po", dano: "1d8 perforante", peso: "2 lb.", propiedades: "Sutil", tipo: "Armas c/c marciales" },
                { nombre: "Hacha de batalla", coste: "10 po", dano: "1d8 cortante", peso: "4 lb.", propiedades: "Versátil (1d10)", tipo: "Armas c/c marciales" },
                { nombre: "Gran hacha", coste: "30 po", dano: "1d12 cortante", peso: "7 lb.", propiedades: "A 2 manos, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Guja", coste: "20 po", dano: "1d10 cortante", peso: "6 lb.", propiedades: "A 2 manos, alcance, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Lanza de caballería", coste: "10 po", dano: "1d12 perforante", peso: "6 lb.", propiedades: "Alcance, especial", tipo: "Armas c/c marciales" },
                { nombre: "Látigo", coste: "2 po", dano: "1d4 cortante", peso: "3 lb.", propiedades: "Alcance, sutil", tipo: "Armas c/c marciales" },
                { nombre: "Lucero del alba", coste: "15 po", dano: "1d8 perforante", peso: "4 lb.", propiedades: "—", tipo: "Armas c/c marciales" },
                { nombre: "Martillo de guerra", coste: "15 po", dano: "1d8 contundente", peso: "2 lb.", propiedades: "Versátil (1d10)", tipo: "Armas c/c marciales" },
                { nombre: "Mayal", coste: "10 po", dano: "1d8 contundente", peso: "2 lb.", propiedades: "—", tipo: "Armas c/c marciales" },
                { nombre: "Pica", coste: "5 po", dano: "1d10 perforante", peso: "18 lb.", propiedades: "A 2 manos, alcance, pesada", tipo: "Armas c/c marciales" },
                { nombre: "Pica de guerra", coste: "5 po", dano: "1d8 perforante", peso: "2 lb.", propiedades: "—", tipo: "Armas c/c marciales" },
                { nombre: "Tridente", coste: "5 po", dano: "1d6 perforante", peso: "4 lb.", propiedades: "Arrojadiza (20/60), versátil", tipo: "Armas c/c marciales" },
                { nombre: "Arco largo", coste: "50 po", dano: "1d8 perforante", peso: "2 lb.", propiedades: "A 2 manos, munición (150/600), pesada", tipo: "Armas a distancia marciales" },
                { nombre: "Ballesta de mano", coste: "75 po", dano: "1d6 perforante", peso: "3 lb.", propiedades: "Ligera, munición (30/120) recarga", tipo: "Armas a distancia marciales" },
                { nombre: "Ballesta pesada", coste: "50 po", dano: "1d10 perforante", peso: "18 lb.", propiedades: "A 2 manos, munición (100/400), pesada, recarga", tipo: "Armas a distancia marciales" },
                { nombre: "Cerbatana", coste: "10 po", dano: "1 perforante", peso: "1 lb.", propiedades: "Munición(25/100), recarga", tipo: "Armas a distancia marciales" },
                { nombre: "Red", coste: "1 po", dano: "—", peso: "3 lb.", propiedades: "Arrojadiza (5/15), especial", tipo: "Armas a distancia marciales" }
            ],
            "Clases": [
                { 
                    nombre: "Bárbaro", 
                    descripcion: "Estos bárbaros se definen por su rabia: una furia desenfrenada, inagotable e irreflexiva. Más que una mera emoción, su ira es la ferocidad de un depredador acorralado, el implacable rugir de la tormenta, la devastadora agitación del mar. Para el bárbaro, la furia no solo proporciona un frenesí ciego en la batalla, sino también extraordinarios reflejos, resistencia y proezas de fuerza.",
                    creacion_rapida: "Primero, Fuerza debería ser tu puntuación de característica más alta, seguida de Constitución. Segundo, escoge el trasfondo Extranjero.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d12 por nivel de bárbaro.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 12 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe a niveles superiores:</b> 1d12 (o 7) + tu modificador de Constitución por nivel de bárbaro después del primero.</li>
                        <li><b>Competencias — Armadura:</b> Armadura ligera, armadura media, escudos.</li>
                        <li><b>Competencias — Armas:</b> Armas simples, armas marciales.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Fuerza, Constitución.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Atletismo, Intimidación, Naturaleza, Percepción, Supervivencia y Trato de Animales.</li>
                        <li><b>Equipo Inicial:</b> (a) gran hacha o (b) cualquier arma marcial cuerpo a cuerpo. (a) dos hachas de mano o (b) cualquier arma simple. Un equipo de explorador y cuatro jabalinas.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla del Bárbaro</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:520px;">
                        <tr><th>Nivel</th><th>Bon. Comp.</th><th>Rasgos</th><th>Furias</th><th>Daño de Furia</th></tr>
                        <tr><td>1</td><td>+2</td><td>Furia, Defensa Sin Armadura</td><td>2</td><td>+2</td></tr>
                        <tr><td>2</td><td>+2</td><td>Ataque Temerario, Sentido del Peligro</td><td>2</td><td>+2</td></tr>
                        <tr><td>3</td><td>+2</td><td>Senda Primaria</td><td>3</td><td>+2</td></tr>
                        <tr><td>4</td><td>+2</td><td>Mejora de Puntuación de Características</td><td>3</td><td>+2</td></tr>
                        <tr><td>5</td><td>+3</td><td>Ataque Extra, Movimiento Rápido</td><td>3</td><td>+2</td></tr>
                        <tr><td>6</td><td>+3</td><td>Rasgo de Senda Primaria</td><td>4</td><td>+2</td></tr>
                        <tr><td>7</td><td>+3</td><td>Instinto Salvaje</td><td>4</td><td>+2</td></tr>
                        <tr><td>8</td><td>+3</td><td>Mejora de Puntuación de Características</td><td>4</td><td>+2</td></tr>
                        <tr><td>9</td><td>+4</td><td>Crítico Brutal (1 dado)</td><td>4</td><td>+3</td></tr>
                        <tr><td>10</td><td>+4</td><td>Rasgo de Senda Primaria</td><td>4</td><td>+3</td></tr>
                        <tr><td>11</td><td>+4</td><td>Furia Implacable</td><td>4</td><td>+3</td></tr>
                        <tr><td>12</td><td>+4</td><td>Mejora de Puntuación de Características</td><td>5</td><td>+3</td></tr>
                        <tr><td>13</td><td>+5</td><td>Crítico Brutal (2 dados)</td><td>5</td><td>+3</td></tr>
                        <tr><td>14</td><td>+5</td><td>Rasgo de Senda Primaria</td><td>5</td><td>+3</td></tr>
                        <tr><td>15</td><td>+5</td><td>Furia Persistente</td><td>5</td><td>+3</td></tr>
                        <tr><td>16</td><td>+5</td><td>Mejora de Puntuación de Características</td><td>5</td><td>+4</td></tr>
                        <tr><td>17</td><td>+6</td><td>Crítico Brutal (3 dados)</td><td>6</td><td>+4</td></tr>
                        <tr><td>18</td><td>+6</td><td>Fuerza Indómita</td><td>6</td><td>+4</td></tr>
                        <tr><td>19</td><td>+6</td><td>Mejora de Puntuación de Características</td><td>6</td><td>+4</td></tr>
                        <tr><td>20</td><td>+6</td><td>Campeón Primario</td><td>Ilimitado</td><td>+4</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* <b>Daño de Furia:</b> bonificador al daño de ataques c/c con Fuerza mientras estás en furia. <b>Furias:</b> usos disponibles por descanso prolongado.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Furia:</b> En tu turno puedes entrar en furia como acción adicional. Mientras estás en furia y no llevas armadura pesada obtienes: ventaja en pruebas y tiradas de salvación de Fuerza; un bonificador al daño en ataques c/c con Fuerza (ver tabla); y resistencia al daño contundente, perforante y cortante. No puedes lanzar conjuros ni concentrarte en ellos durante la furia. Dura 1 minuto y termina antes si caes inconsciente o si en tu turno no has atacado a ninguna criatura hostil ni recibido daño desde el turno anterior. También puedes cancelarla como acción adicional. Usos por descanso prolongado según la tabla.<br><br><b>Defensa Sin Armadura:</b> Mientras no lleves armadura, tu CA es igual a 10 + modificador de Destreza + modificador de Constitución. Puedes usar un escudo y seguir beneficiándote de este rasgo." },
                        { nivel: 2, rasgos: "<b>Ataque Temerario:</b> Cuando hagas tu primer ataque en tu turno, puedes decidir atacar de forma temeraria. Hacerlo te da ventaja en tiradas de ataque c/c con Fuerza durante ese turno, pero los ataques en tu contra tienen ventaja hasta el inicio de tu siguiente turno.<br><br><b>Sentido del Peligro:</b> Obtienes ventaja en las tiradas de salvación de Destreza contra efectos que puedas ver, como trampas y conjuros. Para beneficiarte no puedes estar ciego, sordo o incapacitado." },
                        { nivel: 3, rasgos: "<b>Senda Primaria:</b> Eliges la Senda del Berserker o la Senda del Guerrero Totémico. Tu elección te proporciona rasgos en el nivel 3 y nuevamente en el 6 y en el 14." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica en 2, o dos en 1 cada una. No puedes superar 20 con este rasgo." },
                        { nivel: 5, rasgos: "<b>Ataque Extra:</b> Puedes atacar dos veces, en lugar de una, cada vez que uses la acción de Atacar en tu turno.<br><br><b>Movimiento Rápido:</b> Tu velocidad se incrementa en 10 pies mientras no estés usando armadura pesada." },
                        { nivel: 6, rasgos: "<b>Rasgo de Senda Primaria:</b> Ver subclase elegida." },
                        { nivel: 7, rasgos: "<b>Instinto Salvaje:</b> Tienes ventaja en las tiradas de iniciativa. Además, si eres sorprendido al principio del combate y no estás incapacitado, puedes actuar normalmente en tu primer turno, pero solo si entras en furia antes de hacer cualquier otra cosa." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Crítico Brutal (1 dado):</b> Puedes tirar un dado de daño adicional cuando determinas el daño de un golpe crítico con un ataque cuerpo a cuerpo." },
                        { nivel: 10, rasgos: "<b>Rasgo de Senda Primaria:</b> Ver subclase elegida." },
                        { nivel: 11, rasgos: "<b>Furia Implacable:</b> Si tus puntos de golpe se reducen a 0 mientras estás en furia y no mueres en el momento, puedes hacer una tirada de salvación de Constitución CD 10. Si tienes éxito, quedas a 1 PG en su lugar. Cada uso posterior aumenta la CD en 5. La CD vuelve a 10 tras un descanso corto o prolongado." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Crítico Brutal (2 dados):</b> Ahora tiras dos dados de daño adicionales en un golpe crítico c/c." },
                        { nivel: 14, rasgos: "<b>Rasgo de Senda Primaria:</b> Ver subclase elegida." },
                        { nivel: 15, rasgos: "<b>Furia Persistente:</b> Tu furia es tan feroz que sólo termina prematuramente si caes inconsciente o decides terminarla voluntariamente." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Crítico Brutal (3 dados):</b> Ahora tiras tres dados de daño adicionales en un golpe crítico c/c." },
                        { nivel: 18, rasgos: "<b>Fuerza Indómita:</b> Si tu puntuación total para una prueba de Fuerza es menor a tu puntuación de Fuerza, puedes usar tu puntuación de Fuerza directamente en lugar del resultado de la tirada." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Campeón Primario:</b> Encarnas el poder del salvajismo. Tus puntuaciones de Fuerza y Constitución se incrementan en 4, y el máximo para esas puntuaciones es ahora 24. Además, tus usos de Furia son ilimitados." }
                    ],
                    subclases_titulo: "Sendas Primarias",
                    subclases: [
                        { 
                            nombre: "Senda del Berserker", 
                            rasgos: `<p>Para algunos bárbaros, la furia es un medio para un fin, que generalmente es la propia violencia. La Senda del Berserker es un camino de furia desatada repleto de sangre. Cuando entras en la furia del berserker te sumerges en el caos de la batalla, sin preocuparte de tu salud o tu bienestar.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Frenesí</h5>
                            <p>Puedes entrar en frenesí cuando estás en furia. Si lo haces, mientras dure tu furia puedes hacer un único ataque cuerpo a cuerpo como acción adicional en cada uno de tus turnos después de entrar en frenesí. Cuando tu furia termina, sufres un nivel de fatiga.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Furia Inconsciente</h5>
                            <p>No puedes ser encantado o asustado mientras estés en furia. Si estás encantado o asustado cuando entras en furia, el efecto queda suspendido durante su duración.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Presencia Intimidante</h5>
                            <p>Puedes usar tu acción para asustar a alguien con tu presencia amenazadora. Elige una criatura que puedas ver a 30 pies (9 metros) o menos. Si puede verte u oírte, debe superar una tirada de salvación de Sabiduría (CD 8 + bonificador de competencia + modificador de Carisma) o quedar asustada hasta el final de su siguiente turno. En turnos posteriores puedes usar tu acción para extender el efecto hasta el final de tu próximo turno. Termina si la criatura acaba su turno fuera de tu línea de visión o a más de 60 pies. Si tiene éxito, no puedes usar este rasgo en ella durante 24 horas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Represalia</h5>
                            <p>Cuando recibes daño de una criatura que está a 5 pies de ti, puedes emplear tu reacción para realizar un ataque cuerpo a cuerpo contra esa criatura.</p>`
                        },
                        { 
                            nombre: "Senda del Guerrero Totémico", 
                            rasgos: `<p>El Camino del Tótem Guerrero es un viaje espiritual en el que los bárbaros aceptan un espíritu animal como guía, protección e inspiración. En la batalla, tu espíritu tótem te llena con poder sobrenatural añadiendo magia a tu furia. La mayoría de las tribus bárbaras vinculan un tótem animal a la familia o clan particular. En casi ningún caso un bárbaro puede tener más de un espíritu totémico, aunque existen excepciones.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Buscador Espiritual</h5>
                            <p>Ganas la habilidad de lanzar los conjuros <i>sentido animal</i> y <i>hablar con los animales</i>, pero solo como rituales.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Espíritu Tótem</h5>
                            <p>Eliges un tótem animal y obtienes sus características. Debes hacer o adquirir un objeto como tótem físico (amuleto u otro adorno) que contenga pelo, plumas, garras, dientes o huesos del animal tótem.</p>
                            <ul style="line-height:1.8; padding-left:20px; margin-top:8px;">
                                <li><b>Oso:</b> Mientras estás en furia tienes resistencia a todos los daños salvo el daño psíquico.</li>
                                <li><b>Águila:</b> Mientras estés en furia y no uses armadura pesada, las demás criaturas tienen desventaja en los ataques de oportunidad contra ti, y puedes usar la acción de Carrera como acción adicional en tu turno.</li>
                                <li><b>Lobo:</b> Mientras estés en furia, tus aliados tienen ventaja en las tiradas de ataque cuerpo a cuerpo contra cualquier criatura hostil a 5 pies de ti.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Aspecto de la Bestia</h5>
                            <p>Ganas un beneficio mágico basado en el tótem animal de tu elección (puedes elegir el mismo que en nivel 3 o uno distinto).</p>
                            <ul style="line-height:1.8; padding-left:20px; margin-top:8px;">
                                <li><b>Oso:</b> Tu capacidad de carga (incluyendo carga máxima y capacidad de levantar y arrastrar) se duplica, y tienes ventaja en pruebas de Fuerza para empujar, levantar, tirar o romper objetos.</li>
                                <li><b>Águila:</b> Puedes ver hasta una milla sin dificultad y discernir detalles a no más de 100 pies. La luz tenue no impone desventaja en tus pruebas de Percepción.</li>
                                <li><b>Lobo:</b> Puedes rastrear criaturas viajando a ritmo rápido, y moverte sigilosamente a ritmo normal.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Caminante Espiritual</h5>
                            <p>Puedes lanzar el conjuro <i>comunión con la naturaleza</i>, pero solo como ritual. Cuando lo haces, una versión espiritual de uno de los animales de tu tótem se te aparece para transmitirte la información pedida.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Sintonía Totémica</h5>
                            <p>Ganas un beneficio mágico basado en el tótem animal de tu elección (puedes elegir el mismo que antes o uno distinto).</p>
                            <ul style="line-height:1.8; padding-left:20px; margin-top:8px;">
                                <li><b>Oso:</b> Mientras estás en furia, las criaturas a 5 pies de ti que te sean hostiles tienen desventaja en tiradas de ataque contra cualquier otro que no seas tú u otro personaje con este rasgo. Un enemigo es inmune si no puede verte u oírte, o si no puede ser asustado.</li>
                                <li><b>Águila:</b> Mientras estás en furia, tienes una velocidad de vuelo igual a tu velocidad de movimiento. Solo funciona en breves intervalos; caes si terminas tu turno en el aire y nada más te mantiene en vuelo.</li>
                                <li><b>Lobo:</b> Mientras estás en furia, puedes usar una acción adicional en tu turno para tumbar a una criatura Grande o más pequeña cuando impactas con un ataque cuerpo a cuerpo.</li>
                            </ul>`
                        }
                    ]
                },
                { 
                    nombre: "Bardo", 
                    descripcion: "Ya sea un erudito, un poeta o un canalla, un bardo teje su magia a través de sus palabras y su música para inspirar a los aliados, desmoralizar a los enemigos, manipular mentes, crear ilusiones e incluso sanar heridas. Los bardos dicen que el multiverso fue nombrado para que existiera, y que los ecos de las primordiales Palabras de Creación todavía resuenan a través del cosmos. La música de los bardos es un intento de arrebatar y emplear esos ecos.",
                    creacion_rapida: "Primero, Carisma debería ser tu puntuación más alta, seguida por Destreza. Segundo, elige el trasfondo Artista. Trucos sugeridos: luces danzantes y burla cruel. Conjuros de nivel 1 recomendados: hechizar persona, detectar magia, palabra sanadora y onda atronadora.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de bardo.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe a niveles superiores:</b> 1d8 (o 5) + tu modificador de Constitución por nivel de bardo después del primero.</li>
                        <li><b>Competencias — Armadura:</b> Armadura ligera.</li>
                        <li><b>Competencias — Armas:</b> Armas simples, ballesta de mano, espada larga, espada ropera, espada corta.</li>
                        <li><b>Competencias — Herramientas:</b> Tres instrumentos musicales de tu elección.</li>
                        <li><b>Tiradas de Salvación:</b> Destreza, Carisma.</li>
                        <li><b>Habilidades:</b> Tres de tu elección.</li>
                        <li><b>Equipo Inicial:</b> (a) espada ropera, (b) espada larga o (c) cualquier arma simple. (a) equipo de diplomático o (b) equipo de actor. (a) una lira o (b) cualquier otro instrumento musical. Armadura de cuero y una daga.</li>
                        <li><b>Característica de Conjuros:</b> Carisma. CD Salvación = 8 + bonificador de competencia + mod. CAR. Modificador de Ataque = bonificador de competencia + mod. CAR.</li>
                        <li><b>Conjuración Ritual:</b> Puedes lanzar cualquier conjuro de bardo que conozcas como ritual si ese conjuro tiene el descriptor ritual.</li>
                        <li><b>Foco Arcano:</b> Puedes usar un instrumento musical como foco arcano para tus conjuros de bardo.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Bardo</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:700px;">
                        <tr><th>Nivel</th><th>Trucos</th><th>Conj. Conocidos</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th><th>Nv5</th><th>Nv6</th><th>Nv7</th><th>Nv8</th><th>Nv9</th></tr>
                        <tr><td>1</td><td>2</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>2</td><td>5</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>2</td><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>3</td><td>7</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>3</td><td>8</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>3</td><td>9</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>3</td><td>10</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>3</td><td>11</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>3</td><td>12</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>4</td><td>14</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>4</td><td>16</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>14</td><td>4</td><td>18</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>15</td><td>4</td><td>19</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>16</td><td>4</td><td>19</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>17</td><td>4</td><td>20</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>18</td><td>4</td><td>22</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>19</td><td>4</td><td>22</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>20</td><td>4</td><td>22</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>2</td><td>1</td><td>1</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* Los espacios de conjuro se recuperan con un descanso prolongado. Al ganar nivel puedes reemplazar un conjuro conocido por otro de la lista de bardo del nivel apropiado.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Lanzamiento de Conjuros:</b> Conoces 2 trucos y 4 conjuros de nivel 1 de la lista de bardo. Carisma es tu característica de conjuros. Puedes lanzar conjuros con la etiqueta ritual sin gastar espacio de conjuro. Puedes usar un instrumento musical como foco arcano.<br><br><b>Inspiración de Bardo:</b> Como acción adicional, eliges una criatura distinta a ti a 60 pies que pueda escucharte. Esa criatura gana un dado de Inspiración de Bardo (d6). Una vez dentro de los siguientes 10 minutos puede tirar ese dado y añadirlo a una prueba de habilidad, tirada de ataque o tirada de salvación (puede decidir usarlo después de ver el d20, pero antes de que el DM declare el resultado). Usos = modificador de Carisma (mínimo 1). Se recuperan en descanso prolongado. El dado escala: d8 al nivel 5, d10 al nivel 10, d12 al nivel 15." },
                        { nivel: 2, rasgos: "<b>Polivalente:</b> Puedes añadir la mitad de tu bonificador de competencia (redondeado hacia abajo) a cualquier prueba de habilidad que realices y en la que no incluyas ya tu bonificador de competencia.<br><br><b>Canción de Descanso:</b> Puedes usar música o una oración reconfortante para ayudar a revitalizar a tus aliados durante un descanso corto. Si tú o cualquier criatura amistosa que escucha tu interpretación recobra PG al final del descanso, esa criatura recupera 1d6 de PG adicionales. Escala: 1d8 al nivel 9, 1d10 al nivel 13, 1d12 al nivel 17." },
                        { nivel: 3, rasgos: "<b>Colegio de Bardo:</b> Eliges el Colegio del Conocimiento o el Colegio del Valor. Tu elección te proporciona rasgos en el nivel 3 y nuevamente en el 6 y en el 14.<br><br><b>Experto:</b> Eliges dos de tus habilidades con competencia. Tu bonificador de competencia se duplica para cualquier prueba de habilidad que realices con ellas. Al nivel 10 eliges otras dos habilidades para ganar este beneficio." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica en 2, o dos en 1 cada una. No puedes superar 20 con este rasgo. Aprendes además 1 truco adicional (total 3 trucos)." },
                        { nivel: 5, rasgos: "<b>Fuente de Inspiración:</b> Recuperas todos tus usos de Inspiración de Bardo cuando terminas un descanso corto o prolongado (antes solo se recuperaban con descanso prolongado).<br><br><b>Inspiración de Bardo:</b> Tu dado de Inspiración pasa a ser d8." },
                        { nivel: 6, rasgos: "<b>Contraoda:</b> Como acción, comienzas una interpretación que dura hasta el final de tu siguiente turno. Durante ese tiempo, tú y cualquier criatura amistosa a 30 pies tienen ventaja en tiradas de salvación para evitar ser asustados o encantados. La criatura debe poder escucharte. La interpretación termina si eres incapacitado, silenciado, o si la terminas voluntariamente (no requiere acción).<br><br><b>Rasgo de Colegio de Bardo:</b> Ver subclase elegida." },
                        { nivel: 7, rasgos: "Nuevos espacios de conjuro de nivel 4 (ver tabla). Puedes aprender conjuros de nivel 4." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Canción de Descanso:</b> El dado adicional pasa a ser 1d8. Nuevos espacios de conjuro de nivel 5." },
                        { nivel: 10, rasgos: "<b>Inspiración de Bardo:</b> Tu dado de Inspiración pasa a ser d10.<br><br><b>Experto:</b> Duplicas tu bonificador de competencia en dos habilidades más.<br><br><b>Secretos Mágicos:</b> Eliges dos conjuros de cualquier clase (incluyendo bardo) de un nivel que puedas lanzar, o trucos. Cuentan como conjuros de bardo y se incluyen en tu total de conjuros conocidos.<br><br>Aprendes también 1 truco adicional (total 4 trucos)." },
                        { nivel: 11, rasgos: "Nuevos espacios de conjuro de nivel 6 (ver tabla)." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Canción de Descanso:</b> El dado adicional pasa a ser 1d10. Nuevos espacios de nivel 7." },
                        { nivel: 14, rasgos: "<b>Secretos Mágicos:</b> Aprendes dos conjuros adicionales de cualquier clase.<br><br><b>Rasgo de Colegio de Bardo:</b> Ver subclase elegida." },
                        { nivel: 15, rasgos: "<b>Inspiración de Bardo:</b> Tu dado de Inspiración pasa a ser d12. Nuevos espacios de nivel 8." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Canción de Descanso:</b> El dado adicional pasa a ser 1d12. Nuevos espacios de nivel 9." },
                        { nivel: 18, rasgos: "<b>Secretos Mágicos:</b> Aprendes dos conjuros adicionales más de cualquier clase." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Inspiración Superior:</b> Cuando tiras iniciativa y no te quedan usos de Inspiración de Bardo, recuperas un uso automáticamente." }
                    ],
                    subclases_titulo: "Colegios de Bardo",
                    subclases: [
                        { 
                            nombre: "Colegio del Conocimiento", 
                            rasgos: `<p>Los bardos del Colegio del Conocimiento conocen algo sobre la mayoría de las cosas, coleccionando fragmentos de saber de fuentes tan diversas como tomos de eruditos y cuentos de aldeanos. Estos bardos usan sus dones para mantener a sus audiencias fascinadas y cuestionándose todo lo que tienen por cierto. Su lealtad está depositada en la búsqueda de la belleza y la verdad, no en el vasallaje de un monarca o en seguir las doctrinas de una deidad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Competencias Adicionales</h5>
                            <p>Ganas competencia con tres habilidades de tu elección.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Palabras Hirientes</h5>
                            <p>Aprendes a usar tu ingenio para distraer, confundir y minar la confianza de otros. Cuando una criatura que puedes ver a 60 pies realiza una tirada de ataque, prueba de habilidad o tirada de daño, puedes usar tu reacción para gastar uno de tus usos de Inspiración de Bardo: tiras el dado de Inspiración y restas el resultado a la tirada de la criatura. Puedes decidir usarlo después de que la criatura haya realizado su tirada, pero antes de que el DM determine el resultado. La criatura es inmune si no puede oírte o es inmune al encantamiento.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Secretos Mágicos Adicionales</h5>
                            <p>Aprendes dos conjuros de cualquier clase de un nivel que puedas lanzar, o trucos. Cuentan como conjuros de bardo para ti, pero no se incluyen en la columna de Conjuros Conocidos de la tabla Bardo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Habilidad Incomparable</h5>
                            <p>Cuando haces una prueba de habilidad, puedes gastar un uso de Inspiración de Bardo: tiras el dado y añades el resultado a tu prueba. Puedes esperar a ver el resultado de la tirada antes de decidir usarlo, pero debes hacerlo antes de que el DM diga si la prueba tuvo éxito o no.</p>`
                        },
                        { 
                            nombre: "Colegio del Valor", 
                            rasgos: `<p>Los bardos del Colegio del Valor son osados poetas cuyos cuentos mantienen viva la memoria de los grandes héroes del pasado, inspirando a una nueva generación. Se reúnen en salones de banquetes o alrededor de grandes fogatas para cantar hazañas. Viajan por el mundo para presenciar grandes eventos de primera mano y asegurarse de que su recuerdo no se pierda.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Competencias Adicionales</h5>
                            <p>Ganas competencia con armadura media, escudos y armas marciales.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Inspiración de Combate</h5>
                            <p>Aprendes a inspirar a otros en la batalla. Una criatura que tiene un dado de Inspiración de Bardo tuyo puede tirarlo y añadirlo a una tirada de daño. Alternativamente, cuando se realiza una tirada de ataque contra la criatura, ésta puede usar su reacción para tirar el dado de Inspiración y añadir el resultado a su CA contra ese ataque, después de ver la tirada pero antes de saber si acertó.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Ataque Adicional</h5>
                            <p>Puedes atacar dos veces en lugar de una cuando realizas la acción de Atacar en tu turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Magia de Batalla</h5>
                            <p>Has dominado el arte de entretejer el lanzamiento de conjuros con el combate con armas. Cuando usas tu acción para lanzar un conjuro de bardo, puedes realizar un ataque con arma como acción adicional.</p>`
                        }
                     ]
                },
                { 
                    nombre: "Brujo", 
                    descripcion: "Buscador de conocimientos que forja un pacto con entidades místicas de otros mundos. La magia del brujo es otorgada por su patrón a cambio de servicios y lealtad.",
                    creacion_rapida: "Carisma debe ser tu puntuación más alta, luego Constitución. Trasfondo Charlatán. Trucos recomendados: Estallido Arcano y Toque Gélido. Conjuros iniciales: Rayo de Dolencia y Virote Encantado.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de brujo.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Competencias:</b> Armadura ligera, todas las armas simples.</li>
                        <li><b>Tiradas de Salvación:</b> Sabiduría, Carisma.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Arcano, Engaño, Historia, Intimidación, Investigación, Naturaleza y Religión.</li>
                        <li><b>Equipo Inicial:</b> (a) ballesta ligera y 20 virotes o (b) arma simple. (a) bolsa de componentes o (b) foco arcano. (a) equipo de erudito o (b) equipo de saqueador. Armadura de cuero, arma simple y dos dagas.</li>
                        <li><b>Espacios de Conjuro:</b> Todos tus espacios son del mismo nivel (el más alto disponible). Se recuperan con un descanso <b>corto o prolongado</b>.</li>
                        <li><b>Característica de Conjuros:</b> Carisma. CD Salvación = 8 + competencia + mod. CAR.</li>
                    </ul>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Patrón de Otro Mundo:</b> Eliges La Archihada, El Diablo o El Gran Antiguo. Cada patrón te otorga rasgos a nivel 1, 6, 10 y 14, además de su lista de Conjuros Expandidos.<br><b>Magia de Pacto:</b> 1 espacio de conjuro de nivel 1. Se recupera en descanso corto o prolongado." },
                        { nivel: 2, rasgos: "<b>Invocaciones Sobrenaturales:</b> Ganas 2 invocaciones de tu elección. Fragmentos de conocimiento prohibido que te otorgan habilidades permanentes (ver sección Invocaciones al final)." },
                        { nivel: 3, rasgos: "<b>Don del Pacto:</b> Elige uno de tres dones permanentes:<br>— <b>Pacto de la Cadena:</b> Aprendes <i>encontrar familiar</i> como ritual. Tu familiar puede ser diablillo, pseudodragón, quasit o duende. Puedes sacrificar un ataque tuyo para que tu familiar ataque.<br>— <b>Pacto de la Espada:</b> Creas un arma mágica en tu mano vacía. Eres competente con ella. Desaparece si se aleja más de 5 pies por un minuto.<br>— <b>Pacto del Tomo:</b> Tu patrón te da el <i>Libro de las Sombras</i>. Contiene 3 trucos de cualquier clase (no cuentan en tu límite). Puedes añadir conjuros rituales al libro.<br><b>Espacios de Conjuro:</b> 2 espacios de nivel 2." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Característica.</b><br><b>Espacios de Conjuro:</b> 2 espacios de nivel 2." },
                        { nivel: 5, rasgos: "<b>Espacios de Conjuro:</b> 2 espacios de nivel 3. Puedes aprender conjuros de nivel 3." },
                        { nivel: 6, rasgos: "<b>Rasgo de Patrón (nivel 6):</b> Ver subclase correspondiente.<br><b>Espacios de Conjuro:</b> 2 espacios de nivel 3." },
                        { nivel: 7, rasgos: "<b>Espacios de Conjuro:</b> 2 espacios de nivel 4. Puedes aprender conjuros de nivel 4." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Característica.</b><br><b>Espacios de Conjuro:</b> 2 espacios de nivel 4." },
                        { nivel: 9, rasgos: "<b>Espacios de Conjuro:</b> 2 espacios de nivel 5. Puedes aprender conjuros de nivel 5." },
                        { nivel: 10, rasgos: "<b>Rasgo de Patrón (nivel 10):</b> Ver subclase correspondiente.<br><b>Espacios de Conjuro:</b> 2 espacios de nivel 5." },
                        { nivel: 11, rasgos: "<b>Arcanum Místico (nivel 6):</b> Tu patrón te concede un conjuro de nivel 6 de la lista de brujo. Lo lanzas UNA vez sin gastar espacio (se recarga con descanso prolongado).<br><b>Espacios de Conjuro:</b> 3 espacios de nivel 5." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 13, rasgos: "<b>Arcanum Místico (nivel 7):</b> Aprendes otro conjuro, ahora de nivel 7. Puedes lanzarlo una vez sin gastar espacio." },
                        { nivel: 14, rasgos: "<b>Rasgo de Patrón (nivel 14):</b> Ver subclase correspondiente." },
                        { nivel: 15, rasgos: "<b>Arcanum Místico (nivel 8):</b> Aprendes un conjuro de nivel 8 lanzable una vez sin gastar espacio.<br><b>Espacios de Conjuro:</b> 3 espacios de nivel 5." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 17, rasgos: "<b>Arcanum Místico (nivel 9):</b> Aprendes un conjuro de nivel 9 lanzable una vez sin gastar espacio.<br><b>Espacios de Conjuro:</b> 4 espacios de nivel 5." },
                        { nivel: 18, rasgos: "<b>Espacios de Conjuro:</b> 4 espacios de nivel 5." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 20, rasgos: "<b>Maestro Arcano:</b> Puedes suplicar a tu patrón (1 minuto) para recuperar TODOS tus espacios de conjuro gastados. Solo puedes hacerlo una vez por descanso prolongado." }
                    ],
                    subclases_titulo: "Patrones de Otro Mundo",
                    subclases: [
                        { 
                            nombre: "La Archihada", 
                            rasgos: `<p>Tu patrón es un noble entre los feéricos, una criatura de leyenda poseedora de secretos olvidados antes de que las razas mortales nacieran. Sus motivaciones son inescrutables y a veces caprichosas. Entre los posibles patrones: el Príncipe del Frío, la Reina del Aire y la Oscuridad (soberana de la Corte Crepuscular), Titania de la Corte del Verano, Oberon el Señor Verde, Hyrsam el Príncipe de los Tontos, y antiguas brujas.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros Expandidos de la Archihada</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>fuego feérico, dormir</td></tr>
                            <tr><td>2</td><td>calmar emociones, fuerza fantasmal</td></tr>
                            <tr><td>3</td><td>intermitencia, crecimiento vegetal</td></tr>
                            <tr><td>4</td><td>dominar bestia, invisibilidad mayor</td></tr>
                            <tr><td>5</td><td>dominar persona, similitud</td></tr></table>
                            <b>Nivel 1 — Presencia Feérica:</b> Como acción, fuerzas a criaturas en un cubo de 10 pies centrado en ti a hacer salvación de SAB. Las que fallen quedan hechizadas o asustadas por ti hasta el final de tu siguiente turno. Se recarga en descanso corto o prolongado.<br><br>
                            <b>Nivel 6 — Escape Brumoso:</b> Cuando recibes daño, usas reacción para volverte invisible y teletransportarte hasta 60 pies a un espacio visible. Permaneces invisible hasta el inicio de tu siguiente turno (termina si atacas o lanzas un conjuro). Se recarga en descanso corto o prolongado.<br><br>
                            <b>Nivel 10 — Defensas Seductoras:</b> Eres inmune a ser encantado. Cuando alguien intenta encantarte, puedes usar reacción para devolver el hechizo: la criatura debe superar una salvación de SAB o queda encantada por ti durante un minuto o hasta recibir daño.<br><br>
                            <b>Nivel 14 — Delirio Oscuro:</b> Como acción, sumes a una criatura a 60 pies en un reino ilusorio. Hace salvación de SAB o queda hechizada/asustada por ti durante un minuto (concentración). Solo ve a sí misma, a ti y la ilusión. Termina si recibe daño. Se recarga en descanso corto o prolongado.`
                        },
                        { 
                            nombre: "El Diablo", 
                            rasgos: `<p>Has realizado un pacto con un diablo de los planos inferiores, un ser cuyas metas son malvadas (incluso si peleas contra ellas). Los diablos con suficiente poder para firmar pactos incluyen señores demoníacos como Demogorgon, Orcus, y archidemonios como Asmodeo, Dispater, Mefistófeles y Belial, así como diablos del foso y balors poderosos.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros Expandidos del Diablo</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>manos ardientes, orden imperiosa</td></tr>
                            <tr><td>2</td><td>ceguera/sordera, rayo abrasador</td></tr>
                            <tr><td>3</td><td>bola de fuego, nube apestosa</td></tr>
                            <tr><td>4</td><td>escudo de fuego, muro de fuego</td></tr>
                            <tr><td>5</td><td>descarga flamígera, sacralizar</td></tr></table>
                            <b>Nivel 1 — Bendición del Oscuro:</b> Cuando reduces los PG de una criatura hostil a 0, ganas PG temporales equivalentes a tu modificador de CAR + tu nivel de brujo (mínimo 1).<br><br>
                            <b>Nivel 6 — La Suerte del Oscuro:</b> Cuando haces una prueba de habilidad o salvación, puedes usar este rasgo para sumar 1d10 al resultado (puedes decidir después de ver el dado inicial, pero antes de que surtan efecto). Se recarga en descanso corto o prolongado.<br><br>
                            <b>Nivel 10 — Resistencia Diabólica:</b> Al finalizar un descanso corto, eliges un tipo de daño. Ganas resistencia a ese tipo hasta que uses este rasgo de nuevo. Las armas mágicas o de plata ignoran esta resistencia.<br><br>
                            <b>Nivel 14 — Lanzar a través del Infierno:</b> Cuando golpeas a una criatura con un ataque, puedes usar este rasgo para transportarla instantáneamente a través de los planos inferiores. Desaparece hasta el final de tu siguiente turno. Si no es un diablo, recibe 10d10 de daño psíquico. Se recarga en descanso prolongado.`
                        },
                        { 
                            nombre: "El Gran Antiguo", 
                            rasgos: `<p>Tu patrón es una misteriosa entidad cuya naturaleza es totalmente ajena al tejido de la realidad. Podría venir del Reino Lejano o ser uno de los dioses antiguos conocidos sólo en leyendas. Sus motivos son incomprensibles para los mortales. Podría incluso no ser consciente de tu existencia. Entre los posibles patrones: Ghaunadar (El Que Se Esconde), Tharizdun (el Dios Encadenado), Dendar (la Serpiente Nocturna), Zargon (el Retornante) y el Gran Cthulhu.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros Expandidos del Gran Antiguo</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>susurros disonantes, terribles carcajadas de Tasha</td></tr>
                            <tr><td>2</td><td>detectar pensamientos, fuerza fantasmal</td></tr>
                            <tr><td>3</td><td>clarividencia, recado</td></tr>
                            <tr><td>4</td><td>dominar bestia, tentáculos negros de Evard</td></tr>
                            <tr><td>5</td><td>dominar persona, telequinesia</td></tr></table>
                            <b>Nivel 1 — Mente Despierta:</b> Puedes comunicarte telepáticamente con cualquier criatura que puedas ver en un rango de 30 pies. No necesitas compartir idioma, pero la criatura debe comprender al menos uno.<br><br>
                            <b>Nivel 6 — Guardia Entrópica:</b> Cuando una criatura hace una tirada de ataque contra ti, puedes usar tu reacción para imponerle desventaja en esa tirada. Si el ataque falla, tu próxima tirada de ataque contra esa criatura tiene ventaja (si la realizas antes del final de tu siguiente turno). Se recarga en descanso corto o prolongado.<br><br>
                            <b>Nivel 10 — Escudo de Pensamientos:</b> Tus pensamientos no pueden ser leídos mediante telepatía u otros medios (a menos que lo permitas). Ganas resistencia al daño psíquico. Además, cuando una criatura te hace daño psíquico, esa criatura recibe la misma cantidad de daño.<br><br>
                            <b>Nivel 14 — Crear Esclavo:</b> Como acción, tocas a un humanoide incapacitado. Queda encantado por ti hasta que se le aplique <i>quitar maldición</i>, la condición sea eliminada, o uses este rasgo de nuevo. Puedes comunicarte telepáticamente con él mientras estén en el mismo plano.`
                        }
                    ],
                    invocaciones: `
                        <h4 style="color:var(--primary-color); margin-top:25px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Invocaciones Sobrenaturales</h4>
                        <p style="color:var(--text-secondary); margin-bottom:15px;">A partir del nivel 2, el brujo desentierra fragmentos de conocimiento prohibido que le imbuyen con habilidades mágicas permanentes. Ganas 2 al nivel 2, y más a medida que subes de nivel. Si una invocación tiene prerrequisito, debes cumplirlo para aprenderla.</p>
                        <div class="card-grid">
                            <div class="card"><h3>Estallido Agonizante</h3><span class="tag">Prerreq: Estallido Arcano</span><p>Cuando lanzas <i>estallido arcano</i>, añades tu modificador de CAR al daño al golpear.</p></div>
                            <div class="card"><h3>Estallido Repulsor</h3><span class="tag">Prerreq: Estallido Arcano</span><p>Cuando golpeas con <i>estallido arcano</i>, puedes empujar al objetivo 10 pies lejos de ti en línea recta.</p></div>
                            <div class="card"><h3>Lanza Arcana</h3><span class="tag">Prerreq: Estallido Arcano</span><p>Cuando lanzas <i>estallido arcano</i>, su rango aumenta a 300 pies (91,5 metros).</p></div>
                            <div class="card"><h3>Armadura de Sombras</h3><p>Puedes lanzar <i>armadura de mago</i> sobre ti mismo a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Vista del Diablo</h3><p>Puedes ver normalmente en la oscuridad (mágica o no) a una distancia de 120 pies (36 metros).</p></div>
                            <div class="card"><h3>Vista Arcana</h3><p>Puedes lanzar <i>detectar magia</i> a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Ojos del Guardián de las Runas</h3><p>Puedes leer todas las escrituras y lenguajes conocidos y desconocidos.</p></div>
                            <div class="card"><h3>Máscara de Muchos Rostros</h3><p>Puedes lanzar <i>disfrazarse</i> a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Visiones Brumosas</h3><p>Puedes lanzar <i>imagen silenciosa</i> a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Influencia Seductora</h3><p>Ganas competencia en las habilidades Engaño y Persuasión.</p></div>
                            <div class="card"><h3>Lengua de las Bestias</h3><p>Puedes lanzar <i>hablar con los animales</i> a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Mirada de Dos Mentes</h3><p>Puedes tocar a un humanoide dispuesto y percibir a través de sus sentidos hasta el final de tu siguiente turno. Puedes extender esta conexión con tu acción cada turno.</p></div>
                            <div class="card"><h3>Vigor Diabólico</h3><p>Puedes lanzar <i>vida falsa</i> sobre ti mismo a voluntad como conjuro de nivel 1, sin gastar espacios ni componentes materiales.</p></div>
                            <div class="card"><h3>Libro de los Secretos Antiguos</h3><span class="tag">Prerreq: Pacto del Tomo</span><p>Puedes inscribir rituales en tu Libro de las Sombras. Elige 2 conjuros de nivel 1 con descriptor ritual de cualquier clase. Puedes añadir más durante tus aventuras.</p></div>
                            <div class="card"><h3>Voz del Amo de la Cadena</h3><span class="tag">Prerreq: Pacto de la Cadena</span><p>Puedes comunicarte telepáticamente con tu familiar y percibir a través de sus sentidos (mismo plano). También puedes hablar a través de él con tu propia voz.</p></div>
                            <div class="card"><h3>Paso Ascendente</h3><span class="tag">Prerreq: Nivel 9</span><p>Puedes lanzar <i>levitar</i> sobre ti mismo a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Salto de Otro Mundo</h3><span class="tag">Prerreq: Nivel 9</span><p>Puedes lanzar <i>salto</i> sobre ti mismo a voluntad, sin gastar espacio de conjuro ni componentes materiales.</p></div>
                            <div class="card"><h3>Sirvientes del Caos</h3><span class="tag">Prerreq: Nivel 9</span><p>Puedes lanzar <i>invocar elemental</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Susurros de la Tumba</h3><span class="tag">Prerreq: Nivel 9</span><p>Puedes lanzar <i>hablar con los muertos</i> a voluntad, sin gastar espacio de conjuro.</p></div>
                            <div class="card"><h3>Uno con las Sombras</h3><span class="tag">Prerreq: Nivel 5</span><p>Cuando estés en área de luz tenue u oscuridad, puedes usar tu acción para volverte invisible hasta que te muevas o uses una acción o reacción.</p></div>
                            <div class="card"><h3>Señal de Mal Agüero</h3><span class="tag">Prerreq: Nivel 5</span><p>Puedes lanzar <i>maldición</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Sumir la Mente</h3><span class="tag">Prerreq: Nivel 5</span><p>Puedes lanzar <i>ralentizar</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Ladrón de los Cinco Destinos</h3><span class="tag">Prerreq: Nivel 5</span><p>Puedes lanzar <i>perdición</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Espada Sedienta</h3><span class="tag">Prerreq: Nivel 5, Pacto de la Espada</span><p>Puedes atacar con tu arma de pacto dos veces en lugar de una cuando usas la acción de Atacar en tu turno.</p></div>
                            <div class="card"><h3>Susurros Encantadores</h3><span class="tag">Prerreq: Nivel 7</span><p>Puedes lanzar <i>compulsión</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Palabra Pavorosa</h3><span class="tag">Prerreq: Nivel 7</span><p>Puedes lanzar <i>confusión</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Escultor de Carne</h3><span class="tag">Prerreq: Nivel 7</span><p>Puedes lanzar <i>polimorfar</i> una vez usando un espacio de conjuro de brujo. Se recarga en descanso prolongado.</p></div>
                            <div class="card"><h3>Chupavidas</h3><span class="tag">Prerreq: Nivel 12, Pacto de la Espada</span><p>Cuando golpeas a una criatura con tu arma de pacto, recibe daño necrótico adicional igual a tu modificador de CAR (mínimo 1).</p></div>
                            <div class="card"><h3>Cadenas de Carceri</h3><span class="tag">Prerreq: Nivel 15, Pacto de la Cadena</span><p>Puedes lanzar <i>inmovilizar monstruo</i> a voluntad (apuntando a un diablo, celestial o elemental) sin gastar espacio. Una vez por descanso prolongado por criatura.</p></div>
                            <div class="card"><h3>Maestro de Innumerables Formas</h3><span class="tag">Prerreq: Nivel 15</span><p>Puedes lanzar <i>alterar el propio aspecto</i> a voluntad, sin gastar espacio de conjuro.</p></div>
                            <div class="card"><h3>Visión de Reinos Distantes</h3><span class="tag">Prerreq: Nivel 15</span><p>Puedes lanzar <i>ojo arcano</i> a voluntad, sin gastar espacio de conjuro.</p></div>
                            <div class="card"><h3>Vista de Bruja</h3><span class="tag">Prerreq: Nivel 15</span><p>Puedes ver la verdadera forma de cualquier cambiaformas o criatura disfrazada con magia de ilusión/transmutación, si está en tu línea de visión a 30 pies.</p></div>
                        </div>`
                },
                { 
                    nombre: "Clérigo", 
                    descripcion: "Intermediario entre el mundo mortal y los planos divinos. Los clérigos canalizan el poder de su deidad para sanar aliados, destruir enemigos y hacer cumplir la voluntad de los dioses.",
                    creacion_rapida: "Sabiduría debe ser tu puntuación más alta, seguida de Fuerza o Constitución. Trasfondo Acólito. Trucos sugeridos: Daño Sagrado, Luz, Taumaturgia.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de clérigo.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Competencias:</b> Armadura ligera, armadura media, escudos, todas las armas simples.</li>
                        <li><b>Tiradas de Salvación:</b> Sabiduría, Carisma.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Historia, Medicina, Perspicacia, Persuasión y Religión.</li>
                        <li><b>Equipo Inicial:</b> (a) maza o (b) martillo de guerra. (a) cota de escamas, (b) armadura de cuero, o (c) cota de malla. (a) ballesta ligera o (b) arma simple. Escudo, símbolo sagrado y (a) equipo de sacerdote o (b) equipo explorador.</li>
                        <li><b>Lanzamiento de Conjuros:</b> Sabiduría es tu característica. Preparas cada día SAB + nivel clérigo conjuros (mínimo 1). Recuperas espacios en descanso prolongado. Puedes lanzar rituales si los tienes preparados.</li>
                        <li><b>Conjuros de Dominio:</b> Siempre preparados, no cuentan en tu límite diario. Si no están en la lista de clérigo, igual cuentan como conjuros de clérigo.</li>
                    </ul>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Lanzamiento de Conjuros:</b> Conoces 3 trucos. Preparas SAB + nivel clérigo conjuros al día. CD Salvación = 8 + competencia + mod. SAB.<br><b>Dominio Divino:</b> Eliges tu dominio (Conocimiento, Vida, Luz, Naturaleza, Tempestad, Superchería o Guerra). Recibes sus conjuros de dominio y rasgos de nivel 1." },
                        { nivel: 2, rasgos: "<b>Canalizar Divinidad (1/descanso):</b> Usas energía divina directa de tu deidad. Comienzas con dos efectos: <b>Expulsar Muertos Vivientes</b> (los muertos vivientes en 30 pies hacen salvación de SAB o quedan expulsados 1 minuto) y el efecto de tu Dominio.<br>Ganas un efecto adicional a nivel 6 del dominio." },
                        { nivel: 3, rasgos: "Nuevos espacios de conjuro. Puedes preparar conjuros de nivel 2." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 5, rasgos: "<b>Destruir Muertos Vivientes (VD ½ o menor):</b> Cuando un muerto viviente falla su salvación contra Expulsar, se destruye instantáneamente si su VD es ½ o menor.<br>Nuevos espacios de nivel 3." },
                        { nivel: 6, rasgos: "<b>Canalizar Divinidad (2/descanso).</b><br><b>Rasgo de Dominio (nivel 6).</b>" },
                        { nivel: 7, rasgos: "Nuevos espacios de conjuro de nivel 4." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Característica.</b><br><b>Destruir Muertos Vivientes (VD 1 o menor).</b><br><b>Rasgo de Dominio (nivel 8):</b> Cada dominio otorga Golpe Divino (1d8 extra de un tipo especial de daño) o Lanzamiento de Conjuros Potente (añades SAB al daño de trucos)." },
                        { nivel: 9, rasgos: "Nuevos espacios de conjuro de nivel 5." },
                        { nivel: 10, rasgos: "<b>Intervención Divina:</b> Como acción, imploras la ayuda de tu deidad. Tiras dado porcentual; si sacas igual o menos que tu nivel de clérigo, la deidad interviene (el DM decide cómo). Si funciona, no puedes usarlo en 7 días. Si no, puedes intentarlo tras un descanso prolongado." },
                        { nivel: 11, rasgos: "<b>Destruir Muertos Vivientes (VD 2 o menor).</b><br>Nuevos espacios de nivel 6." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 13, rasgos: "Nuevos espacios de nivel 7." },
                        { nivel: 14, rasgos: "<b>Destruir Muertos Vivientes (VD 3 o menor).</b><br>Golpe Divino mejora a <b>2d8</b> de daño adicional (según dominio)." },
                        { nivel: 15, rasgos: "Nuevos espacios de nivel 8." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 17, rasgos: "<b>Destruir Muertos Vivientes (VD 4 o menor).</b><br><b>Rasgo de Dominio (nivel 17):</b> Cada dominio otorga su rasgo culminante.<br>Nuevos espacios de nivel 9." },
                        { nivel: 18, rasgos: "<b>Canalizar Divinidad (3/descanso).</b>" },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Característica.</b>" },
                        { nivel: 20, rasgos: "<b>Intervención Divina (automática):</b> Tu llamada para la intervención divina tiene éxito automáticamente. No se necesita tirada." }
                    ],
                    subclases_titulo: "Dominios Divinos",
                    subclases: [
                        {
                            nombre: "Dominio del Conocimiento",
                            rasgos: `<p>Los dioses del conocimiento (Oghma, Boccob, Gilean, Aureon, Thoth) valoran el aprendizaje y el entendimiento sobre todo. Sus clérigos estudian tradiciones esotéricas, coleccionan tomos y exploran lugares de poder.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>orden imperiosa, identificar</td></tr>
                            <tr><td>3</td><td>augurio, sugestión</td></tr>
                            <tr><td>5</td><td>indetectabilidad, hablar con los muertos</td></tr>
                            <tr><td>7</td><td>ojo arcano, confusión</td></tr>
                            <tr><td>9</td><td>conocimiento de leyendas, escudriñamiento</td></tr></table>
                            <b>Nivel 1 — Bendiciones del Conocimiento:</b> Aprendes 2 idiomas. Ganas competencia en 2 habilidades entre Arcano, Historia, Naturaleza o Religión. Tu bonificador de competencia se duplica para esas habilidades.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Conocimiento de las Edades:</b> Como acción, eliges una habilidad o herramienta. Durante 10 minutos tienes competencia en ella.<br><br>
                            <b>Nivel 6 — Canalizar Divinidad: Leer los Pensamientos:</b> Como acción, eliges una criatura a 60 pies. Hace salvación de SAB. Si falla, lees sus pensamientos superficiales durante 1 minuto. Puedes usar tu acción para terminar el efecto y lanzar <i>sugestión</i> sobre ella sin gastar espacio (falla automáticamente la salvación).<br><br>
                            <b>Nivel 8 — Lanzamiento de Conjuros Potente:</b> Añades tu modificador de SAB al daño de cualquier truco de clérigo.<br><br>
                            <b>Nivel 17 — Visión del Pasado:</b> Meditando durante minutos (hasta tu puntuación de SAB), recibes visiones de eventos recientes ligados a un objeto que sostienes (<i>Lectura de Objeto</i>) o del área que te rodea (<i>Lectura de Área</i>, cubo de 50 pies, retrocediendo días = tu puntuación de SAB). Se recarga en descanso corto o prolongado.`
                        },
                        {
                            nombre: "Dominio de la Guerra",
                            rasgos: `<p>Los dioses de la guerra abarcan tanto campeones del honor (Torm, Heironeous, Kiri-Jolith) como dioses de la destrucción (Erythnul, Gruumsh, Ares) y de la conquista (Bane, Hextor). Algunos, como Tempus o Nuada, son más neutrales, apoyando a guerreros en cualquier bando.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>favor divino, escudo de la fe</td></tr>
                            <tr><td>3</td><td>arma mágica, arma espiritual</td></tr>
                            <tr><td>5</td><td>manto del cruzado, espíritus guardianes</td></tr>
                            <tr><td>7</td><td>libertad de movimiento, piel pétrea</td></tr>
                            <tr><td>9</td><td>descarga flamígera, inmovilizar monstruo</td></tr></table>
                            <b>Nivel 1 — Competencias Adicionales:</b> Ganas competencia con armas marciales y armadura pesada.<br><br>
                            <b>Nivel 1 — Clérigo de Guerra:</b> Cuando usas la acción de Atacar, puedes hacer un ataque adicional como acción adicional. Usos = modificador de SAB (mínimo 1). Se recargan en descanso prolongado.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Impacto Guiado:</b> Cuando realizas una tirada de ataque, puedes usar Canalizar Divinidad para añadir +10 a la tirada. Decides después de ver el dado, pero antes de saber si acierta.<br><br>
                            <b>Nivel 6 — Canalizar Divinidad: Bendición del Dios de la Guerra:</b> Cuando una criatura a 30 pies hace una tirada de ataque, puedes usar tu reacción y Canalizar Divinidad para darle +10 a esa tirada. Decides después de ver el dado, antes del resultado.<br><br>
                            <b>Nivel 8 — Golpe Divino:</b> Una vez por turno al golpear con un arma, causas 1d8 adicional del mismo tipo de daño del arma. Aumenta a 2d8 al nivel 14.<br><br>
                            <b>Nivel 17 — Avatar de Batalla:</b> Ganas resistencia al daño contundente, perforante y cortante de armas no mágicas.`
                        },
                        {
                            nombre: "Dominio de la Luz",
                            rasgos: `<p>Los dioses de la luz (Helm, Lathander, Pholtus, Branchala, la Llama Plateada, Belenus, Apolo, Re-Horakhty) promueven el renacimiento, la verdad, la vigilancia y la belleza. Sus clérigos son almas iluminadas que persiguen las mentiras y disipan la oscuridad.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>manos ardientes, fuego feérico</td></tr>
                            <tr><td>3</td><td>esfera flamígera, rayo abrasador</td></tr>
                            <tr><td>5</td><td>luz del día, bola de fuego</td></tr>
                            <tr><td>7</td><td>guardián de la fe, muro de fuego</td></tr>
                            <tr><td>9</td><td>descarga flamígera, escudriñamiento</td></tr></table>
                            <b>Nivel 1 — Truco Adicional:</b> Aprendes el truco <i>luz</i> si aún no lo conocías.<br><br>
                            <b>Nivel 1 — Fulgor Protector:</b> Cuando una criatura a 30 pies de ti te ataca (o ataca a otro a 30 pies a partir del nivel 6), puedes usar tu reacción para imponer desventaja en esa tirada de ataque.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Resplandor del Alba:</b> Como acción, alzas tu símbolo sagrado. Disipar cualquier oscuridad mágica a 30 pies. Cada criatura hostil a 30 pies hace salvación de CON: recibe daño radiante igual a 2d10 + tu nivel de clérigo si falla, o la mitad si supera. Las criaturas con cobertura total quedan inmunes.<br><br>
                            <b>Nivel 6 — Fulgor Mejorado:</b> También puedes usar Fulgor Protector cuando una criatura a 30 pies de ti ataque a <i>otra</i> criatura (no solo a ti).<br><br>
                            <b>Nivel 8 — Lanzamiento de Conjuros Potente:</b> Añades tu modificador de SAB al daño de cualquier truco de clérigo.<br><br>
                            <b>Nivel 17 — Corona de Luz:</b> Como acción, activas un aura de luz solar (1 minuto o hasta desactivarla). Emites luz brillante a 60 pies y tenue a 30 pies adicionales. Tus enemigos en luz brillante tienen desventaja en salvaciones contra conjuros de daño radiante o de fuego.`
                        },
                        {
                            nombre: "Dominio de la Naturaleza",
                            rasgos: `<p>Los dioses de la naturaleza son tan variados como el mundo natural: desde los inescrutables dioses del bosque profundo (Silvanus, Obad-Hai, Chislev, Balinor, Pan) hasta deidades amistosas de arboledas y primaveras (Eldath). Sus clérigos cazan monstruosidades, bendicen cosechas y protegen los bosques.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>amistad con los animales, hablar con los animales</td></tr>
                            <tr><td>3</td><td>piel de robliza, brotar de espigas</td></tr>
                            <tr><td>5</td><td>crecimiento vegetal, muro de aire</td></tr>
                            <tr><td>7</td><td>dominar bestia, enredaderas agarradoras</td></tr>
                            <tr><td>9</td><td>plaga de insectos, zancada arbórea</td></tr></table>
                            <b>Nivel 1 — Acólito de la Naturaleza:</b> Aprendes un truco de druida a tu elección. Además, ganas competencia en una habilidad entre Trato con Animales, Naturaleza o Supervivencia.<br><br>
                            <b>Nivel 1 — Competencia Adicional:</b> Ganas competencia con armadura pesada.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Hechizar Animales y Plantas:</b> Como acción, muestras tu símbolo sagrado. Cada bestia o planta que te vea a 30 pies hace salvación de SAB. Si falla, queda encantada durante 1 minuto o hasta recibir daño (amistosa hacia ti y tus aliados designados).<br><br>
                            <b>Nivel 6 — Amortiguar Elementos:</b> Cuando tú o una criatura a 30 pies recibís daño de ácido, frío, fuego, relámpago o trueno, puedes usar tu reacción para dar resistencia a ese daño a esa criatura.<br><br>
                            <b>Nivel 8 — Golpe Divino:</b> Una vez por turno al golpear con un arma, causas 1d8 adicional de daño de frío, fuego o eléctrico (a tu elección). Aumenta a 2d8 al nivel 14.<br><br>
                            <b>Nivel 17 — Maestro de la Naturaleza:</b> Puedes usar acción adicional para ordenar verbalmente qué hacer en su siguiente turno a cada criatura encantada por tu Hechizar Animales y Plantas.`
                        },
                        {
                            nombre: "Dominio de la Tempestad",
                            rasgos: `<p>Los dioses de la Tempestad (Talos, Umberlee, Kord, Zeboim, el Devorador, Zeus, Thor) gobiernan tormentas, cielo y mar. Incluyen dioses del trueno, del relámpago, del océano y de la fuerza física. Sus clérigos inspiran miedo y son temidos incluso por sus propios aliados.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>nube brumosa, onda de trueno</td></tr>
                            <tr><td>3</td><td>ráfaga de viento, estallar</td></tr>
                            <tr><td>5</td><td>llamada al relámpago, tormenta aguanieve</td></tr>
                            <tr><td>7</td><td>controlar las aguas, tormenta de hielo</td></tr>
                            <tr><td>9</td><td>onda destructiva, plaga de insectos</td></tr></table>
                            <b>Nivel 1 — Competencias Adicionales:</b> Ganas competencia con armas marciales y armadura pesada.<br><br>
                            <b>Nivel 1 — Ira de la Tormenta:</b> Cuando una criatura a 5 pies de ti te golpea, puedes usar tu reacción para que haga salvación de DES. Si falla, recibe 2d8 de daño sónico o eléctrico (a tu elección). Si supera, recibe la mitad. Usos = modificador de SAB (mínimo 1). Se recargan en descanso prolongado.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Ira Destructiva:</b> Cuando hagas daño sónico o eléctrico, puedes usar Canalizar Divinidad para maximizar el daño (sin tirar dados).<br><br>
                            <b>Nivel 6 — Golpe de Rayo:</b> Cuando hagas daño eléctrico a una criatura Grande o más pequeña, también puedes empujarla 10 pies lejos de ti.<br><br>
                            <b>Nivel 8 — Golpe Divino:</b> Una vez por turno al golpear con un arma, causas 1d8 adicional de daño sónico. Aumenta a 2d8 al nivel 14.<br><br>
                            <b>Nivel 17 — Hijo de la Tormenta:</b> Ganas velocidad de vuelo igual a tu velocidad de movimiento, siempre que no estés en interiores o bajo tierra.`
                        },
                        {
                            nombre: "Dominio de Superchería",
                            rasgos: `<p>Los dioses de la superchería (Tymora, Beshaba, Olidammara, el Viajero, Garl Glittergold, Loki) son hacedores de engaños y representan una amenaza constante al orden establecido. Son patrones de ladrones, apostadores, rebeldes y libertadores. Sus clérigos prefieren el subterfugio, las bromas, el engaño y el robo a la confrontación directa.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>hechizar persona, disfrazarse</td></tr>
                            <tr><td>3</td><td>imagen múltiple, pasar sin dejar rastro</td></tr>
                            <tr><td>5</td><td>intermitencia, disipar magia</td></tr>
                            <tr><td>7</td><td>puerta dimensional, polimorfar</td></tr>
                            <tr><td>9</td><td>dominar persona, modificar recuerdo</td></tr></table>
                            <b>Nivel 1 — Bendición del Tramposo:</b> Como acción, tocas a una criatura voluntaria y le das ventaja en tiradas de Destreza (Sigilo). La bendición dura 1 hora o hasta que uses este rasgo de nuevo.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Invocar Duplicidad:</b> Como acción, creas una ilusión perfecta de ti mismo durante 1 minuto (concentración). Aparece en un espacio desocupado a 30 pies. Puedes moverla hasta 30 pies como acción adicional (máximo 120 pies de ti). Puedes lanzar conjuros desde su posición. Si ambos estáis a 5 pies de una criatura que ve la ilusión, tienes ventaja en ataques contra ella.<br><br>
                            <b>Nivel 6 — Canalizar Divinidad: Capa de las Sombras:</b> Como acción, te vuelves invisible hasta el final de tu siguiente turno. La invisibilidad termina si atacas o lanzas un conjuro.<br><br>
                            <b>Nivel 8 — Golpe Divino:</b> Una vez por turno al golpear con un arma, causas 1d8 adicional de daño de veneno. Aumenta a 2d8 al nivel 14.<br><br>
                            <b>Nivel 17 — Duplicidad Mejorada:</b> Puedes crear hasta 4 duplicados en lugar de 1 con Invocar Duplicidad. Puedes mover cualquier número de ellos como acción adicional (hasta 30 pies cada uno, máximo 120 pies de ti).`
                        },
                        {
                            nombre: "Dominio de la Vida",
                            rasgos: `<p>El dominio de Vida se centra en la energía positiva que sustenta toda vida. Los dioses de la vida promueven vitalidad y salud sanando enfermos, cuidando necesitados y alejando la muerte. Incluyen deidades agrícolas (Chauntea, Arawai, Deméter), solares (Lathander, Pelor, Re-Horakhty), de la sanación (Ilmater, Mishakal, Apolo, Diancecht) y del hogar (Hestia, Hathor, Boldrei). Prácticamente cualquier deidad no malvada puede reclamar este dominio.</p>
                            <h5 style="color:#d32f2f; margin-top:10px;">Conjuros de Dominio</h5>
                            <table style="margin-bottom:12px;"><tr><th>Nivel</th><th>Conjuros</th></tr>
                            <tr><td>1</td><td>bendecir, curar heridas</td></tr>
                            <tr><td>3</td><td>restablecimiento menor, arma espiritual</td></tr>
                            <tr><td>5</td><td>faro de esperanza, revivir</td></tr>
                            <tr><td>7</td><td>custodia contra la muerte, guardián de la fe</td></tr>
                            <tr><td>9</td><td>curar heridas en masa, revivir a los muertos</td></tr></table>
                            <b>Nivel 1 — Competencias Adicionales:</b> Ganas competencia con armadura pesada.<br><br>
                            <b>Nivel 1 — Discípulo de la Vida:</b> Tus conjuros de sanación son más efectivos. Cada vez que uses un conjuro de nivel 1 o superior para restaurar PG a una criatura, recupera PG adicionales iguales a 2 + el nivel del conjuro.<br><br>
                            <b>Nivel 2 — Canalizar Divinidad: Preservar Vida:</b> Como acción, presentas tu símbolo sagrado y evocas energía curativa que restaura PG totales = 5 × tu nivel de clérigo. Los distribuyes entre criaturas a 30 pies (máximo la mitad de sus PG máximos cada una). No funciona en muertos vivientes ni constructos.<br><br>
                            <b>Nivel 6 — Sanador Bendecido:</b> Cuando lanzas un conjuro de nivel 1 o superior que cura PG a una criatura que no seas tú, tú también te curas PG = 2 + el nivel del conjuro.<br><br>
                            <b>Nivel 8 — Golpe Divino:</b> Una vez por turno al golpear con un arma, causas 1d8 adicional de daño radiante. Aumenta a 2d8 al nivel 14.<br><br>
                            <b>Nivel 17 — Sanación Suprema:</b> Cuando uses un conjuro para recuperar PG y normalmente tirarías dados, en lugar de eso usas el resultado máximo posible para cada dado. Ej: en vez de tirar 2d6 recuperas directamente 12 PG.`
                        }
                    ]
                },
                { 
                    nombre: "Druida", 
                    descripcion: "Sacerdote de la Vieja Fe, empuña los poderes de la naturaleza —luz de luna y crecimiento vegetal, fuego y relámpago— y adopta formas animales.",
                    creacion_rapida: "Primero, Sabiduría debe ser tu mayor puntuación, seguida de Constitución. Segundo, elige el trasfondo Ermitaño.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de druida.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d8 (o 5) + tu modificador de Constitución por cada nivel de druida posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Armadura ligera, armadura media y escudos (los druidas no usarán armaduras ni escudos de metal).</li>
                        <li><b>Competencias — Armas:</b> Bastones, cimitarras, clavas, dagas, dardos, hoces, hondas, lanzas y mazas.</li>
                        <li><b>Competencias — Herramientas:</b> Kit de herboristería.</li>
                        <li><b>Tiradas de Salvación:</b> Inteligencia, Sabiduría.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Arcano, Medicina, Naturaleza, Percepción, Perspicacia, Religión, Supervivencia y Trato de Animales.</li>
                        <li><b>Equipo Inicial:</b> (a) escudo de madera o (b) arma simple c/c. (a) cimitarra o (b) arma simple a distancia. Armadura de cuero, equipo explorador y foco druídico.</li>
                        <li><b>Característica de Conjuros:</b> Sabiduría. CD de Salvación = 8 + bonificador de competencia + mod. SAB. Bonificador de ataque = bonificador de competencia + mod. SAB.</li>
                        <li><b>Foco de Conjuros:</b> Puedes usar un foco druídico (rama de muérdago, bastón tallado, tótem, etc.) como foco de tus conjuros de druida.</li>
                        <li><b>Preparar Conjuros:</b> Preparas una lista de conjuros druídicos disponibles tras un descanso prolongado. Número de conjuros preparados = tu nivel de druida + tu modificador de SAB (mínimo 1). Puedes cambiar tu lista de conjuros preparados al terminar un descanso prolongado.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Druida</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:700px;">
                        <tr><th>Nivel</th><th>Trucos</th><th>Conj. Prep.</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th><th>Nv5</th><th>Nv6</th><th>Nv7</th><th>Nv8</th><th>Nv9</th></tr>
                        <tr><td>1</td><td>2</td><td>2</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>2</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>2</td><td>4</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>3</td><td>5</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>3</td><td>6</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>3</td><td>7</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>3</td><td>8</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>3</td><td>9</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>3</td><td>10</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>4</td><td>11</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>4</td><td>12</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>4</td><td>12</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>4</td><td>13</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>14</td><td>4</td><td>13</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>15</td><td>4</td><td>14</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>16</td><td>4</td><td>14</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>17</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>18</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>19</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>20</td><td>4</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>2</td><td>1</td><td>1</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* "Conj. Prep." = número de conjuros druídicos que puedes tener preparados (nivel de druida + mod. SAB). Los trucos se conocen desde el principio y no ocupan espacios.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Lengua Druídica:</b> Conoces el lenguaje secreto de los druidas: Druídico. Es un idioma secreto que los druidas no enseñan a aquellos que no pertenecen a su orden. Puedes dejar mensajes ocultos escritos en druídico; otros druidas pueden reconocerlos, pero no los que no han sido iniciados.<br><br><b>Lanzamiento de Conjuros:</b> La magia del druida surge de la naturaleza misma. Sabiduría es tu característica de conjuros. Conoces 2 trucos druídicos a tu elección. A medida que subes de nivel, aprendes más trucos según la tabla. Tienes espacios de conjuro según la tabla y puedes preparar un número de conjuros de druida igual a tu nivel + modificador de SAB. Los espacios se recuperan con un descanso prolongado.<br><br><b>Rituales:</b> Puedes lanzar cualquier conjuro druídico que tengas preparado como ritual si tiene el rasgo Ritual." },
                        { nivel: 2, rasgos: "<b>Forma Salvaje:</b> Puedes usar tu acción para asumir mágicamente la forma de una bestia que hayas visto antes. Puedes usar este rasgo dos veces y recuperas todos los usos cuando terminas un descanso corto o prolongado. Tu nivel de druida determina las bestias que puedes transformarte en:<br>— <b>Nivel 2:</b> VD máximo 1/4, sin velocidad de vuelo ni natación.<br>— <b>Nivel 4:</b> VD máximo 1/2, sin velocidad de vuelo.<br>— <b>Nivel 8:</b> VD máximo 1.<br><br>Cuando te transformas, ocurre lo siguiente: tus estadísticas de juego son reemplazadas por las de la bestia, pero conservas tu alineación, personalidad e INT, SAB y CAR. También conservas tus competencias en tiradas de salvación y habilidades, usando las del perfil de la bestia o las tuyas propias, la que sea mayor. No puedes lanzar conjuros en esta forma (a menos que el Círculo de la Luna lo modifique). Los PG de la forma bestial son independientes; cuando se agotan, vuelves a tu forma original con los PG que tenías antes de transformarte (si son 0, recibes daño desbordante). La transformación también termina si caes inconsciente, mueres o decides terminarla (sin acción requerida).<br><br><b>Círculo Druídico:</b> En el nivel 2 eliges unirte al Círculo de la Tierra o al Círculo de la Luna. Tu elección te otorga rasgos en el nivel 2 y de nuevo en los niveles 6, 10 y 14." },
                        { nivel: 3, rasgos: "<b>Espacios de Conjuro de Nivel 2:</b> Ahora puedes preparar y lanzar conjuros de nivel 2. Tu número total de espacios por nivel aumenta según la tabla." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica de tu elección en 2, o puedes aumentar dos puntuaciones en 1 cada una. No puedes superar 20 con este rasgo.<br><br><b>Forma Salvaje (Mejora):</b> Ahora puedes transformarte en bestias de VD máximo 1/2 sin velocidad de vuelo.<br><br><b>Truco adicional:</b> Aprendes un truco druídico adicional (total 3 trucos)." },
                        { nivel: 5, rasgos: "<b>Espacios de Conjuro de Nivel 3:</b> Puedes preparar y lanzar conjuros de nivel 3. Tu número total de espacios por nivel aumenta según la tabla." },
                        { nivel: 6, rasgos: "<b>Rasgo de Círculo Druídico:</b> Tu círculo te otorga un rasgo adicional (ver subclase seleccionada)." },
                        { nivel: 7, rasgos: "<b>Espacios de Conjuro de Nivel 4:</b> Puedes preparar y lanzar conjuros de nivel 4." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b><br><br><b>Forma Salvaje (Mejora):</b> Ahora puedes transformarte en bestias de VD máximo 1 (incluyendo formas con vuelo, si el DM lo permite)." },
                        { nivel: 9, rasgos: "<b>Espacios de Conjuro de Nivel 5:</b> Puedes preparar y lanzar conjuros de nivel 5." },
                        { nivel: 10, rasgos: "<b>Rasgo de Círculo Druídico:</b> Tu círculo te otorga un rasgo adicional (ver subclase seleccionada).<br><br><b>Truco adicional:</b> Aprendes un truco druídico adicional (total 4 trucos)." },
                        { nivel: 11, rasgos: "<b>Espacios de Conjuro de Nivel 6:</b> Puedes preparar y lanzar conjuros de nivel 6. Los conjuros de los niveles 6 en adelante tienen solo 1 espacio por nivel y no se recuperan con descanso corto." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Espacios de Conjuro de Nivel 7:</b> Puedes preparar y lanzar conjuros de nivel 7 (1 espacio)." },
                        { nivel: 14, rasgos: "<b>Rasgo de Círculo Druídico:</b> Tu círculo te otorga su rasgo final (ver subclase seleccionada)." },
                        { nivel: 15, rasgos: "<b>Espacios de Conjuro de Nivel 8:</b> Puedes preparar y lanzar conjuros de nivel 8 (1 espacio)." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Espacios de Conjuro de Nivel 9:</b> Puedes preparar y lanzar conjuros de nivel 9 (1 espacio), el nivel máximo de los conjuros. A este nivel también ganas un espacio de nivel 5 adicional (total 3 espacios de nivel 5)." },
                        { nivel: 18, rasgos: "<b>Cuerpo Eterno:</b> La magia druídica que impregna tu ser lentifica tu envejecimiento: por cada 10 años que pasan, tu cuerpo envejece solo 1 año.<br><br><b>Conjuros Bestiales:</b> Puedes realizar los componentes somáticos y verbales de tus conjuros de druida mientras estás en una forma de bestia. Aunque no puedes hablar en forma de bestia, puedes concentrarte en conjuros que ya estaban activos. Esto te permite usar algunas formas animales para lanzar conjuros que normalmente requieren las manos." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Archidruida:</b> Puedes usar tu Forma Salvaje un número ilimitado de veces. Además, ignoras los componentes verbales y somáticos de tus conjuros de druida, así como cualquier componente material que no tenga un coste indicado y no sea consumido por el conjuro. Este beneficio no se aplica a los conjuros mágicos de otras clases." }
                    ],
                    subclases_titulo: "Círculos Druídicos",
                    subclases: [
                        { 
                            nombre: "Círculo de la Tierra", 
                            rasgos: `<p>El Círculo de la Tierra está formado por druidas místicos y sabios que custodian el conocimiento y rituales antiquísimos. Estos druidas se reúnen en círculos de árboles sagrados o recintos de piedra para compartir noticias y realizan rituales para honrar a la naturaleza. La sabiduría del círculo trasciende una sola vida; generación tras generación se reúne para acordar su lugar en el orden de las cosas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Truco Adicional</h5>
                            <p>Cuando eliges este círculo en el nivel 2, aprendes un truco druídico adicional de tu elección. Este truco no cuenta en tu número normal de trucos conocidos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Recuperación Natural</h5>
                            <p>Puedes recuperar parte de tu energía mágica sentándote en meditación y comulgando con la naturaleza. Durante un descanso corto, decides recuperar espacios de conjuro gastados. El nivel total de los espacios recuperados no puede ser superior a la mitad de tu nivel de druida (redondeando hacia arriba), y ningún espacio individual puede ser de nivel 6 o superior. Por ejemplo, un druida de nivel 4 podría recuperar espacios por un total de 2 niveles (p. ej., dos espacios de nivel 1 o un espacio de nivel 2). Solo puedes usar este rasgo una vez por descanso prolongado.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Niveles 3, 5, 7 y 9 — Conjuros del Círculo</h5>
                            <p>Tu conexión mística con la tierra te proporciona acceso a ciertos conjuros. Cuando alcanzas ciertos niveles de druida, ganas acceso a los Conjuros del Círculo que aparecen a continuación para el terreno que elegiste. Estos conjuros siempre están preparados y no cuentan en el número de conjuros que puedes preparar a diario. Si obtienes un conjuro de círculo que no aparece en la lista de conjuros del druida, el conjuro se considera un conjuro de druida para ti.</p>
                            <p>Debes elegir el tipo de terreno que representa tu círculo al elegir esta subclase:</p>
                            
                            <table style="margin:12px 0; font-size:0.85em;">
                                <tr><th>Terreno</th><th>Nivel 3</th><th>Nivel 5</th><th>Nivel 7</th><th>Nivel 9</th></tr>
                                <tr><td><b>Ártico</b></td><td>retener la paz, pico de agujas</td><td>luz del día, ralentizar</td><td>cono de frío, herir</td><td>comunión con la naturaleza, muro de hielo</td></tr>
                                <tr><td><b>Costa</b></td><td>imagen especular, paso brumoso</td><td>caminar sobre el agua, invocar relámpago</td><td>control del agua, solidez del vacío</td><td>invocar elemental, oleaje</td></tr>
                                <tr><td><b>Desierto</b></td><td>ceguera/sordera, silencio</td><td>crear comida y agua, protección contra energía</td><td>destello antecedente, tormenta de arena</td><td>plaga de insectos, muro de piedra</td></tr>
                                <tr><td><b>Bosque</b></td><td>golpe feérico, telaraña</td><td>invocar relámpago, crecimiento vegetal</td><td>divisor de la mente, localizar criatura</td><td>comunión con la naturaleza, plaga de insectos</td></tr>
                                <tr><td><b>Páramo</b></td><td>oscuridad, ver la invisibilidad</td><td>nube apestosa, pasos fantasmales</td><td>libertad de movimiento, localizar criatura</td><td>nube enferma, telepatía</td></tr>
                                <tr><td><b>Montaña</b></td><td>escalar la araña, pico de agujas</td><td>relámpago, fusión con la piedra</td><td>mano de piedra, ahorcar</td><td>muro de piedra, comunión con la naturaleza</td></tr>
                                <tr><td><b>Pantano</b></td><td>oscuridad, ver la invisibilidad</td><td>agua ácida, invocar relámpago</td><td>localizar criatura, libertad de movimiento</td><td>plaga de insectos, nube enferma</td></tr>
                                <tr><td><b>Subsuelo</b></td><td>araña de la web, telaraña</td><td>gasiform self, piedra a barro</td><td>ahorcar, localizar objeto</td><td>comunión con la naturaleza, paso del plano</td></tr>
                            </table>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Zancada Forestal</h5>
                            <p>Te mover a través de vegetación no mágica difícil no te cuesta movimiento adicional. Además, el terreno difícil de vegetación creado por magia no puede ralentizarte.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Protección de la Naturaleza</h5>
                            <p>Eres inmune al veneno y a las enfermedades, y tienes resistencia al daño de relámpago y truenos. Además, no puedes ser encantado ni asustado por elementales o plantas feéricas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Santuario de la Naturaleza</h5>
                            <p>Las criaturas del mundo natural dudan ante ti. Cuando una bestia o planta ataca, debe hacer una tirada de salvación de Sabiduría contra tu CD de Salvación de Conjuros del Druida. Con un fallo, la criatura debe elegir un objetivo diferente, o el ataque falla automáticamente. Si el ataque tiene éxito, la criatura es inmune a este efecto durante 24 horas. Las criaturas que sean conscientes de que eres un druida son inmunes al efecto de manera permanente.</p>`
                        },
                        { 
                            nombre: "Círculo de la Luna", 
                            rasgos: `<p>Los druidas del Círculo de la Luna son guardianes feroces de las tierras salvajes. Sus rituales se realizan bajo la luna llena, convocándose para compartir información y recordar los cambios de las estaciones. Frecuentan los lugares más profundos de las tierras salvajes donde la magia es fuerte y los hombres son escasos. Son conocidos por su capacidad de adoptar formas animales peligrosas para proteger la naturaleza.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Forma Salvaje de Combate</h5>
                            <p>Puedes usar tu Forma Salvaje como acción adicional en lugar de como una acción normal. Además, mientras estés en forma de bestia, puedes gastar un espacio de conjuro para recuperar 1d8 puntos de golpe por nivel del espacio gastado. Por ejemplo, gastar un espacio de nivel 2 recupera 2d8 PG. Esta curación se aplica a tu forma bestial.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Formas del Círculo</h5>
                            <p>El conocimiento ritual del círculo expande las formas que puedes adoptar. A partir de ahora, puedes usar tu Forma Salvaje para transformarte en una bestia con un VD tan alto como 1 (en lugar de VD 1/4), ignorando las restricciones usuales de movimiento de vuelo y natación.</p>
                            <p>A partir del <b>nivel 6</b>, puedes transformarte en una bestia con un VD igual a tu nivel de druida dividido entre 3 (redondeado hacia abajo). Por ejemplo, un druida de nivel 9 puede adoptar formas de bestias con VD hasta 3.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Golpe Primitivo</h5>
                            <p>Tus ataques realizados en forma de bestia cuentan como mágicos a efectos de superar la resistencia e inmunidad a ataques no mágicos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Forma Salvaje Elemental</h5>
                            <p>Puedes gastar dos usos de tu Forma Salvaje al mismo tiempo para transformarte en un elemental de aire, tierra, fuego o agua. Estas formas tienen VD de 5. Las estadísticas del elemental reemplazan a las tuyas, pero conservas tu alineamiento, personalidad, e INT, SAB y CAR.<br>Los elementales disponibles son: <b>Elemental de Aire (VD 5), Elemental de Tierra (VD 5), Elemental de Fuego (VD 5) y Elemental de Agua (VD 5)</b>, cuyos perfiles se encuentran en el Libro del Dungeon Master.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Mil Formas</h5>
                            <p>Has aprendido a usar la magia para alterar tu forma física de maneras más sutiles. Puedes lanzar el conjuro <i>alterar el propio aspecto</i> a voluntad, sin gastar un espacio de conjuro.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Explorador", 
                    descripcion: "Guerrero de las tierras salvajes, especializado en rastrear y cazar enemigos específicos. Los exploradores encuentran su fuerza en la soledad de las tierras salvajes, aprendiendo sus secretos, siguiendo el rastro de presas poderosas, y moviéndose con sigilo por el mundo.",
                    creacion_rapida: "Primero, Destreza debe ser tu mayor puntuación (o Fuerza si prefieres combate c/c), seguida de Sabiduría. Segundo, elige el trasfondo Forastero.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d10 por nivel de explorador.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 10 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d10 (o 6) + tu modificador de Constitución por cada nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Armadura ligera, armadura media y escudos.</li>
                        <li><b>Competencias — Armas:</b> Armas simples y armas marciales.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Fuerza, Destreza.</li>
                        <li><b>Habilidades:</b> Escoge tres entre Atletismo, Perspicacia, Investigación, Naturaleza, Percepción, Sigilo, Supervivencia y Trato con Animales.</li>
                        <li><b>Equipo Inicial:</b> (a) cota de escamas o (b) armadura de cuero. (a) dos espadas cortas o (b) dos armas simples c/c. (a) equipo de explorador o (b) equipo de saqueador de mazmorras. Una ballesta de mano y 20 virotes.</li>
                        <li><b>Característica de Conjuros:</b> Sabiduría. CD de Salvación = 8 + bonificador de competencia + mod. SAB. Bonificador de ataque = bonificador de competencia + mod. SAB.</li>
                        <li><b>Conjuros Conocidos:</b> El explorador conoce un número fijo de conjuros que siempre tiene disponibles (no los prepara, los conoce). El número de conjuros conocidos y los espacios disponibles se muestran en la tabla.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Explorador</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:600px;">
                        <tr><th>Nivel</th><th>Conj. Conocidos</th><th>Espacios Nv1</th><th>Espacios Nv2</th><th>Espacios Nv3</th><th>Espacios Nv4</th><th>Espacios Nv5</th></tr>
                        <tr><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>2</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>4</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>4</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>5</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>5</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>6</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>6</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>7</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>7</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>8</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td></tr>
                        <tr><td>14</td><td>8</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td></tr>
                        <tr><td>15</td><td>9</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td></tr>
                        <tr><td>16</td><td>9</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td></tr>
                        <tr><td>17</td><td>10</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td></tr>
                        <tr><td>18</td><td>10</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td></tr>
                        <tr><td>19</td><td>11</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td></tr>
                        <tr><td>20</td><td>11</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* El explorador conoce sus conjuros de forma fija (no los prepara diariamente). Los espacios se recuperan con un descanso prolongado. En nivel 1 no tiene conjuros.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Enemigo Predilecto:</b> Tienes ventaja en las pruebas de Sabiduría (Supervivencia) para rastrear a tus enemigos predilectos, y en las pruebas de Inteligencia para recordar información sobre ellos. Elige un tipo de enemigo predilecto entre: aberraciones, bestias, celestiales, constructos, dragones, elementales, feéricos, gigantes, humanoides (dos tipos de raza), demonios, muertos vivientes, oozes o plantas. A nivel 6 y 14 eliges un tipo adicional.<br><br><b>Explorador de lo Natural:</b> Eres especialmente hábil en un tipo de terreno natural. Elige un tipo de terreno predilecto entre: ártico, costa, desierto, bosque, páramo, montaña, pantano o subsuelo. Mientras estés en ese terreno:<ul style='margin:6px 0 0 20px; line-height:1.6'><li>El terreno difícil no ralentiza tu grupo si el grupo no está montado.</li><li>Tu grupo no puede perderse salvo por medios mágicos.</li><li>Incluso cuando estás realizando otra actividad, permaneces alerta.</li><li>Si vas solo, puedes moverte con sigilo a ritmo normal.</li><li>Cuando buscas comida o agua, encuentras el doble de lo normal.</li><li>Al rastrear otras criaturas, también aprendes su número exacto, sus tamaños y hace cuánto tiempo pasaron por allí.</li></ul>A nivel 6 y 10 eliges un terreno adicional." },
                        { nivel: 2, rasgos: "<b>Estilo de Combate:</b> Adoptas un estilo particular de combate. Elige una de estas opciones (no puedes elegir el mismo más de una vez):<br>— <b>Arquería:</b> Obtienes +2 a las tiradas de ataque con armas a distancia.<br>— <b>Defensa:</b> Mientras lleves armadura, obtienes +1 a la CA.<br>— <b>Duelista:</b> Cuando empuñas un arma c/c en una mano y ninguna otra arma, obtienes +2 a las tiradas de daño con ella.<br>— <b>Dos Armas:</b> Cuando luchas con dos armas, puedes añadir tu modificador de característica al daño del segundo ataque.<br><br><b>Lanzamiento de Conjuros:</b> A partir del nivel 2, puedes lanzar conjuros de explorador. Sabiduría es tu característica de conjuros. Los conjuros conocidos y los espacios disponibles están en la tabla. Puedes cambiar un conjuro conocido al subir de nivel por otro de la lista de explorador de nivel apropiado." },
                        { nivel: 3, rasgos: "<b>Arquetipo de Explorador:</b> Eliges un arquetipo que define tu estilo: Cazador o Señor de las Bestias. Tu arquetipo te otorga rasgos a nivel 3 y de nuevo en los niveles 7, 11 y 15.<br><br><b>Conciencia Primitiva:</b> Puedes gastar un espacio de conjuro (acción adicional) para agudizar tus sentidos en busca de tus enemigos predilectos. Durante 1 minuto por nivel del espacio gastado, puedes detectar si hay alguno de tus enemigos predilectos dentro de 1 milla de ti (6 millas en tierra a la intemperie). Esta información te indica cuántos hay, no su ubicación exacta." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica en 2, o dos en 1 cada una. No puedes superar 20 con este rasgo." },
                        { nivel: 5, rasgos: "<b>Ataque Extra:</b> Puedes atacar dos veces, en lugar de una, cada vez que uses la acción de Atacar en tu turno." },
                        { nivel: 6, rasgos: "<b>Enemigo Predilecto (Mejora):</b> Eliges un tipo de enemigo predilecto adicional.<br><b>Explorador de lo Natural (Mejora):</b> Eliges un tipo de terreno predilecto adicional." },
                        { nivel: 7, rasgos: "<b>Rasgo de Arquetipo:</b> Recibes un rasgo de tu arquetipo de explorador elegido (ver subclase)." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b><br><br><b>Zancada Terrestre:</b> Moverte a través de terreno difícil no mágico no te cuesta movimiento adicional. Además, no puedes ser atrapado por plantas no mágicas. Las plantas mágicas tampoco te pueden retener." },
                        { nivel: 9, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional de la lista de explorador de nivel apropiado (según tabla)." },
                        { nivel: 10, rasgos: "<b>Ocultarse a plena vista:</b> Puedes pasar 1 minuto creando una zona de camuflaje en el terreno natural que te rodea. Debes permanecer inmóvil. Mientras estés escondido así, eres prácticamente invisible: obtienes un bonificador de +10 a las pruebas de Sigilo mientras no te muevas. La ocultación termina si te mueves, atacas, lanzas un conjuro o actúas de cualquier manera visible.<br><br><b>Explorador de lo Natural (Mejora):</b> Eliges un tipo de terreno predilecto adicional." },
                        { nivel: 11, rasgos: "<b>Rasgo de Arquetipo:</b> Recibes un rasgo de tu arquetipo de explorador elegido (ver subclase)." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional de la lista de explorador (según tabla)." },
                        { nivel: 14, rasgos: "<b>Enemigo Predilecto (Mejora):</b> Eliges un tipo de enemigo predilecto adicional.<br><br><b>Desvanecerse:</b> Puedes usar la acción de Esconderse como acción adicional en tu turno. Además, no puedes ser rastreado por medios no mágicos a menos que decidas dejar un rastro." },
                        { nivel: 15, rasgos: "<b>Rasgo de Arquetipo:</b> Recibes el rasgo final de tu arquetipo de explorador elegido (ver subclase)." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional de la lista de explorador (según tabla)." },
                        { nivel: 18, rasgos: "<b>Sentidos Salvajes:</b> Adquieres sentidos sobrenaturales que te ayudan a luchar contra criaturas que no puedes ver. Cuando ataques a una criatura que no puedes ver, no tienes desventaja en la tirada de ataque contra ella siempre que no seas ciego, sordo o incapacitado. También eres consciente de la ubicación de cualquier criatura invisible a 30 pies de ti (siempre que no estés cegado o ensordecido)." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Asesino de Enemigos:</b> Te conviertes en un cazador de leyenda. Una vez por turno, cuando hagas una tirada de ataque o de daño contra uno de tus enemigos predilectos, puedes añadir tu modificador de Sabiduría a esa tirada." }
                    ],
                    subclases_titulo: "Arquetipos de Explorador",
                    subclases: [
                        { 
                            nombre: "Cazador", 
                            rasgos: `<p>Emular al Cazador es aceptar el lugar del explorador en la cadena alimentaria de la naturaleza: depredador en la cima. A medida que avanzas en este arquetipo, aprendes técnicas de caza especializadas para combatir una variedad de amenazas, ya sean hordas de criaturas o monstruos colosales.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Presa del Cazador</h5>
                            <p>Ganas uno de los siguientes rasgos a tu elección:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Asesino de Colosos:</b> Tu tenacidad puede derribar a los adversarios más poderosos. Cuando golpeas a una criatura que está por debajo de su máximo de PG con un ataque de arma, le haces 1d8 de daño adicional. Solo puedes hacer este daño adicional una vez por turno.</li>
                                <li><b>Aplastador de Gigantes:</b> Cuando una criatura de tamaño Grande o mayor dentro de 5 pies te ataca, puedes usar tu reacción para atacarla inmediatamente después de que se resuelva su ataque, siempre que puedas verla.</li>
                                <li><b>Asesino de Hordas:</b> Una vez en cada uno de tus turnos, cuando hagas un ataque de arma, puedes hacer otro ataque con la misma arma contra una criatura diferente que esté a 5 pies del objetivo original y dentro del alcance de tu arma.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Tácticas Defensivas</h5>
                            <p>Ganas uno de los siguientes rasgos a tu elección:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Escurrir entre las Hordas:</b> Los ataques de oportunidad contra ti tienen desventaja.</li>
                                <li><b>Defensa ante Ataques Múltiples:</b> Cuando una criatura te golpea con un ataque, ganas +4 a la CA contra todos los ataques subsiguientes de esa criatura durante el resto del turno.</li>
                                <li><b>Voluntad de Acero:</b> Tienes ventaja en las tiradas de salvación contra quedar asustado.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 11 — Ataque Múltiple</h5>
                            <p>Ganas uno de los siguientes rasgos a tu elección:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Granizo de Balas:</b> Puedes usar tu acción para hacer un ataque a distancia contra cualquier número de criaturas dentro de 10 pies de un punto que puedas ver y que estén dentro del alcance de tu arma. Debes tener munición para cada objetivo, y haces una tirada de ataque separada para cada uno.</li>
                                <li><b>Torbellino de Golpes:</b> Puedes usar tu acción para hacer un ataque cuerpo a cuerpo contra cualquier número de criaturas dentro de 5 pies de ti, con una tirada de ataque separada para cada objetivo.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Defensa Superior del Cazador</h5>
                            <p>Ganas uno de los siguientes rasgos a tu elección:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Evasión:</b> Cuando estás sujeto a un efecto que te permite hacer una tirada de salvación de Destreza para sufrir solo la mitad del daño, en su lugar no sufres ningún daño si tienes éxito en la tirada de salvación, y solo la mitad del daño si la fallas. No puedes usar Evasión si estás incapacitado.</li>
                                <li><b>Aguantar el Ataque:</b> Cuando una criatura te falla con un ataque c/c, puedes usar tu reacción para obligarla a repetir ese ataque contra otra criatura de tu elección (distinta a sí misma).</li>
                                <li><b>Esquiva Prodigiosa:</b> Cuando un atacante que puedas ver te golpea con un ataque, puedes usar tu reacción para reducir a la mitad el daño de ese ataque.</li>
                            </ul>`
                        },
                        { 
                            nombre: "Señor de las Bestias", 
                            rasgos: `<p>El prototípico Señor de las Bestias vive en la frontera entre la civilización y los peligros de la naturaleza. Capaz de rastrear a sus presas a través de las tierras más peligrosas, el Señor de las Bestias caza con un compañero animal: una bestia leal y feroz entrenada para luchar a su lado. Un Señor de las Bestias puede rastrear una presa sin descubrir al tiempo que su compañero bloquea el camino de la presa.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Compañero Bestial</h5>
                            <p>Ganas un compañero animal que viaja contigo en tus aventuras y recibe entrenamiento especial para luchar junto a ti. Elige una bestia con CR de 1/4 o inferior que no tenga velocidad de vuelo o natación. La bestia obedece tus comandos lo mejor que puede.</p>
                            <p><b>Estadísticas del Compañero:</b></p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Puntos de Golpe:</b> El compañero tiene PG iguales a cuatro veces tu nivel de explorador, o los PG normales de la bestia si son superiores.</li>
                                <li><b>Bonificador de Competencia:</b> El compañero usa tu bonificador de competencia en lugar del suyo propio para tiradas de ataque, daño, y salvaciones donde sea competente.</li>
                                <li><b>Características:</b> Usa las estadísticas del Manual de Monstruos para la bestia elegida.</li>
                                <li><b>Acciones:</b> El compañero actúa en tu iniciativa. Puedes gastar una acción adicional para ordenarle que ataque, se mueva o realice otras acciones. Si no le das órdenes, el compañero realiza la acción de Esquivar.</li>
                                <li><b>Si muere:</b> Puedes conseguir otro compañero tras un ritual de 8 horas de duración para establecer un vínculo con una nueva bestia (que cumpla los requisitos).</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Entrenamiento Extraordinario</h5>
                            <p>Durante cualquier turno en que tu compañero bestial no ataque, puede usar su acción para realizar las acciones de Correr, Desengancharse, Esquivar, o Ayudar.</p>
                            <p>Además, los ataques c/c de tu compañero animal cuentan como mágicos a efectos de superar la resistencia e inmunidad a ataques y daño no mágicos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 11 — Bestia Feroz</h5>
                            <p>Cuando ordenas a tu compañero bestial que ataque, puede realizar dos ataques, o puede usar Ataque Múltiple si ya lo tiene disponible.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Compartir Conjuros</h5>
                            <p>Cuando lanzas un conjuro que solo tiene como objetivo a ti mismo, también puedes afectar a tu compañero bestial con ese conjuro si este se encuentra dentro de 30 pies de ti.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Guerrero", 
                    descripcion: "Maestro del combate marcial, hábil con una variedad de armas y armaduras. Los guerreros aprenden el arte de la batalla dominando una gran variedad de armas y técnicas de combate, siendo los combatientes más versátiles y completos de todos los grupos de aventureros.",
                    creacion_rapida: "Primero, asigna Fuerza o Destreza como tu puntuación más alta (según prefieras combate c/c o a distancia), seguida de Constitución. Segundo, elige el trasfondo Soldado.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d10 por nivel de guerrero.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 10 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d10 (o 6) + tu modificador de Constitución por cada nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Todas las armaduras y escudos.</li>
                        <li><b>Competencias — Armas:</b> Armas simples y armas marciales.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Fuerza, Constitución.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Acrobacias, Trato con Animales, Atletismo, Historia, Perspicacia, Intimidación, Percepción y Supervivencia.</li>
                        <li><b>Equipo Inicial:</b> (a) cota de mallas o (b) armadura de cuero, arco largo y 20 flechas. (a) arma marcial y escudo, o (b) dos armas marciales. (a) ballesta ligera y 20 virotes, o (b) 2 hachas de mano. (a) equipo de saqueador de mazmorras, o (b) equipo de explorador.</li>
                    </ul>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Estilo de Combate:</b> Adoptas un estilo de combate particular como especialidad. Elige una de las siguientes opciones (no puedes elegir la misma más de una vez, incluso si tienes la posibilidad de elegir de nuevo):<br><ul style='margin:6px 0 0 20px; line-height:1.8'><li><b>Arquería:</b> Ganas un bonificador de +2 a las tiradas de ataque con armas a distancia.</li><li><b>Defensa:</b> Mientras lleves armadura, ganas un bonificador de +1 a la CA.</li><li><b>Duelista:</b> Cuando empuñas un arma c/c en una mano y no tienes otra arma, ganas un bonificador de +2 a las tiradas de daño con esa arma.</li><li><b>Combate con Armas Grandes:</b> Cuando sacas 1 o 2 en un dado de daño con un ataque de arma a dos manos o versátil (usada a dos manos), puedes repetir el dado y quedarte con el nuevo resultado.</li><li><b>Protección:</b> Cuando una criatura atacas a un objetivo distinto a ti y que esté a 5 pies de ti, puedes usar tu reacción para imponer desventaja a la tirada de ataque (necesitas escudo).</li><li><b>Combate con Dos Armas:</b> Cuando luchas con dos armas, puedes añadir tu modificador de característica al daño del segundo ataque.</li></ul><br><b>Nuevas Energías:</b> Tienes una reserva sobrehumana de vigor que puedes aprovechar en combate. Con ella, puedes protegerte de daño o curarte. Usa una acción adicional para recuperar PG igual a 1d10 + tu nivel de guerrero. Una vez usas este rasgo, debes terminar un descanso corto o prolongado para volver a usarlo." },
                        { nivel: 2, rasgos: "<b>Oleada de Acción:</b> Puedes sobrepasar momentáneamente tus capacidades normales. En tu turno, puedes realizar una acción adicional además de tu acción normal y tu posible acción adicional. Una vez usas este rasgo, debes terminar un descanso corto o prolongado para volver a usarlo. A partir del nivel 17 puedes usarlo dos veces por descanso (pero solo una vez por turno)." },
                        { nivel: 3, rasgos: "<b>Arquetipo Marcial:</b> Eliges un arquetipo que se esfuerza por emular en tus estilos y técnicas de combate: Campeón, Maestro de Batalla o Caballero Arcano. Cada arquetipo te otorga rasgos a nivel 3 y de nuevo en los niveles 7, 10, 15 y 18." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica en 2, o dos en 1 cada una. No puedes superar 20 con este rasgo." },
                        { nivel: 5, rasgos: "<b>Ataque Extra:</b> Puedes atacar dos veces, en lugar de una, cada vez que uses la acción de Atacar en tu turno." },
                        { nivel: 6, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 7, rasgos: "<b>Rasgo de Arquetipo Marcial:</b> Recibes un rasgo de tu arquetipo marcial elegido (ver subclase)." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Indomable (1 uso):</b> Puedes repetir una tirada de salvación que hayas fallado. Si lo haces, debes usar el nuevo resultado y no puedes usar este rasgo de nuevo hasta que termines un descanso prolongado. A nivel 13 puedes usarlo 2 veces, y a nivel 17 puedes usarlo 3 veces entre descansos prolongados." },
                        { nivel: 10, rasgos: "<b>Rasgo de Arquetipo Marcial:</b> Recibes un rasgo de tu arquetipo marcial elegido (ver subclase)." },
                        { nivel: 11, rasgos: "<b>Ataque Extra (3 ataques):</b> Ahora puedes atacar tres veces cada vez que uses la acción de Atacar." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Indomable (2 usos):</b> Ahora puedes usar Indomable dos veces antes de necesitar un descanso prolongado." },
                        { nivel: 14, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 15, rasgos: "<b>Rasgo de Arquetipo Marcial:</b> Recibes un rasgo de tu arquetipo marcial elegido (ver subclase)." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Oleada de Acción (2 usos):</b> Ahora puedes usar Oleada de Acción dos veces por descanso (pero solo una vez por turno).<br><br><b>Indomable (3 usos):</b> Ahora puedes usar Indomable tres veces antes de necesitar un descanso prolongado." },
                        { nivel: 18, rasgos: "<b>Rasgo de Arquetipo Marcial:</b> Recibes el rasgo final de tu arquetipo marcial elegido (ver subclase)." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Ataque Extra (4 ataques):</b> Puedes atacar cuatro veces cada vez que uses la acción de Atacar. Esta es la mayor cantidad de ataques de cualquier clase en el juego." }
                    ],
                    subclases_titulo: "Arquetipos Marciales",
                    subclases: [
                        { 
                            nombre: "Campeón", 
                            rasgos: `<p>El arquetipo Campeón se centra en el desarrollo de la potencia física bruta llevada a una perfección mortal. Los que modelan su estilo de lucha siguiendo este arquetipo combinan el entrenamiento riguroso con la excelencia física para golpear con fuerza devastadora.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Crítico Mejorado</h5>
                            <p>Tus ataques con armas consiguen un golpe crítico cuando sacas 19 o 20 en el dado de ataque (en lugar del 20 normal).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Atleta Extraordinario</h5>
                            <p>Puedes añadir la mitad de tu bonificador de competencia (redondeando hacia arriba) a cualquier prueba de Fuerza, Destreza o Constitución en la que no seas ya competente.<br>Además, cuando realizas un salto de longitud en carrera, la distancia que puedes cubrir aumenta en un número de pies igual a tu modificador de Fuerza.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Estilo de Combate Adicional</h5>
                            <p>Puedes elegir una segunda opción de la función Estilo de Combate del guerrero.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Crítico Superior</h5>
                            <p>Tus ataques con armas ahora consiguen un golpe crítico cuando sacas 18, 19 o 20 en el dado de ataque.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 18 — Superviviente</h5>
                            <p>Alcanzas el pico de tu resistencia en batalla. Al inicio de cada uno de tus turnos, recuperas PG iguales a 5 + tu modificador de Constitución si tienes menos de la mitad de tus PG máximos pero al menos 1 PG.</p>`
                        },
                        { 
                            nombre: "Maestro de Batalla", 
                            rasgos: `<p>Los que siguen el arquetipo de Maestro de Batalla emplean técnicas marciales transmitidas a lo largo de generaciones. Para un Maestro de Batalla, el combate es un campo académico que a veces incluye el conocimiento del propio enemigo. No todo guerrero se absorbe en el aspecto físico del combate, sino también el intelectual.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Pericia en Combate</h5>
                            <p>Ganas competencia con un tipo de herramienta de artesano a tu elección.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Superioridad en Combate</h5>
                            <p>Aprendes maniobras alimentadas por dados especiales llamados <b>Dados de Superioridad</b>.</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Maniobras:</b> Aprendes 3 maniobras a tu elección (lista abajo). Aprendes 2 adicionales en los niveles 7, 10 y 15 (total 9 maniobras).</li>
                                <li><b>Dados de Superioridad:</b> Tienes 4 Dados de Superioridad d8. Un dado se gasta al usar una maniobra. Recuperas todos los usados al terminar un descanso corto o prolongado. A nivel 7 suben a 5 dados, a nivel 15 a 6 dados.</li>
                                <li><b>Progresión del dado:</b> El dado es d8 a nivel 3, sube a <b>d10 en nivel 10</b> y a <b>d12 en nivel 15</b>.</li>
                                <li><b>CD de Salvación de Maniobra:</b> 8 + tu bonificador de competencia + tu modificador de Fuerza o Destreza (el que sea mayor).</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:12px;">Lista de Maniobras</h5>
                            <div class="card-grid" style="margin-top:10px;">
                                <div class="card"><h3>Golpe de Precisión</h3><p>Cuando haces una tirada de ataque con arma, puedes gastar un Dado de Superioridad y sumarlo a esa tirada. Puedes usarlo antes o después de saber si aciertas (pero antes de aplicar los efectos).</p></div>
                                <div class="card"><h3>Golpe Amedrentador</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas su resultado al daño. Si el objetivo falla una salvación de Sabiduría, queda <i>asustado</i> de ti hasta el final de tu siguiente turno.</p></div>
                                <div class="card"><h3>Golpe Empujador</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas su resultado al daño. Si el objetivo falla una salvación de Fuerza, lo empujas hasta 15 pies lejos de ti.</p></div>
                                <div class="card"><h3>Golpe Derribador</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas al daño. Si el objetivo falla una salvación de Fuerza, queda <i>derribado</i> (tumbado).</p></div>
                                <div class="card"><h3>Golpe Distractivo</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas al daño. El siguiente ataque de un aliado contra ese objetivo tiene ventaja hasta el inicio de tu próximo turno.</p></div>
                                <div class="card"><h3>Golpe Desarmador</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas al daño. El objetivo debe superar una salvación de Fuerza o suelta un objeto de tu elección (cae a sus pies).</p></div>
                                <div class="card"><h3>Golpe Provocador</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas al daño. El objetivo falla una salvación de Sabiduría o tiene desventaja en todas las tiradas de ataque contra objetivos distintos a ti hasta el fin de tu siguiente turno.</p></div>
                                <div class="card"><h3>Golpe de Barrida</h3><span class="tag">Al golpear</span><p>Puedes gastar un DS para golpear a una segunda criatura dentro de 5 pies del objetivo original y dentro de tu alcance. Haces daño igual al resultado del DS (no necesitas tirada de ataque).</p></div>
                                <div class="card"><h3>Contraataque</h3><span class="tag">Reacción</span><p>Cuando una criatura te falla con un ataque c/c, puedes usar tu reacción para hacer un ataque c/c contra ella. Si golpeas, sumas un DS al daño.</p></div>
                                <div class="card"><h3>Parar</h3><span class="tag">Reacción</span><p>Cuando una criatura te daña con un ataque c/c, puedes usar tu reacción para reducir ese daño en el resultado del DS + tu modificador de Destreza.</p></div>
                                <div class="card"><h3>Maniobra Esquiva</h3><span class="tag">Movimiento</span><p>Cuando te mueves en tu turno, puedes gastar un DS y sumarlo a tu CA hasta que dejes de moverte.</p></div>
                                <div class="card"><h3>Ataque Fingido</h3><span class="tag">Acción adicional</span><p>Puedes gastar un DS para fintar. Eliges una criatura a 5 pies. Tienes ventaja en la siguiente tirada de ataque contra ella este turno. Si aciertas, sumas el DS al daño.</p></div>
                                <div class="card"><h3>Golpe de Maniobra</h3><span class="tag">Al golpear</span><p>Gastas un DS y sumas al daño. Eliges a un aliado amistoso que pueda verte u oírte: puede usar su reacción para moverse hasta la mitad de su velocidad sin provocar ataques de oportunidad.</p></div>
                                <div class="card"><h3>Golpe de Instructor</h3><span class="tag">Acción adicional</span><p>Renuncias a uno de tus ataques de la acción Atacar para indicarle a un aliado que puede ver u oír que realice un ataque. Ese aliado puede hacer un ataque con reacción y suma el DS al daño si golpea.</p></div>
                                <div class="card"><h3>Conoce a tu Enemigo</h3><span class="tag">Nivel 7</span><p>Si pasas al menos 1 minuto observando o interactuando con otra criatura fuera del combate, puedes pedirle al DM que compare una característica tuya con la de la criatura: FUE, DES, CON, CA, PG actuales (mayor/menor/igual), niveles de clase o dados de golpe.</p></div>
                            </div>

                            <h5 style="color:#d32f2f; margin-top:20px;">Nivel 15 — Implacable</h5>
                            <p>Cuando tiras iniciativa y no te quedan Dados de Superioridad, recuperas 1.</p>`
                        },
                        { 
                            nombre: "Caballero Arcano", 
                            rasgos: `<p>El arquetipo de Caballero Arcano combina la maestría marcial de un guerrero con un cuidadoso estudio de la magia. Los Caballeros Arcanos usan técnicas mágicas similares a las practicadas por los magos. Se enfocan en las escuelas de Abjuración y Evocación: Abjuración porque les protege en batalla, y Evocación porque canaliza energía destructiva en el combate. Antes del nivel 10, la mayoría de sus conjuros conocidos deben ser de abjuración o evocación (con algunas excepciones).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Tabla de Conjuros del Caballero Arcano</h5>
                            <div style="overflow-x:auto;">
                            <table style="font-size:0.85em; min-width:500px;">
                                <tr><th>Nivel Guerrero</th><th>Trucos</th><th>Conj. Conocidos</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th></tr>
                                <tr><td>3</td><td>2</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>4</td><td>2</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>5</td><td>2</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>6</td><td>2</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>7</td><td>2</td><td>5</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>8</td><td>2</td><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>9</td><td>2</td><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>10</td><td>3</td><td>7</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>11</td><td>3</td><td>8</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>12</td><td>3</td><td>8</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>13</td><td>3</td><td>9</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>14</td><td>3</td><td>10</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>15</td><td>3</td><td>10</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>16</td><td>3</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>17</td><td>3</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>18</td><td>3</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>19</td><td>3</td><td>12</td><td>4</td><td>3</td><td>3</td><td>1</td></tr>
                                <tr><td>20</td><td>3</td><td>13</td><td>4</td><td>3</td><td>3</td><td>1</td></tr>
                            </table>
                            </div>
                            <p style="color:var(--text-secondary); font-size:0.85em; margin-top:6px;">* La característica de conjuros del Caballero Arcano es <b>Inteligencia</b>. Los conjuros conocidos deben ser de las escuelas de Abjuración o Evocación, salvo las excepciones marcadas abajo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Lanzamiento de Conjuros</h5>
                            <p>Aprendes la magia de los magos. Inteligencia es tu característica de conjuros (CD Salvación = 8 + bonificador de competencia + mod. INT).</p>
                            <p><b>Conjuros conocidos:</b> Conoces 3 conjuros de mago de nivel 1. Al menos dos deben ser de Abjuración o Evocación. El tercer conjuro puede ser de cualquier escuela. A medida que subes de nivel como guerrero, puedes aprender más conjuros de la tabla:</p>
                            <ul style="line-height:1.7; padding-left:20px;">
                                <li>Cuando aprendes un nuevo conjuro, puede ser de cualquier nivel para el que tengas espacios. Debe ser de Abjuración o Evocación, <b>salvo en los niveles de guerrero 3, 8, 14 y 20</b>, donde puedes aprender un conjuro de cualquier escuela.</li>
                                <li>Puedes reemplazar un conjuro conocido al subir de nivel de guerrero, siguiendo las mismas restricciones de escuela.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Vínculo de Arma</h5>
                            <p>Aprendes un ritual de 1 hora para crear un vínculo mágico con un arma. Una vez vinculada:</p>
                            <ul style="line-height:1.7; padding-left:20px;">
                                <li>No puedes ser desarmado del arma vinculada a menos que estés incapacitado.</li>
                                <li>Si el arma no mágica vinculada está en el mismo plano de existencia, puedes invocarla a tu mano vacía como acción adicional.</li>
                            </ul>
                            <p>Puedes tener hasta dos armas vinculadas, pero solo puedes invocar una por acción adicional. Si intentas vincular un tercer arma, el vínculo más antiguo se rompe.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Magia de Guerra</h5>
                            <p>Has aprendido a entremezclar el lanzamiento de conjuros con el combate con armas. Cuando usas tu acción para lanzar un <b>truco</b>, puedes realizar un ataque con arma como acción adicional.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Vínculo de Arma Adicional</h5>
                            <p>Puedes vincular un segundo arma (ahora puedes tener dos armas vinculadas activas simultáneamente y llamar a cualquiera de ellas como acción adicional).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Hechizo Cargado</h5>
                            <p>Aprendes a cargar temporalmente un arma con energía mágica. Cuando usas tu acción para lanzar un conjuro de nivel 1 o superior con tiempo de lanzamiento de 1 acción, puedes elegir que ese conjuro se almacene en un arma cuerpo a cuerpo que estás empuñando. La próxima vez que golpees con esa arma, además del daño normal del arma, el arma libera el conjuro almacenado. Si el conjuro requiere una tirada de ataque, golpea automáticamente; si requiere una tirada de salvación, el objetivo la realiza contra tu CD de conjuros. El conjuro se libera si no has golpeado con el arma al finalizar tu siguiente turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 18 — Magia de Guerra Mejorada</h5>
                            <p>Cuando usas tu acción para lanzar un conjuro <b>de cualquier nivel</b> (no solo trucos), puedes realizar un ataque con arma como acción adicional.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Hechicero", 
                    descripcion: "Los hechiceros llevan la magia en su propia sangre. Su poder no surge del estudio como el del mago, sino de una fuente innata: un linaje dracónico, una línea de sangre tocada por los dioses, o el caos puro de la magia salvaje. Mientras otros deben memorizar conjuros, el hechicero los siente fluir de forma natural desde su interior.",
                    creacion_rapida: "Primero, Carisma debería ser tu puntuación más alta, seguida de Constitución. Segundo, elige el trasfondo Ermitaño. Trucos recomendados: Toque Gélido y Estallido Arcano. Conjuros de nivel 1: Escudo y Rayo de Fuego.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d6 por nivel de hechicero.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 6 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d6 (o 4) + tu modificador de Constitución por cada nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Ninguna.</li>
                        <li><b>Competencias — Armas:</b> Dagas, dardos, hondas, bastones y ballestas ligeras.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Constitución, Carisma.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Arcano, Engaño, Intimidación, Perspicacia, Persuasión y Religión.</li>
                        <li><b>Equipo Inicial:</b> (a) ballesta ligera y 20 virotes o (b) arma simple. (a) bolsa de componentes o (b) foco arcano. (a) equipo de saqueador de mazmorras o (b) equipo de explorador. Dos dagas.</li>
                        <li><b>Característica de Conjuros:</b> Carisma. CD de Salvación = 8 + bonificador de competencia + mod. CAR. Bonificador de ataque = bonificador de competencia + mod. CAR.</li>
                        <li><b>Conjuros Conocidos:</b> El hechicero conoce un número fijo de conjuros (no prepara diariamente). Conoce 2 conjuros de nivel 1 al inicio, y gana más según la tabla. Puede reemplazar un conjuro conocido al subir de nivel.</li>
                        <li><b>Foco de Conjuros:</b> Puede usar un foco arcano como foco de sus conjuros.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Hechicero</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:750px;">
                        <tr><th>Nivel</th><th>Trucos</th><th>Conj. Conocidos</th><th>Pts. Hech.</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th><th>Nv5</th><th>Nv6</th><th>Nv7</th><th>Nv8</th><th>Nv9</th></tr>
                        <tr><td>1</td><td>4</td><td>2</td><td>—</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>4</td><td>3</td><td>2</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>4</td><td>4</td><td>3</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>5</td><td>5</td><td>4</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>5</td><td>6</td><td>5</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>5</td><td>7</td><td>6</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>5</td><td>8</td><td>7</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>5</td><td>9</td><td>8</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>5</td><td>10</td><td>9</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>6</td><td>11</td><td>10</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>6</td><td>12</td><td>11</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>6</td><td>12</td><td>12</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>6</td><td>13</td><td>13</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>14</td><td>6</td><td>13</td><td>14</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>15</td><td>6</td><td>14</td><td>15</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>16</td><td>6</td><td>14</td><td>16</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>17</td><td>6</td><td>15</td><td>17</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>18</td><td>6</td><td>15</td><td>18</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>19</td><td>6</td><td>15</td><td>19</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>20</td><td>6</td><td>15</td><td>20</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>2</td><td>1</td><td>1</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* Los espacios de conjuro se recuperan con un descanso prolongado. Los Puntos de Hechicería también se recuperan con descanso prolongado.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Lanzamiento de Conjuros:</b> La magia del hechicero surge de un don o linaje innato. Carisma es tu característica de conjuros. Conoces 4 trucos y 2 conjuros de nivel 1. La tabla de conjuros muestra cuántos trucos y conjuros conoces y cuántos espacios tienes por nivel. Al subir de nivel como hechicero puedes reemplazar un conjuro conocido por otro de nivel apropiado.<br><br><b>Origen de Hechicero:</b> Elige el origen que describe el origen de tu poder mágico innato: Línea de Sangre Dracónica o Magia Salvaje. Tu elección te otorga rasgos a nivel 1 y de nuevo en los niveles 6, 14 y 18." },
                        { nivel: 2, rasgos: "<b>Fuente de Magia:</b> Aprovechas una fuente profunda de magia en tu interior. Esta fuente está representada por Puntos de Hechicería que te permiten crear una variedad de efectos mágicos. Comienzas con 2 puntos y ganas 1 adicional por cada nivel posterior (igual a tu nivel de hechicero en total).<br><br><b>Flexibilidad Hechiceril — Crear espacios de conjuro:</b> Puedes transformar Puntos de Hechicería no gastados en un espacio de conjuro como acción adicional en tu turno. La tabla muestra el coste:<br><div style='overflow-x:auto; margin:8px 0'><table style='font-size:0.85em; width:auto'><tr><th>Nivel del espacio</th><th>Coste en Puntos</th></tr><tr><td>1</td><td>2</td></tr><tr><td>2</td><td>3</td></tr><tr><td>3</td><td>5</td></tr><tr><td>4</td><td>6</td></tr><tr><td>5</td><td>7</td></tr></table></div><b>Convertir espacios en Puntos:</b> Como acción adicional, puedes gastar un espacio de conjuro y ganar Puntos de Hechicería igual al nivel de ese espacio. No puedes crear espacios de nivel 6 o superior con este método." },
                        { nivel: 3, rasgos: "<b>Metamagia:</b> Adquieres la habilidad de distorsionar tus conjuros según tus necesidades. Ganas dos de las siguientes opciones de Metamagia a tu elección. Ganas otra a los niveles 10 y 17.<br><ul style='margin:6px 0 0 20px; line-height:1.8'><li><b>Conjuro Cuidadoso (1 pto):</b> Eliges hasta CAR criaturas. Superan automáticamente la tirada de salvación del conjuro.</li><li><b>Conjuro Distante (1 pto):</b> Duplicas el alcance del conjuro. Si el alcance es de toque, pasa a 30 pies.</li><li><b>Conjuro Empoderado (1 pto):</b> Puedes repetir hasta CAR dados de daño del conjuro y usar los nuevos resultados. Puedes combinarlo con otra Metamagia.</li><li><b>Conjuro Extendido (1 pto):</b> Duplicas la duración del conjuro (máximo 24 horas).</li><li><b>Conjuro Potenciado (3 ptos):</b> Un objetivo del conjuro tiene desventaja en su primera tirada de salvación contra él.</li><li><b>Conjuro Sutil (1 pto):</b> Puedes lanzar el conjuro sin componentes verbales ni somáticos.</li><li><b>Conjuro Acelerado (2 ptos):</b> Si el tiempo de lanzamiento es 1 acción, puedes lanzarlo como acción adicional en su lugar.</li><li><b>Conjuro Gemelo (coste = nivel del conjuro, mín. 1):</b> Si el conjuro afecta a un solo objetivo y no tiene alcance de uno mismo, puedes seleccionar un segundo objetivo que esté dentro del alcance.</li></ul>" },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características.</b> Además aprendes 1 truco adicional (total 5 trucos)." },
                        { nivel: 5, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 3." },
                        { nivel: 6, rasgos: "<b>Rasgo de Origen Hechicero:</b> Tu origen te otorga un rasgo adicional (ver subclase)." },
                        { nivel: 7, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 4." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 5." },
                        { nivel: 10, rasgos: "<b>Metamagia:</b> Aprendes una opción de Metamagia adicional. Además aprendes 1 truco adicional (total 6 trucos)." },
                        { nivel: 11, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 6." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 7." },
                        { nivel: 14, rasgos: "<b>Rasgo de Origen Hechicero:</b> Tu origen te otorga un rasgo adicional (ver subclase)." },
                        { nivel: 15, rasgos: "<b>Nuevos Conjuros:</b> Conoces un conjuro adicional. Ahora tienes acceso a espacios de conjuro de nivel 8." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Metamagia:</b> Aprendes una opción de Metamagia adicional. Ahora tienes acceso a espacios de conjuro de nivel 9." },
                        { nivel: 18, rasgos: "<b>Rasgo de Origen Hechicero:</b> Tu origen te otorga su rasgo final (ver subclase)." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Restablecimiento de Hechicero:</b> Recuperas 4 Puntos de Hechicería gastados siempre que terminas un descanso corto." }
                    ],
                    subclases_titulo: "Orígenes de Hechicero",
                    subclases: [
                        { 
                            nombre: "Línea de Sangre Dracónica", 
                            rasgos: `<p>Tu magia innata proviene de la magia dracónica que fue mezclada con tu sangre o con la de tus ancestros. Más a menudo, los hechiceros con este origen remontan su linaje a un poderoso hechicero antepasado que llegó a un acuerdo con un dragón o que podría incluso haber tenido descendencia con uno. Algunas de estas líneas de sangre son bien establecidas en el mundo, pero la mayoría son oscuras. Cualquier dragón podría ser el progenitor de tal línea.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Tabla: Tipo de Dragón Ancestral</h5>
                            <table style="font-size:0.85em; margin:10px 0;">
                                <tr><th>Dragón</th><th>Tipo de Daño</th><th>Forma del Arma de Aliento</th></tr>
                                <tr><td>Negro</td><td>Ácido</td><td>Línea de 5 × 30 pies (salvación DES)</td></tr>
                                <tr><td>Azul</td><td>Relámpago</td><td>Línea de 5 × 100 pies (salvación DES)</td></tr>
                                <tr><td>Latón</td><td>Fuego</td><td>Línea de 5 × 30 pies (salvación DES)</td></tr>
                                <tr><td>Bronce</td><td>Relámpago</td><td>Línea de 5 × 100 pies (salvación DES)</td></tr>
                                <tr><td>Cobre</td><td>Ácido</td><td>Línea de 5 × 30 pies (salvación DES)</td></tr>
                                <tr><td>Dorado</td><td>Fuego</td><td>Cono de 15 pies (salvación DES)</td></tr>
                                <tr><td>Verde</td><td>Veneno</td><td>Cono de 15 pies (salvación CON)</td></tr>
                                <tr><td>Rojo</td><td>Fuego</td><td>Cono de 15 pies (salvación DES)</td></tr>
                                <tr><td>Plateado</td><td>Frío</td><td>Cono de 15 pies (salvación CON)</td></tr>
                                <tr><td>Blanco</td><td>Frío</td><td>Cono de 15 pies (salvación CON)</td></tr>
                            </table>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 1 — Ancestro Dragón</h5>
                            <p>Eliges un tipo de dragón como tu ancestro (tabla arriba). El tipo de daño asociado a tu dragón se usa en varios de tus rasgos. Puedes hablar, leer y escribir el idioma Dracónico. Además, cuando haces una prueba de Carisma en interacciones con dragones, tu bonificador de competencia se duplica si se aplica a la prueba.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 1 — Resistencia Dracónica</h5>
                            <p>A medida que la magia fluye a través de tu cuerpo, hace aflorar rasgos físicos de tu ancestro dragón. Tu máximo de Puntos de Golpe aumenta en 1 y vuelve a aumentar en 1 cada vez que ganas un nivel en esta clase.<br>Además, partes de tu piel están cubiertas por una fina película de escamas de dragón. Cuando no llevas armadura, tu CA es igual a <b>13 + tu modificador de Destreza</b>.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Afinidad Elemental</h5>
                            <p>Cuando lanzas un conjuro que hace daño del tipo asociado a tu ascendencia dracónica, añades tu modificador de Carisma a una tirada de daño de ese conjuro.<br>Al mismo tiempo, puedes gastar 1 Punto de Hechicería para ganar resistencia a ese tipo de daño durante 1 hora.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Alas de Dragón</h5>
                            <p>Ganas la habilidad de hacer brotar un par de alas de dragón de tu espalda, ganando una velocidad de vuelo igual a tu velocidad actual. Puedes crear las alas como acción adicional en tu turno. Las alas duran hasta que las desestimas como acción adicional en tu turno. No puedes manifestar tus alas mientras lleves armadura, a menos que la armadura esté hecha para acomodarlas, y la ropa hecha sin tenerlas en cuenta podría destruirse al hacerlas aparecer.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 18 — Presencia Draconiana</h5>
                            <p>Puedes canalizar la presencia abrumadora de tu ancestro dragón y hacer que te rodee un aura de sobrecogimiento o terror. Como acción, puedes gastar 5 Puntos de Hechicería para sacar esta presencia draconiana. Durante 1 minuto, o hasta que pierdas tu concentración (como si estuvieras concentrándote en un conjuro), cada criatura hostil que sea consciente de ti dentro de 60 pies de ti debe superar una salvación de Sabiduría contra tu CD de conjuros o quedar hechizada (sobrecogida) o asustada por ti (tú eliges) hasta que el aura termine. Una criatura que tenga éxito en esta tirada de salvación es inmune a tu aura durante 24 horas.</p>`
                        },
                        { 
                            nombre: "Magia Salvaje", 
                            rasgos: `<p>Tu poder mágico innato proviene de las fuerzas salvajes del caos que subyacen en el orden de la creación. Podrías haber sufrido la exposición a alguna forma cruda de magia, quizás a través de un viaje por el plano Limbo, los dominios de Limbo, o una explosión de Magia Antigua. La brecha entre tus deseos y el efecto de tus conjuros puede ser imprevisible; la oleada caótica de la magia puede manifestarse en el momento más inoportuno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 1 — Oleada de Magia Salvaje</h5>
                            <p>Tus intentos de lanzar conjuros pueden desatar una brecha de magia descontrolada. Inmediatamente después de lanzar un conjuro de nivel 1 o superior, el DM puede pedirte que tires un d20. Si sacas un 1, tira en la tabla de Oleada de Magia Salvaje para crear un efecto mágico aleatorio. Una Oleada de Magia Salvaje puede ocurrir una vez por turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 1 — Mareas del Caos</h5>
                            <p>Puedes manipular las fuerzas del azar y el caos para ganar ventaja en una tirada de ataque, prueba de característica o tirada de salvación. Una vez que usas este rasgo, debes terminar un descanso prolongado para volver a usarlo.<br>Alternativamente, el DM puede pedir que hagas una tirada en la tabla de Oleada de Magia Salvaje en cualquier momento antes de que puedas usarlo de nuevo. Si eso ocurre, recuperas el uso de Mareas del Caos.</p>

                            <h5 style="color:#d32f2f; margin-top:12px; border-bottom: 1px solid var(--border-color); padding-bottom:5px;">Tabla de Oleada de Magia Salvaje (d100)</h5>
                            <div style="overflow-x:auto; max-height:400px; overflow-y:auto;">
                            <table style="font-size:0.82em; margin-top:8px;">
                                <tr><th style="width:60px">d100</th><th>Efecto</th></tr>
                                <tr><td>01–02</td><td>Tiras en esta tabla al inicio de cada uno de tus turnos durante 1 minuto, ignorando este resultado en las tiradas posteriores.</td></tr>
                                <tr><td>03–04</td><td>Durante el próximo minuto, puedes ver a cualquier criatura invisible siempre que tengas línea de visión hacia ella.</td></tr>
                                <tr><td>05–06</td><td>Un modron elegido y controlado por el DM aparece en un espacio desocupado a 5 pies de ti y desaparece al cabo de 1 minuto.</td></tr>
                                <tr><td>07–08</td><td>Lanzas <i>bola de fuego</i> como un conjuro de nivel 3 centrado en ti mismo.</td></tr>
                                <tr><td>09–10</td><td>Lanzas <i>proyectil mágico</i> como un conjuro de nivel 5.</td></tr>
                                <tr><td>11–12</td><td>Tira 1d10. Tu estatura cambia en pulgadas igual al resultado. Si es impar, encoges; si es par, creces.</td></tr>
                                <tr><td>13–14</td><td>Lanzas <i>confusión</i> centrado en ti mismo.</td></tr>
                                <tr><td>15–16</td><td>Durante el próximo minuto, recuperas 5 PG al inicio de cada uno de tus turnos.</td></tr>
                                <tr><td>17–18</td><td>Te crece una larga barba de plumas que permanece hasta que estornudas, momento en el que explota desde tu cara.</td></tr>
                                <tr><td>19–20</td><td>Lanzas <i>grasa</i> centrado en ti mismo.</td></tr>
                                <tr><td>21–22</td><td>Las criaturas tienen desventaja en las salvaciones contra el próximo conjuro que lances en el siguiente minuto.</td></tr>
                                <tr><td>23–24</td><td>Tu piel se torna de un tono azul brillante. Un conjuro <i>quitar maldición</i> puede terminar este efecto.</td></tr>
                                <tr><td>25–26</td><td>Un ojo aparece en tu frente durante 1 minuto. Tienes ventaja en pruebas de Percepción que dependan de la vista durante ese tiempo.</td></tr>
                                <tr><td>27–28</td><td>Durante el próximo minuto, todos tus conjuros con tiempo de lanzamiento de 1 acción tienen tiempo de lanzamiento de 1 acción adicional.</td></tr>
                                <tr><td>29–30</td><td>Te teletransportas hasta 60 pies a un espacio desocupado que puedas ver.</td></tr>
                                <tr><td>31–32</td><td>Eres transportado al Plano Astral hasta el final de tu siguiente turno, tras lo cual regresas al espacio que ocupabas antes.</td></tr>
                                <tr><td>33–34</td><td>Maximizas el daño del próximo conjuro dañino que lances en el siguiente minuto.</td></tr>
                                <tr><td>35–36</td><td>Tira 1d10. Tu edad cambia en años igual al resultado (impar = más joven, mínimo 1; par = más viejo).</td></tr>
                                <tr><td>37–38</td><td>1d6 flumphs aparecen en espacios desocupados a 60 pies de ti y están asustados de ti. Desaparecen al cabo de 1 minuto.</td></tr>
                                <tr><td>39–40</td><td>Recuperas 2d10 Puntos de Golpe.</td></tr>
                                <tr><td>41–42</td><td>Te conviertes en una planta en maceta hasta el inicio de tu siguiente turno. Estás incapacitado y tienes vulnerabilidad a todo el daño.</td></tr>
                                <tr><td>43–44</td><td>Durante el próximo minuto, puedes teletransportarte hasta 20 pies como acción adicional en cada uno de tus turnos.</td></tr>
                                <tr><td>45–46</td><td>Lanzas <i>levitar</i> sobre ti mismo.</td></tr>
                                <tr><td>47–48</td><td>Un unicornio controlado por el DM aparece a 5 pies de ti y desaparece al cabo de 1 minuto.</td></tr>
                                <tr><td>49–50</td><td>No puedes hablar durante el próximo minuto. Cada vez que lo intentas, salen burbujas rosadas de tu boca.</td></tr>
                                <tr><td>51–52</td><td>Un escudo espectral flota cerca de ti por 1 minuto, dándote +2 a la CA e inmunidad a <i>proyectil mágico</i>.</td></tr>
                                <tr><td>53–54</td><td>Eres inmune a los efectos de embriaguez por alcohol durante los próximos 5d6 días.</td></tr>
                                <tr><td>55–56</td><td>Se te cae el cabello, pero vuelve a crecer en 24 horas.</td></tr>
                                <tr><td>57–58</td><td>Durante el próximo minuto, cualquier objeto inflamable que toques y que no lleve alguien se incendia.</td></tr>
                                <tr><td>59–60</td><td>Recuperas tu espacio de conjuro de menor nivel gastado.</td></tr>
                                <tr><td>61–62</td><td>Durante el próximo minuto, debes gritar cuando hablas.</td></tr>
                                <tr><td>63–64</td><td>Lanzas <i>niebla espesa</i> centrada en ti mismo.</td></tr>
                                <tr><td>65–66</td><td>Hasta tres criaturas que elijas a 30 pies de ti reciben 4d10 de daño por relámpago.</td></tr>
                                <tr><td>67–68</td><td>Estás asustado de la criatura más cercana a ti hasta el final de tu siguiente turno.</td></tr>
                                <tr><td>69–70</td><td>Cada criatura a 30 pies de ti queda invisible hasta el inicio de tu siguiente turno. La invisibilidad termina sobre una criatura cuando ataca o lanza un conjuro.</td></tr>
                                <tr><td>71–72</td><td>Ganas resistencia a todo el daño durante el próximo minuto.</td></tr>
                                <tr><td>73–74</td><td>Una criatura aleatoria a 60 pies de ti queda envenenada durante 1d4 horas.</td></tr>
                                <tr><td>75–76</td><td>Brillas con luz brillante en un radio de 30 pies durante 1 minuto. Cualquier criatura a 5 pies de ti al final de su turno queda cegada hasta el final de su siguiente turno.</td></tr>
                                <tr><td>77–78</td><td>Lanzas <i>polimorfar</i> sobre ti mismo. Si fallas la salvación, te conviertes en oveja durante la duración.</td></tr>
                                <tr><td>79–80</td><td>Mariposas ilusorias y pétalos de flores revolotean en el aire a 10 pies de ti durante 1 minuto.</td></tr>
                                <tr><td>81–82</td><td>Puedes realizar una acción adicional de inmediato.</td></tr>
                                <tr><td>83–84</td><td>Cada criatura a 30 pies de ti recibe 1d10 de daño necrótico. Recuperas PG iguales a la suma del daño necrótico causado.</td></tr>
                                <tr><td>85–86</td><td>Lanzas <i>imagen duplicada</i>.</td></tr>
                                <tr><td>87–88</td><td>Lanzas <i>volar</i> sobre una criatura aleatoria a 60 pies de ti.</td></tr>
                                <tr><td>89–90</td><td>Te vuelves invisible hasta el inicio de tu siguiente turno, o hasta que ataques o lances un conjuro.</td></tr>
                                <tr><td>91–92</td><td>Si mueres en el próximo minuto, regresas inmediatamente a la vida como si fuera el conjuro <i>reencarnación</i>.</td></tr>
                                <tr><td>93–94</td><td>Tu tamaño aumenta en una categoría durante el próximo minuto.</td></tr>
                                <tr><td>95–96</td><td>Tú y todas las criaturas a 30 pies de ti ganáis vulnerabilidad al daño perforante durante el próximo minuto.</td></tr>
                                <tr><td>97–98</td><td>Quedas rodeado de tenue música etérea que solo tú puedes oír durante el próximo minuto.</td></tr>
                                <tr><td>99–00</td><td>Recuperas todos los Puntos de Hechicería gastados.</td></tr>
                            </table>
                            </div>

                            <h5 style="color:#d32f2f; margin-top:20px;">Nivel 6 — Curvar la Suerte</h5>
                            <p>Adquieres la habilidad de torcer el destino usando tu magia salvaje. Cuando otra criatura que puedas ver hace una tirada de ataque, una prueba de característica o una tirada de salvación, puedes usar tu reacción y gastar 2 Puntos de Hechicería para tirar 1d4 y aplicar el número tirado como bonificador o penalización (a tu elección) a la tirada de la criatura. Puedes hacerlo después de que la criatura tire, pero antes de que se apliquen los efectos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Caos Controlado</h5>
                            <p>Adquieres cierto dominio sobre las oleadas de tu magia. Siempre que tires en la tabla de Oleada de Magia Salvaje, puedes tirar dos veces y usar cualquiera de los dos resultados.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 18 — Bombardeo de Conjuros</h5>
                            <p>La energía de tus conjuros se intensifica. Cuando tiras los dados de daño de un conjuro y obtienes el resultado máximo en alguno de esos dados, puedes tirar ese dado de nuevo y sumar el resultado al daño del conjuro. Solo puedes hacerlo con un dado por conjuro lanzado.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Mago", 
                    descripcion: "Los magos son el epítome de los lanzadores de conjuros eruditos, estudiando durante décadas los secretos de la magia para poder doblar la realidad a su voluntad. Su magia no proviene de un don innato ni de un pacto, sino de años de estudio meticuloso registrado en su libro de conjuros. Son los únicos que pueden copiar y aprender conjuros de rollos y libros, expandiendo su repertorio sin límites.",
                    creacion_rapida: "Inteligencia debería ser tu puntuación más alta, seguida de Constitución o Destreza. Elige el trasfondo Sabio. Para trucos elige Rayo de Llamas y Prestidigitación. Los conjuros de nivel 1 recomendados son Dormir y Proyectil Mágico.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d6 por nivel de mago.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 6 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d6 (o 4) + tu modificador de Constitución por cada nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Ninguna.</li>
                        <li><b>Competencias — Armas:</b> Dagas, dardos, hondas, bastones y ballestas ligeras.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Inteligencia, Sabiduría.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Arcano, Historia, Perspicacia, Investigación, Medicina y Religión.</li>
                        <li><b>Equipo Inicial:</b> (a) bastón o (b) daga. (a) bolsa de componentes o (b) foco arcano. (a) equipo de estudioso o (b) equipo de explorador. Libro de conjuros.</li>
                        <li><b>Característica de Conjuros:</b> Inteligencia. CD de Salvación = 8 + bonificador de competencia + mod. INT. Bonificador de ataque = bonificador de competencia + mod. INT.</li>
                        <li><b>Libro de Conjuros:</b> El mago posee un libro de conjuros que contiene todos sus conjuros. Empieza con 6 conjuros de nivel 1 y añade 2 más a su libro al subir cada nivel de mago. Copiar un conjuro de un rollo externo cuesta 50 po y 2 horas por nivel del conjuro.</li>
                        <li><b>Preparación de Conjuros:</b> Prepara diariamente un número de conjuros de tu libro igual a tu <b>modificador de Inteligencia + nivel de mago</b> (mínimo 1). Cambias los conjuros preparados al finalizar un descanso prolongado.</li>
                        <li><b>Conjuros Rituales:</b> Puedes lanzar como ritual cualquier conjuro de mago que conozcas y que tenga la etiqueta "ritual", sin gastar un espacio de conjuro.</li>
                        <li><b>Foco de Conjuros:</b> Puede usar un foco arcano como foco de sus conjuros.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Mago</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:700px;">
                        <tr><th>Nivel</th><th>Trucos</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th><th>Nv5</th><th>Nv6</th><th>Nv7</th><th>Nv8</th><th>Nv9</th></tr>
                        <tr><td>1</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>3</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>4</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>4</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>4</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>4</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>4</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>4</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>14</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>—</td><td>—</td></tr>
                        <tr><td>15</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>16</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>—</td></tr>
                        <tr><td>17</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>18</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>19</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>1</td><td>1</td><td>1</td></tr>
                        <tr><td>20</td><td>5</td><td>4</td><td>3</td><td>3</td><td>3</td><td>3</td><td>2</td><td>2</td><td>1</td><td>1</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* Los espacios de conjuro se recuperan con un descanso prolongado. El número de conjuros preparados = modificador de INT + nivel de mago.</p>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Lanzamiento de Conjuros:</b> Inteligencia es tu característica de conjuros. Conoces 3 trucos desde el inicio (ganas 1 más a nivel 4, 10 y 18; total 6). Tu libro de conjuros contiene 6 conjuros de nivel 1. Cada día preparas un número de conjuros de tu libro igual a tu modificador de INT + nivel de mago. Puedes lanzar conjuros con etiqueta ritual sin gastar espacio.<br><br><b>Recuperación Arcana:</b> Una vez por día, al terminar un descanso corto, puedes recuperar espacios de conjuro gastados. La suma de niveles recuperados no puede superar la mitad de tu nivel de mago (redondeando hacia arriba), y ningún espacio recuperado puede ser de nivel 6 o superior. Por ejemplo, a nivel 4 puedes recuperar hasta 2 niveles de espacios (ej.: un espacio de nivel 2, o dos espacios de nivel 1)." },
                        { nivel: 2, rasgos: "<b>Tradición Arcana:</b> Eliges una tradición arcana que dé forma a tu práctica de la magia eligiendo entre una de las ocho escuelas de la magia: Abjuración, Adivinación, Conjuración, Encantamiento, Evocación, Ilusión, Nigromancia y Transmutación. Tu elección te otorga rasgos a nivel 2 y de nuevo en los niveles 6, 10 y 14." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características.</b> Además aprendes 1 truco adicional (total 4 trucos)." },
                        { nivel: 6, rasgos: "<b>Rasgo de Tradición Arcana:</b> Recibes un rasgo adicional de tu tradición arcana elegida (ver subclase)." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 10, rasgos: "<b>Rasgo de Tradición Arcana:</b> Recibes un rasgo adicional de tu tradición arcana. Además aprendes 1 truco adicional (total 5 trucos)." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 14, rasgos: "<b>Rasgo de Tradición Arcana:</b> Recibes el rasgo final de tu tradición arcana." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 18, rasgos: "<b>Maestría de Conjuros:</b> Has alcanzado tal dominio sobre ciertos conjuros que puedes lanzarlos a voluntad. Elige un conjuro de mago de nivel 1 y uno de nivel 2 de tu libro. Puedes lanzar esos conjuros a su nivel más bajo sin gastar un espacio de conjuro. Si quieres lanzarlos a un nivel superior, debes usar un espacio de conjuro como de costumbre. Al terminar un descanso prolongado puedes intercambiarlos por otros conjuros de los mismos niveles." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b> Además aprendes 1 truco adicional (total 6 trucos)." },
                        { nivel: 20, rasgos: "<b>Conjuros de Signatura:</b> Alcanzas la cima de la maestría mágica. Elige dos conjuros de mago de nivel 3 de tu libro como tus conjuros de signatura. Siempre los tienes preparados y no cuentan para el límite de conjuros preparados. Puedes lanzar cada uno una vez sin gastar un espacio de conjuro. Cuando lo hagas, debes terminar un descanso corto o prolongado para volver a lanzarlo de esta manera." }
                    ],
                    subclases_titulo: "Tradiciones Arcanas",
                    subclases: [
                        { 
                            nombre: "Escuela de Abjuración", 
                            rasgos: `<p>La escuela de Abjuración hace hincapié en la magia que bloquea, destierra o protege. Los detractores de esta escuela dicen que su tradición trata de negar la magia más que usarla. Tú entiendes, sin embargo, que acaba la batalla antes de que empiece. Esta magia es difensa ardua, no negación.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Abjuración</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Abjuración en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Escudo Arcano</h5>
                            <p>Forjas un escudo mágico de energía protectora que se interpone entre ti y el daño. Cuando lanzas un conjuro de Abjuración de nivel 1 o superior, puedes usar tu reacción para crear un escudo arcano con Puntos de Golpe iguales al doble de tu nivel de mago + tu modificador de Inteligencia. Cuando recibes daño, ese daño se aplica primero al escudo. Si los PG del escudo caen a 0, recibes el daño restante. Si el daño no supera el escudo, recibes 0 daño. El escudo desaparece cuando alcanza 0 PG. Cuando lanzas otro conjuro de Abjuración de nivel 1 o superior, repleneces el escudo hasta su máximo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Escudo Proyectado</h5>
                            <p>Cuando una criatura que puedas ver dentro de 30 pies de ti recibe daño, puedes usar tu reacción para hacer que tu Escudo Arcano absorba ese daño. Si el daño reduce los PG del escudo a 0, la criatura protegida recibe el daño restante.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Abjuración Mejorada</h5>
                            <p>Cuando lanzas un conjuro de Abjuración que requiera una prueba de característica como parte del lanzamiento (como ocurre con <i>contrahechizo</i> y <i>disipar magia</i>), añades tu bonificador de competencia a esa prueba.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Resistencia a Conjuros</h5>
                            <p>Tienes ventaja en las tiradas de salvación contra conjuros. Además, tienes resistencia al daño de los conjuros.</p>`
                        },
                        { 
                            nombre: "Escuela de Adivinación", 
                            rasgos: `<p>La búsqueda del conocimiento es el centro de la filosofía de un adepto de la Adivinación. Reconocen que el conocimiento es poder y buscan enterarse de secretos y descubrir la verdad antes que cualquier otra cosa. Lo que otros llaman fortuna, los adivinos lo llaman visión.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Adivinación</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Adivinación en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Presagio</h5>
                            <p>Atisbos del futuro comienzan a filtrarse en tu conciencia. Cuando terminas un descanso prolongado, tira dos d20 y anota los resultados. Puedes reemplazar cualquier tirada de ataque, prueba de característica o tirada de salvación hecha por ti o por una criatura que puedas ver con uno de esos dados de presagio. Debes elegir hacerlo antes de que se tire, y solo puedes usar cada tirada de presagio una vez. Cuando terminas el siguiente descanso prolongado, pierdes los dados de presagio no usados.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Adivinación Experta</h5>
                            <p>Lanzar conjuros de Adivinación se vuelve tan fácil que apenas cuesta esfuerzo. Cuando lanzas un conjuro de Adivinación de nivel 2 o superior usando un espacio de conjuro, recuperas un espacio de conjuro gastado. El espacio que recuperas debe ser de un nivel inferior al conjuro que acabas de lanzar y no puede ser de nivel 6 o superior.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — El Tercer Ojo</h5>
                            <p>Puedes usar tu acción para aumentar tus poderes de percepción. Cuando lo haces, elige uno de los siguientes beneficios que dura hasta que seas incapacitado o hasta que termines un descanso corto o prolongado. No puedes usar el rasgo de nuevo hasta entonces.</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Visión en la Oscuridad:</b> Obtienes visión en la oscuridad de 60 pies.</li>
                                <li><b>Visión Etérea:</b> Puedes ver en el Plano Etéreo a 60 pies de ti.</li>
                                <li><b>Mayor Comprensión:</b> Puedes leer cualquier idioma.</li>
                                <li><b>Ver lo Invisible:</b> Puedes ver criaturas y objetos invisibles a 10 pies de ti siempre que tengas línea de visión a ellos.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Presagio Mayor</h5>
                            <p>Tu característica de Presagio mejora. A partir de ahora, tiras tres d20 en lugar de dos cuando usas Presagio.</p>`
                        },
                        { 
                            nombre: "Escuela de Conjuración", 
                            rasgos: `<p>Como adepto de la Conjuración, te especializas en traer criaturas y objetos de otros planos de existencia. Los conjuradores también aprenden a transportarse a sí mismos instantáneamente de un lugar a otro.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Conjuración</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Conjuración en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Conjuración Menor</h5>
                            <p>Puedes usar tu acción para conjurar un objeto inanimado no mágico en tu mano o en el suelo en un espacio desocupado a 10 pies de ti. El objeto no puede ser mayor de 3 pies en ninguna dimensión y no puede pesar más de 10 libras. El objeto tiene que ser de algo que hayas visto. El objeto desaparece después de 1 hora, cuando lo desestimas, o cuando estás a más de 10 pies de él.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Transposición Benigna</h5>
                            <p>Puedes teletransportarte hasta 30 pies a un espacio desocupado que puedas ver como acción adicional en tu turno. Alternativamente, puedes elegir un espacio dentro del alcance que esté ocupado por una criatura voluntaria de tamaño pequeño o mediano: ambos os teletransportáis intercambiando lugares. Una vez usas este rasgo, debes terminar un descanso corto o prolongado para volver a usarlo, a menos que gastes un espacio de conjuro de nivel 1 o superior.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Conjuración Concentrada</h5>
                            <p>Tu concentración no puede romperse como resultado de recibir daño mientras estás concentrado en un conjuro de Conjuración.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Invocaciones Duraderas</h5>
                            <p>Las criaturas que conjuras o creas son más robustas que las de otros magos. Toda criatura que convocas o creas con un conjuro de Conjuración gana 30 Puntos de Golpe temporales.</p>`
                        },
                        { 
                            nombre: "Escuela de Encantamiento", 
                            rasgos: `<p>Como miembro de la Escuela de Encantamiento, has perfeccionado tu arte para cautivar, persuadir y controlar las mentes de los demás. Algunos encantadores son manipuladores benévolos que usan su magia para impulsar a otros. Otros son tiránicos déspotas que esclavizan a quien desean.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Encantamiento</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Encantamiento en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Mirada Hipnótica</h5>
                            <p>Usando palabras gentiles y una mirada penetrante, puedes encantar hechizantemente a una criatura. Como acción, elige a una criatura que puedas ver dentro de 5 pies de ti. Si el objetivo puede verte, debe superar una tirada de salvación de Sabiduría contra tu CD de conjuros o quedar encantada por ti hasta el final de tu siguiente turno. La criatura encantada está aturdida. En cada uno de tus turnos puedes usar tu acción para mantener este efecto, extendiendo su duración hasta el final de tu siguiente turno. El efecto termina si te mueves a más de 5 pies de la criatura, si no puedes verla o si ella tiene éxito en una tirada de salvación. Una vez el efecto termina, o si la criatura supera su tirada de salvación inicial, no puedes usar esta característica en esa criatura durante 24 horas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Encanto Instintivo</h5>
                            <p>Cuando una criatura que puedas ver dentro de 30 pies de ti hace una tirada de ataque contra ti, puedes usar tu reacción para desviar el ataque, siempre que otra criatura esté dentro del alcance del ataque. El atacante debe realizar una tirada de salvación de Sabiduría contra tu CD de conjuros. Si falla, el atacante debe atacar a la criatura más cercana que no seas tú o el atacante. Si varias criaturas son igual de cercanas, el atacante elige. Si tiene éxito, no puedes usar este rasgo en él durante 24 horas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Encantamiento Dividido</h5>
                            <p>Cuando lanzas un conjuro de Encantamiento de nivel 1 o superior que afecta solo a una criatura, puedes hacer que afecte a una segunda criatura. (Los conjuros de encantamiento que atacan múltiples objetivos, como <i>atracción de personas</i>, no se benefician de este rasgo).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Alterar Recuerdos</h5>
                            <p>Adquieres la habilidad de hacer que una criatura tenga recuerdos alterados de sus experiencias contigo. Cuando un conjuro de Encantamiento que has lanzado termina, puedes usar tu acción principal para intentar suprimir el recuerdo de la criatura de que estaba encantada. La criatura debe superar una tirada de salvación de Inteligencia contra tu CD de conjuros. Si falla, no recuerda haber estado encantada. Tienes 1 minuto para usar este rasgo una vez que el conjuro termina. Además, puedes alterar el recuerdo de la criatura sobre la última hora para hacerla creer que estaba con ella amigablemente durante ese tiempo (aunque en realidad no estabas).</p>`
                        },
                        { 
                            nombre: "Escuela de Evocación", 
                            rasgos: `<p>Te enfocas en la magia que crea efectos de energía poderosos como fuego abrasador, rayos chispeantes, frío helador o energía ácida corrosiva. Algunos evocadores encuentran empleo en los ejércitos militares, lanzando bolas de fuego a las fuerzas enemigas. Otros usan su inmenso poder para proteger a los débiles.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Evocación</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Evocación en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Esculpir Conjuros</h5>
                            <p>Puedes crear bolsillos de relativa seguridad dentro de los efectos de tus conjuros de evocación. Cuando lanzas un conjuro de Evocación que afecta a otras criaturas que puedas ver, puedes elegir a un número de ellas igual a 1 + el nivel del conjuro. Las criaturas elegidas superan automáticamente sus tiradas de salvación contra el conjuro y no reciben daño si normalmente recibirían la mitad de daño en una tirada de salvación exitosa.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Truco Potente</h5>
                            <p>Tus trucos dañinos afectan incluso a las criaturas que evitan su peor impacto. Cuando una criatura supera una tirada de salvación contra un truco tuyo, la criatura toma la mitad del daño del truco (si lo hay) pero no sufre ningún efecto adicional del truco.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Evocación Potenciada</h5>
                            <p>Añades tu modificador de Inteligencia al daño de cualquier conjuro de Evocación de mago que lances.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Sobrecanalizar</h5>
                            <p>Puedes aumentar la potencia de tus conjuros más simples. Cuando lanzas un conjuro de Evocación de mago de nivel 1 a 5, puedes hacer que cause el daño máximo. La primera vez que lo haces, no sufres ningún efecto adverso. Si usas esta característica de nuevo antes de terminar un descanso prolongado, sufres 2d12 de daño necrótico por nivel del conjuro, inmediatamente después de lanzarlo. Cada vez que lo usas de nuevo antes de descansar, el daño por dado aumenta en 1d12. Ese daño no puede ser reducido ni evitado de ninguna manera.</p>`
                        },
                        { 
                            nombre: "Escuela de Ilusión", 
                            rasgos: `<p>Te enfocas en la magia que deslumbra los sentidos, confunde la mente y engaña incluso a las criaturas más listas. Tu magia es sutil, pero los maestros de esta escuela pronto aprenden que una ilusión tomada por real es tan peligrosa como cualquier amenaza física.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Ilusión</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Ilusión en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Ilusión Menor Mejorada</h5>
                            <p>Aprendes el truco <i>ilusión menor</i> si no lo conoces ya. Cuando lo lanzas, puedes crear tanto un sonido como una imagen con un solo lanzamiento del conjuro.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Ilusiones Maleables</h5>
                            <p>Cuando lanzas un conjuro de Ilusión que requiere concentración, puedes usar tu acción para cambiar la naturaleza de esa ilusión (usando las reglas normales del conjuro para la ilusión), siempre que puedas ver la ilusión.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Doble Ilusorio</h5>
                            <p>Puedes crear un doble ilusorio de ti mismo como reacción instantánea a un peligro. Cuando una criatura hace una tirada de ataque contra ti, puedes usar tu reacción para interponer el doble ilusorio entre el atacante y tú. El ataque falla automáticamente y el doble se disipa. Una vez usas esta característica, debes terminar un descanso corto o prolongado para volver a usarla.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Realidad Ilusoria</h5>
                            <p>Aprendes el secreto de entrelazar la magia de las sombras con tus ilusiones para darles una semirealidad temporal. Cuando lanzas un conjuro de Ilusión de nivel 1 o superior, puedes elegir un objeto inanimado e inanime dentro de la ilusión y hacerlo real. Puedes hacerlo en el momento del lanzamiento o como acción adicional mientras te concentras en el conjuro. El objeto permanece real durante 1 minuto. El objeto no puede infligir daño ni imponer condiciones a las criaturas directamente.</p>`
                        },
                        { 
                            nombre: "Escuela de Nigromancia", 
                            rasgos: `<p>La Escuela de Nigromancia explora las fuerzas cósmicas de la vida, la muerte y la no-muerte. Mientras que algunos usan esta magia para hacer el bien (curar y restaurar), la mayoría prefiere manipular la energía vital para crear muertos vivientes y doblegar la muerte misma.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Nigromancia</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Nigromancia en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Cosecha Sombría</h5>
                            <p>Obtienes la capacidad de recolectar energía vital de las criaturas que matas con tus conjuros. Una vez por turno cuando matas a una o más criaturas con un conjuro de nivel 1 o superior, recuperas puntos de golpe iguales al doble del nivel del conjuro, o tres veces el nivel del conjuro si es un conjuro de Nigromancia. No ganas estos puntos de golpe al matar muertos vivientes o constructos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Secuaces No Muertos</h5>
                            <p>Empiezas a añadir tu marca a los muertos vivientes que creas. Cuando lanzas <i>animar muertos</i>, puedes animar un cadáver o conjunto de huesos adicional (total 4 en lugar de 3). Además, los muertos vivientes creados por tus conjuros de Nigromancia tienen los siguientes beneficios:<br>• El máximo de PG de la criatura aumenta en una cantidad igual a tu nivel de mago.<br>• La criatura añade tu bonificador de competencia a sus tiradas de daño con armas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Templado en la No-Muerte</h5>
                            <p>Tienes resistencia al daño necrótico y tu máximo de puntos de golpe no puede reducirse.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Comandar No Muertos</h5>
                            <p>Puedes usar la magia para colocar a los muertos vivientes bajo tu control, incluso aquellos creados por otros magos. Como acción, eliges a un muerto viviente que puedas ver dentro de 60 pies de ti. Ese ser debe hacer una tirada de salvación de Sabiduría contra tu CD de conjuros. Si tiene éxito, no puedes usar este rasgo en él de nuevo durante 24 horas. Si falla, queda bajo tu control durante 24 horas o hasta que lo uses de nuevo en otro muerto viviente. Los muertos vivientes con INT de 8 o superior hacen la tirada con desventaja.</p>`
                        },
                        { 
                            nombre: "Escuela de Transmutación", 
                            rasgos: `<p>Eres un estudiante de la magia que modifica la energía y la materia. Para ti, el mundo no es un lugar fijo sino algo que puede transformarse de manera fundamental. La Transmutación es la magia que hace posible tales cambios, y tú eres el agente de ese cambio.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Erudito en Transmutación</h5>
                            <p>El tiempo y el dinero que gastas cuando copias un conjuro de Transmutación en tu libro de conjuros se reduce a la mitad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 2 — Alquimia Menor</h5>
                            <p>Puedes alterar temporalmente las propiedades físicas de un objeto no mágico, cambiando una sustancia en otra. Realizas un procedimiento alquímico especial sobre un objeto compuesto de madera, piedra (pero no una gema preciosa), hierro, cobre o plata durante 10 minutos para transformarlo en un material diferente. Por ejemplo, puedes convertir madera en piedra. Mientras mantienes tu concentración, el objeto permanece en su nuevo estado. Si te mueves a más de 30 pies del objeto o si pierdes la concentración, el objeto vuelve a su estado original (máximo 1 hora).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Piedra del Transmutar</h5>
                            <p>Puedes pasar 8 horas creando una piedra del transmutar que almacena magia transmutadora. Puedes beneficiarte de la piedra tú mismo o dársela a otra criatura. Una criatura se beneficia de la piedra solo si la tiene en su posesión. Solo puedes tener una piedra a la vez. La piedra otorga uno de los siguientes beneficios a su elección cuando la creas (puedes cambiarlo como acción adicional cuando lanzas un conjuro de transmutación):</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li>Visión en la oscuridad de 60 pies.</li>
                                <li>Un incremento de 10 pies a la velocidad mientras no uses armadura pesada.</li>
                                <li>Competencia en tiradas de salvación de Constitución.</li>
                                <li>Resistencia a daño de ácido, relámpago, frío, fuego o trueno (elige al crear la piedra).</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 10 — Cambiante</h5>
                            <p>Añades el conjuro <i>polimorfar</i> a tu libro si no está ya en él. Puedes lanzar <i>polimorfar</i> sobre ti mismo sin gastar un espacio de conjuro; sin embargo, transformándote en una bestia de CR igual o inferior a tu nivel de mago dividido entre 4 (redondeando hacia abajo). Una vez usas este rasgo, debes terminar un descanso corto o prolongado para volver a usarlo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 14 — Gran Transmutar</h5>
                            <p>Puedes usar tu acción para consumir las reservas mágicas de tu Piedra del Transmutar en un poderoso efecto de transmutación. Cuando lo haces, la piedra es destruida y elige uno de los siguientes efectos:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Transmutación Mayor:</b> Transforma hasta 5 pies cúbicos de material no mágico en otro tipo de materia no mágica.</li>
                                <li><b>Curación Completa:</b> Eliminas todas las enfermedades y venenos de una criatura que toques, o levantas una maldición que la afecte.</li>
                                <li><b>Resucitar:</b> Lanzas el conjuro <i>resucitar</i> sin gastar un espacio de conjuro ni necesitar componentes materiales.</li>
                                <li><b>Transmutación Heroica:</b> Una criatura voluntaria que toques obtiene inmunidad a un tipo de daño de tu elección durante 1 hora.</li>
                            </ul>`
                        }
                    ]
                },
                { 
                    nombre: "Monje", 
                    descripcion: "Los monjes son maestros de las artes marciales que canalizan una misteriosa energía llamada ki. Esta energía fluye a través de los seres vivos y los monjes aprenden a aprovecharla para potenciar sus golpes, moverse con velocidad sobrenatural y resistir el daño. Combinan una velocidad y resistencia inigualables con una disciplina mental férrea, convirtiéndose en combatientes devastadores sin necesitar armas ni armaduras.",
                    creacion_rapida: "Primero, Destreza debe ser tu puntuación más alta, seguida de Sabiduría. Segundo, escoge el trasfondo de Ermitaño.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de monje.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d8 (o 5) + tu modificador de Constitución por nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Ninguna.</li>
                        <li><b>Competencias — Armas:</b> Armas simples y espadas cortas. Las <b>armas de monje</b> son espadas cortas y cualquier arma simple que no tenga la propiedad «a dos manos» ni «pesada».</li>
                        <li><b>Competencias — Herramientas:</b> Elige entre un tipo de herramienta de artesano o un instrumento musical.</li>
                        <li><b>Tiradas de Salvación:</b> Fuerza, Destreza.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Acrobacias, Atletismo, Historia, Perspicacia, Religión y Sigilo.</li>
                        <li><b>Equipo Inicial:</b> (a) espada corta o (b) arma simple. (a) equipo de saqueador de mazmorras o (b) equipo de explorador. 10 dardos.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla del Monje</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:560px;">
                        <tr><th>Nivel</th><th>Bon. Comp.</th><th>Artes Marciales</th><th>Puntos de Ki</th><th>Movimiento sin Armadura</th></tr>
                        <tr><td>1</td><td>+2</td><td>1d4</td><td>—</td><td>—</td></tr>
                        <tr><td>2</td><td>+2</td><td>1d4</td><td>2</td><td>+10 pies</td></tr>
                        <tr><td>3</td><td>+2</td><td>1d4</td><td>3</td><td>+10 pies</td></tr>
                        <tr><td>4</td><td>+2</td><td>1d4</td><td>4</td><td>+10 pies</td></tr>
                        <tr><td>5</td><td>+3</td><td>1d6</td><td>5</td><td>+10 pies</td></tr>
                        <tr><td>6</td><td>+3</td><td>1d6</td><td>6</td><td>+15 pies</td></tr>
                        <tr><td>7</td><td>+3</td><td>1d6</td><td>7</td><td>+15 pies</td></tr>
                        <tr><td>8</td><td>+3</td><td>1d6</td><td>8</td><td>+15 pies</td></tr>
                        <tr><td>9</td><td>+4</td><td>1d6</td><td>9</td><td>+15 pies</td></tr>
                        <tr><td>10</td><td>+4</td><td>1d6</td><td>10</td><td>+20 pies</td></tr>
                        <tr><td>11</td><td>+4</td><td>1d8</td><td>11</td><td>+20 pies</td></tr>
                        <tr><td>12</td><td>+4</td><td>1d8</td><td>12</td><td>+20 pies</td></tr>
                        <tr><td>13</td><td>+5</td><td>1d8</td><td>13</td><td>+20 pies</td></tr>
                        <tr><td>14</td><td>+5</td><td>1d8</td><td>14</td><td>+25 pies</td></tr>
                        <tr><td>15</td><td>+5</td><td>1d8</td><td>15</td><td>+25 pies</td></tr>
                        <tr><td>16</td><td>+5</td><td>1d8</td><td>16</td><td>+25 pies</td></tr>
                        <tr><td>17</td><td>+6</td><td>1d10</td><td>17</td><td>+25 pies</td></tr>
                        <tr><td>18</td><td>+6</td><td>1d10</td><td>18</td><td>+30 pies</td></tr>
                        <tr><td>19</td><td>+6</td><td>1d10</td><td>19</td><td>+30 pies</td></tr>
                        <tr><td>20</td><td>+6</td><td>1d10</td><td>20</td><td>+30 pies</td></tr>
                    </table>
                    </div>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Defensa sin Armadura:</b> Mientras no lleves armadura ni uses escudo, tu CA es igual a 10 + tu modificador de Destreza + tu modificador de Sabiduría.<br><br><b>Artes Marciales:</b> Tu práctica de las artes marciales te permite dominar el combate con ataques desarmados y armas de monje. Ganas los siguientes beneficios mientras estás desarmado o solo empuñas armas de monje y no llevas armadura ni escudo:<ul style='margin:6px 0 0 20px; line-height:1.8'><li>Puedes usar Destreza en lugar de Fuerza para las tiradas de ataque y daño.</li><li>Puedes tirar un dado de Artes Marciales (ver tabla) en lugar del daño normal del arma o del golpe desarmado.</li><li>Cuando usas la acción de Atacar con un ataque desarmado o con un arma de monje en tu turno, puedes hacer un ataque desarmado adicional como acción adicional. Por ejemplo, si usas la acción de Atacar y golpeas con un bastón, también puedes hacer un ataque desarmado como acción adicional.</li></ul>" },
                        { nivel: 2, rasgos: "<b>Ki:</b> Tu entrenamiento te permite aprovechar la energía mística del ki. Tu acceso a esta energía se refleja en un número de <b>Puntos de Ki</b> (igual a tu nivel de monje). Los recuperas todos al terminar un descanso corto o prolongado. Puedes gastar puntos de ki para alimentar las siguientes características:<ul style='margin:6px 0 0 20px; line-height:1.8'><li><b>Ráfaga de Golpes (1 ki):</b> Acción adicional para hacer dos ataques desarmados.</li><li><b>Defensa Paciente (1 ki):</b> Acción adicional para realizar la acción de Esquivar.</li><li><b>Andar del Viento (1 ki):</b> Acción adicional para realizar la acción de Alejarse o de Correr. Tu distancia de salto se duplica durante este turno.</li></ul><br><b>Movimiento sin Armadura:</b> Tu velocidad aumenta en 10 pies mientras no lleves armadura ni escudo (ver tabla). A nivel 9, también puedes correr por superficies verticales y sobre líquidos en tu turno sin caer." },
                        { nivel: 3, rasgos: "<b>Tradición Monástica:</b> Te comprometes con una tradición monástica: Camino de la Mano Abierta, Camino de la Sombra o Camino de los Cuatro Elementos. Tu tradición te otorga rasgos a nivel 3 y de nuevo en los niveles 6, 11 y 17.<br><br><b>Desviar Proyectiles:</b> Puedes usar tu reacción para desviar o atrapar el proyectil cuando te golpea un ataque de arma a distancia. Cuando lo haces, el daño recibido se reduce en 1d10 + tu modificador de Destreza + tu nivel de monje. Si reduces el daño a 0, puedes atrapar el proyectil si cabe en una mano y tienes una mano libre. Si lo atrapas, puedes gastar 1 punto de ki para hacer un ataque a distancia con el proyectil o el arma (alcance 20/60 pies) como parte de la misma reacción." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características.</b><br><br><b>Caída Lenta:</b> Puedes usar tu reacción cuando caes para reducir el daño por caída que sufres en una cantidad igual a cinco veces tu nivel de monje." },
                        { nivel: 5, rasgos: "<b>Ataque Extra:</b> Puedes atacar dos veces, en lugar de una, cada vez que uses la acción de Atacar en tu turno.<br><br><b>Golpe Aturdidor:</b> Puedes interferir en el flujo de ki de un enemigo. Cuando golpeas a una criatura con un ataque de arma c/c, puedes gastar 1 punto de ki para intentar un golpe aturdidor. El objetivo debe superar una tirada de salvación de Constitución contra tu CD de conjuros de ki (8 + bonificador de competencia + modificador de SAB). Si falla, queda <i>aturdido</i> hasta el final de tu siguiente turno." },
                        { nivel: 6, rasgos: "<b>Golpes Potenciados con Ki:</b> Tus ataques desarmados cuentan como mágicos a efectos de superar la resistencia e inmunidad a ataques y daños no mágicos.<br><br><b>Rasgo de Tradición Monástica:</b> Recibes un rasgo de tu tradición monástica (ver subclase)." },
                        { nivel: 7, rasgos: "<b>Evasión:</b> Tu agilidad instintiva te permite esquivar ciertos efectos de área. Cuando estás sujeto a un efecto que te permite hacer una tirada de salvación de Destreza para recibir solo la mitad del daño, en su lugar no recibes daño si tienes éxito en la tirada de salvación y solo recibes la mitad del daño si fallas.<br><br><b>Quietud de la Mente:</b> Puedes usar tu acción para terminar un efecto sobre ti mismo que te cause el estado de <i>encantado</i> o de <i>asustado</i>." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Movimiento sin Armadura (mejora):</b> Ahora puedes correr por paredes verticales y sobre superficies líquidas durante tu turno sin caer al terminar tu movimiento." },
                        { nivel: 10, rasgos: "<b>Pureza del Cuerpo:</b> Tu dominio del ki que fluye a través de tu cuerpo te hace inmune a la enfermedad y al veneno." },
                        { nivel: 11, rasgos: "<b>Rasgo de Tradición Monástica:</b> Recibes un rasgo adicional de tu tradición monástica (ver subclase)." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Lengua del Sol y la Luna:</b> Aprendes a tocar el ki de otras mentes para que puedas comprender todos los idiomas hablados. Además, cualquier criatura que pueda entender un idioma puede entenderte." },
                        { nivel: 14, rasgos: "<b>Alma Diamantina:</b> Tu dominio del ki te otorga competencia en todas las tiradas de salvación. Además, cuando haces una tirada de salvación, puedes gastar 1 punto de ki para repetirla. Debes usar el segundo resultado." },
                        { nivel: 15, rasgos: "<b>Cuerpo Imperecedero:</b> Tu ki te sostiene frente a las penurias que de otro modo te matarían. Ganas los siguientes beneficios:<ul style='margin:6px 0 0 20px; line-height:1.8'><li>Solo necesitas 1 hora de sueño durante un descanso prolongado.</li><li>No puedes envejecer mágicamente. Aunque aún puedes morir de vejez, el ritmo en que envejeces es 10 veces más lento de lo normal.</li><li>Ya no necesitas comida ni agua.</li><li>Eres inmune a los efectos de la privación extrema de calor o frío.</li></ul>" },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Rasgo de Tradición Monástica:</b> Recibes el rasgo final de tu tradición monástica (ver subclase)." },
                        { nivel: 18, rasgos: "<b>Cuerpo Vacío:</b> Puedes usar tu acción para gastar 4 puntos de ki y volverte invisible durante 1 minuto. Durante ese tiempo también tienes resistencia a todo el daño excepto el daño de Fuerza. Además, puedes gastar 8 puntos de ki para lanzar el conjuro <i>proyección astral</i> sin necesitar componentes materiales. No puedes llevar a otras criaturas contigo cuando lo lanzas." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Perfección de Uno Mismo:</b> Cuando tiras iniciativa y no te quedan puntos de ki, recuperas 4 puntos de ki." }
                    ],
                    subclases_titulo: "Tradiciones Monásticas",
                    subclases: [
                        { 
                            nombre: "Camino de la Mano Abierta", 
                            rasgos: `<p>Los monjes del Camino de la Mano Abierta son los maestros definitivos del combate de artes marciales, tanto armados como desarmados. Aprenden técnicas para empujar y derribar a sus oponentes, manipulan el ki para curar el daño en sus cuerpos y practican una meditación avanzada que puede protegerlos del daño.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Técnica de la Mano Abierta</h5>
                            <p>Puedes manipular el ki de tu enemigo cuando golpeas con la Ráfaga de Golpes. Siempre que golpeas a una criatura con uno de los ataques concedidos por tu Ráfaga de Golpes, puedes imponerle uno de los siguientes efectos:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li>Debe superar una tirada de salvación de Destreza o quedar <i>derribada</i> (tumbada).</li>
                                <li>Debe superar una tirada de salvación de Fuerza o ser empujada hasta 15 pies de ti.</li>
                                <li>No puede usar reacciones hasta el inicio de tu siguiente turno.</li>
                            </ul>
                            <p>La CD para todas estas salvaciones es 8 + tu bonificador de competencia + tu modificador de Sabiduría.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Integridad del Cuerpo</h5>
                            <p>Ganas la capacidad de curarte a ti mismo. Como acción, puedes recuperar Puntos de Golpe iguales a tres veces tu nivel de monje. Debes terminar un descanso prolongado para poder usar este rasgo de nuevo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 11 — Tranquilidad</h5>
                            <p>Puedes entrar en un estado especial de meditación que te rodea de un aura de paz. Al final de un descanso prolongado, obtienes el efecto del conjuro <i>santuario</i> que dura hasta el inicio de tu siguiente descanso prolongado (el conjuro puede terminar antes de tiempo, según de costumbre). La CD de la tirada de salvación para el conjuro es igual a 8 + tu modificador de Sabiduría + tu bonificador de competencia.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 17 — Palma Temblorosa</h5>
                            <p>Ganas la capacidad de establecer vibraciones letales en el cuerpo de alguien. Cuando golpeas a una criatura con un ataque desarmado, puedes gastar 3 puntos de ki para iniciar estas vibraciones impalpables, que duran un número de días igual a tu nivel de monje. Las vibraciones son inofensivas a menos que elijas terminarlas, y podrás hacerlo mientras la criatura esté en el mismo plano de existencia que tú. Para ello, usas tu acción y la criatura debe hacer una tirada de salvación de Constitución. Si falla, se reduce a 0 Puntos de Golpe. Si tiene éxito, recibe 10d10 de daño necrótico. Solo puedes tener una criatura bajo el efecto de este rasgo a la vez. Puedes elegir terminar las vibraciones inofensivamente sin gastar una acción.</p>`
                        },
                        { 
                            nombre: "Camino de la Sombra", 
                            rasgos: `<p>Los monjes del Camino de la Sombra siguen una tradición que valora la discreción y el sigilo. Estos monjes podrían llamarse a sí mismos ninjas o shinobi. Cuando terminan su formación, los monjes de la sombra son maestros del sigilo de combate, siendo oscuros intermediarios o asesinos que se desplazan como fantasmas.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Artes Sombrías</h5>
                            <p>Puedes usar tu ki para duplicar los efectos de ciertos conjuros. Como acción, puedes gastar 2 puntos de ki para lanzar <i>oscuridad</i>, <i>visión en la oscuridad</i>, <i>pasar sin dejar rastro</i> o <i>silencio</i>, sin necesitar componentes materiales. Además, obtienes el truco <i>ilusión menor</i> si no lo conoces ya.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 6 — Paso Sombrío</h5>
                            <p>Ganas la habilidad de pasar de una sombra a otra. Cuando estás en penumbra u oscuridad, como acción adicional puedes teletransportarte hasta 60 pies a un espacio desocupado que también esté en penumbra u oscuridad. Tienes ventaja en el primer ataque c/c que hagas antes del final del turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 11 — Capa de Sombras</h5>
                            <p>Aprendes a volverte uno con las sombras. Cuando estás en una zona de penumbra u oscuridad, puedes usar tu acción para volverte invisible. Permaneces invisible hasta que ataques, lances un conjuro, o estés en un área de luz brillante.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 17 — Oportunista</h5>
                            <p>Puedes aprovechar la distracción de un enemigo para golpear sin piedad. Cuando una criatura dentro de 5 pies de ti es golpeada por un ataque hecho por alguien que no eres tú, puedes usar tu reacción para hacer un ataque c/c contra esa criatura.</p>`
                        },
                        { 
                            nombre: "Camino de los Cuatro Elementos", 
                            rasgos: `<p>Sigues una tradición monástica que te enseña a dominar los cuatro elementos. Estos monjes ven el ki como un medio para canalizar el poder de los elementos primordiales, lo que les permite tratar las fuerzas de la creación como extensiones de sí mismos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Discípulo de los Elementos</h5>
                            <p>Aprendes disciplinas mágicas que aprovechan la magia elemental. Una disciplina requiere que gastes puntos de ki cada vez que la usas. Aprendes la disciplina de <b>Sintonía Elemental</b> y dos disciplinas elementales adicionales de tu elección, listadas abajo.</p>
                            <p>Aprendes una disciplina elemental adicional de tu elección en los niveles <b>6, 11 y 17</b>. Siempre que aprendes una disciplina nueva, también puedes reemplazar una que conozcas por otra diferente. Algunas disciplinas requieren un nivel de monje mínimo para aprenderlas.</p>
                            <p><b>Lanzamiento de conjuros:</b> Algunas disciplinas te permiten lanzar conjuros. Tu característica de conjuros es Sabiduría para estas disciplinas, y usas tu CD de ki (8 + bonificador de competencia + modificador de SAB). No necesitas componentes materiales para estos conjuros.</p>

                            <h5 style="color:#d32f2f; margin-top:12px;">Disciplinas Elementales</h5>
                            <div class="card-grid" style="margin-top:10px;">
                                <div class="card"><h3>Sintonía Elemental</h3><span class="tag">Gratis</span><p>Puedes usar tu acción para hacer que uno de los siguientes efectos ocurra brevemente: una brisa suave, llamas menores que no dañan, pequeñas ondas en el agua, tierra y piedra temblar ligeramente. Efectos cosméticos que duran 1 minuto.</p></div>
                                <div class="card"><h3>Colmillos de la Serpiente de Fuego</h3><span class="tag">1 ki</span><p>Cuando usas la acción de Atacar, puedes gastar 1 ki para que uno de tus ataques tenga un alcance de 10 pies adicionales. Si golpeas, el objetivo recibe 1d10 daño de fuego adicional.</p></div>
                                <div class="card"><h3>Puño de los Cuatro Truenos</h3><span class="tag">2 ki</span><p>Lanzas el conjuro <i>onda de trueno</i>.</p></div>
                                <div class="card"><h3>Puño del Aire Indómito</h3><span class="tag">2 ki</span><p>Creas una ráfaga de aire comprimido que golpea a una criatura a 30 pies. Debe superar una salvación de Fuerza o recibir 3d10 de daño contundente y ser empujada 20 pies. Si falla por 5 o más, también queda derribada.</p></div>
                                <div class="card"><h3>Ímpetu de los Espíritus del Vendaval</h3><span class="tag">2 ki</span><p>Lanzas el conjuro <i>ráfaga de viento</i>.</p></div>
                                <div class="card"><h3>Moldear el Río Fluyente</h3><span class="tag">2 ki</span><p>Como acción, afectas hasta 30 pies cúbicos de agua y hielo dentro de 120 pies de ti: puedes moverlos, cambiar su forma, congelarlos o derretirlos. El efecto dura 1 minuto.</p></div>
                                <div class="card"><h3>Golpe de Cinders Barredor</h3><span class="tag">2 ki</span><p>Lanzas el conjuro <i>manos ardientes</i>.</p></div>
                                <div class="card"><h3>Látigo de Agua</h3><span class="tag">2 ki</span><p>Como acción adicional o reacción cuando recibes daño, creas un látigo de agua. Haces una tirada de ataque a distancia (alcance 30 pies) con tu bonificador de ataque desarmado. Si golpeas, hace 3d10 daño contundente y el objetivo debe superar una salvación de Destreza o ser derribado o arrastrado 25 pies hacia ti (elige).</p></div>
                                <div class="card"><h3>Garra del Viento del Norte</h3><span class="tag" style="background:#6a1a1a">3 ki · Nv 6</span><p>Lanzas el conjuro <i>sostener persona</i>.</p></div>
                                <div class="card"><h3>Gong de la Cumbre</h3><span class="tag" style="background:#6a1a1a">3 ki · Nv 6</span><p>Lanzas el conjuro <i>estrépito</i>.</p></div>
                                <div class="card"><h3>Llamas del Fénix</h3><span class="tag" style="background:#6a1a1a">4 ki · Nv 11</span><p>Lanzas el conjuro <i>bola de fuego</i>.</p></div>
                                <div class="card"><h3>Postura de la Niebla</h3><span class="tag" style="background:#6a1a1a">4 ki · Nv 11</span><p>Lanzas el conjuro <i>forma gaseosa</i> sobre ti mismo.</p></div>
                                <div class="card"><h3>Cabalgar el Viento</h3><span class="tag" style="background:#6a1a1a">4 ki · Nv 11</span><p>Lanzas el conjuro <i>volar</i> sobre ti mismo.</p></div>
                                <div class="card"><h3>Río de Llama Hambrienta</h3><span class="tag" style="background:#6a1a1a">5 ki · Nv 17</span><p>Lanzas el conjuro <i>muro de fuego</i>.</p></div>
                                <div class="card"><h3>Defensa Eterna de la Montaña</h3><span class="tag" style="background:#6a1a1a">5 ki · Nv 17</span><p>Lanzas el conjuro <i>piel pétrea</i> sobre ti mismo.</p></div>
                                <div class="card"><h3>Ola de Tierra Rodante</h3><span class="tag" style="background:#6a1a1a">6 ki · Nv 17</span><p>Lanzas el conjuro <i>muro de piedra</i>.</p></div>
                            </div>
                            <p style="color:var(--text-secondary); font-size:0.85em; margin-top:10px;">* Las disciplinas con nivel mínimo indicado (Nv 6, Nv 11, Nv 17) solo pueden aprenderse cuando alcanzas ese nivel de monje.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Paladín", 
                    descripcion: "Sea cual sea su origen y misión, los paladines están unidos por su juramento de oponerse a las fuerzas del mal. Los paladines son guerreros sagrados que combinan combate marcial con magia divina. Su poder emana de un compromiso solemne con la justicia, la protección o la venganza. Son los únicos guerreros que pueden curar heridas y emitir Castigo Divino para añadir energía radiante a sus golpes.",
                    creacion_rapida: "Primero, Fuerza debería ser tu puntuación más alta, seguida de Carisma. Segundo, escoge el trasfondo de Noble.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d10 por nivel de paladín.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 10 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe en niveles superiores:</b> 1d10 (o 6) + tu modificador de Constitución por nivel posterior al 1.</li>
                        <li><b>Competencias — Armaduras:</b> Todas las armaduras y escudos.</li>
                        <li><b>Competencias — Armas:</b> Armas simples y armas marciales.</li>
                        <li><b>Competencias — Herramientas:</b> Ninguna.</li>
                        <li><b>Tiradas de Salvación:</b> Sabiduría, Carisma.</li>
                        <li><b>Habilidades:</b> Escoge dos entre Atletismo, Intimidación, Medicina, Persuasión, Perspicacia y Religión.</li>
                        <li><b>Equipo Inicial:</b> (a) arma marcial y escudo o (b) dos armas marciales. (a) 5 jabalinas o (b) arma simple c/c. (a) equipo de sacerdote o (b) equipo de explorador. Cota de mallas y símbolo sagrado.</li>
                        <li><b>Característica de Conjuros:</b> Carisma. CD de Salvación = 8 + bonificador de competencia + mod. CAR. Bonificador de ataque = bonificador de competencia + mod. CAR.</li>
                        <li><b>Preparación de Conjuros:</b> El número de conjuros que puedes preparar cada día es igual a tu modificador de Carisma + la mitad de tu nivel de paladín (redondeando hacia abajo), con un mínimo de 1.</li>
                        <li><b>Conjuros de Juramento:</b> Cada Juramento Sagrado incluye una lista de conjuros asociados. Estos conjuros siempre están preparados y no cuentan para el límite diario de preparación.</li>
                        <li><b>Foco de Conjuros:</b> Puede usar un símbolo sagrado como foco de conjuros.</li>
                        <li><b>Castigo Divino:</b> Cuando golpeas a una criatura con un ataque de arma c/c, puedes gastar un espacio de conjuro para causar daño radiante extra al objetivo, además del daño del arma. El daño extra es 2d8 por el nivel del espacio gastado, con un máximo de 5d8 (6d8 contra muertos vivientes o demonios).</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla de Conjuros del Paladín</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:450px;">
                        <tr><th>Nivel Paladín</th><th>Nv1</th><th>Nv2</th><th>Nv3</th><th>Nv4</th><th>Nv5</th></tr>
                        <tr><td>2</td><td>2</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>3</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>5</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>7</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>8</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                        <tr><td>9</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td></tr>
                        <tr><td>10</td><td>4</td><td>3</td><td>2</td><td>—</td><td>—</td></tr>
                        <tr><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td></tr>
                        <tr><td>12</td><td>4</td><td>3</td><td>3</td><td>—</td><td>—</td></tr>
                        <tr><td>13</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td></tr>
                        <tr><td>14</td><td>4</td><td>3</td><td>3</td><td>1</td><td>—</td></tr>
                        <tr><td>15</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td></tr>
                        <tr><td>16</td><td>4</td><td>3</td><td>3</td><td>2</td><td>—</td></tr>
                        <tr><td>17</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td></tr>
                        <tr><td>18</td><td>4</td><td>3</td><td>3</td><td>3</td><td>1</td></tr>
                        <tr><td>19</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td></tr>
                        <tr><td>20</td><td>4</td><td>3</td><td>3</td><td>3</td><td>2</td></tr>
                    </table>
                    </div>
                    <p style="color:var(--text-secondary); font-size:0.85em; margin-top:8px;">* Los espacios de conjuro se recuperan con descanso prolongado. El paladín usa Carisma como característica de conjuros.</p>
                    <h5 style="color:var(--primary-color); margin-top:16px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Castigo Divino — Daño por nivel de espacio</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; width:auto;">
                        <tr><th>Espacio gastado</th><th>Daño radiante extra</th><th>vs Muertos Vivientes/Demonios</th></tr>
                        <tr><td>Nivel 1</td><td>2d8</td><td>3d8</td></tr>
                        <tr><td>Nivel 2</td><td>3d8</td><td>4d8</td></tr>
                        <tr><td>Nivel 3</td><td>4d8</td><td>5d8</td></tr>
                        <tr><td>Nivel 4</td><td>5d8</td><td>6d8</td></tr>
                        <tr><td>Nivel 5+</td><td>5d8 (máx.)</td><td>6d8 (máx.)</td></tr>
                    </table>
                    </div>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Sentido Divino:</b> Como acción, puedes abrir tu conciencia a la presencia de tales fuerzas. Hasta el final de tu siguiente turno, conoces la ubicación de todo celestial, infernal o muerto viviente a 60 pies de ti que no esté detrás de una cobertura total. Sabes el tipo (celestial, infernal o muerto viviente) de cualquier ser cuya presencia sientas, pero no su identidad. Puedes usar este rasgo un número de veces igual a 1 + tu modificador de Carisma. Recuperas todos los usos al finalizar un descanso prolongado.<br><br><b>Imposición de Manos:</b> Tienes una reserva de energía curativa que se renueva al finalizar un descanso prolongado. Con esa reserva puedes curar un total de PG iguales a tu nivel de paladín × 5. Como acción, puedes tocar a una criatura y gastar PG de la reserva para restaurarle PG, en cualquier cantidad. Alternativamente, puedes gastar 5 PG de la reserva para curar al objetivo de una enfermedad o neutralizar un veneno que le afecte. Este rasgo no tiene efecto sobre muertos vivientes y constructos." },
                        { nivel: 2, rasgos: "<b>Estilo de Combate:</b> Adoptas un estilo de combate como especialidad. Elige una de las siguientes opciones:<ul style='margin:6px 0 0 20px; line-height:1.8'><li><b>Defensa:</b> +1 a la CA mientras lleves armadura.</li><li><b>Duelista:</b> +2 al daño con arma c/c en una mano sin otra arma.</li><li><b>Combate con Armas Grandes:</b> Repites 1 o 2 en dados de daño de armas a dos manos.</li><li><b>Protección:</b> Reacción para imponer desventaja a ataque contra aliado a 5 pies (necesitas escudo).</li></ul><br><b>Lanzamiento de Conjuros:</b> A partir de este nivel puedes lanzar conjuros de paladín. Tu característica de conjuros es Carisma. Usa la tabla de conjuros para ver cuántos espacios tienes.<br><br><b>Castigo Divino:</b> Cuando golpeas a una criatura con un ataque de arma c/c, puedes gastar un espacio de conjuro para causar daño radiante extra. Ver tabla arriba para el daño por nivel de espacio." },
                        { nivel: 3, rasgos: "<b>Salud Divina:</b> La magia divina que fluye a través de ti te hace inmune a las enfermedades.<br><br><b>Juramento Sagrado:</b> Pronuncias el juramento que te ata como paladín para siempre. Hasta ahora has estado en una etapa preparatoria, comprometido con el camino pero aún sin pronunciar el juramento definitivo. Eliges entre el Juramento de Devoción, el Juramento de los Ancestros o el Juramento de Venganza. Obtienes los <b>Conjuros de Juramento</b> y la capacidad de <b>Canalizar Divinidad</b>.<br><br><b>Canalizar Divinidad (1/descanso):</b> Tu juramento te permite canalizar energía divina para alimentar efectos mágicos. Cada opción de Canalizar Divinidad se indica en la descripción del juramento. Cuando usas este rasgo, eliges qué opción usar. Recuperas el uso al terminar un descanso corto o prolongado." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 5, rasgos: "<b>Ataque Extra:</b> Puedes atacar dos veces, en lugar de una, cuando usas la acción de Atacar en tu turno." },
                        { nivel: 6, rasgos: "<b>Aura de Protección:</b> Siempre que tú o una criatura amistosa dentro de 10 pies de ti deba hacer una tirada de salvación, esa criatura añade tu modificador de Carisma a la tirada (con un mínimo de +1). Debes estar consciente para conceder este bonificador. A nivel 18, el aura se expande a 30 pies." },
                        { nivel: 7, rasgos: "<b>Rasgo de Juramento Sagrado:</b> Recibes un rasgo de tu juramento sagrado (ver subclase)." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Nuevos espacios de conjuro:</b> Ahora tienes acceso a espacios de conjuro de nivel 3 (ver tabla)." },
                        { nivel: 10, rasgos: "<b>Aura de Coraje:</b> Tú y las criaturas amistosas dentro de 10 pies de ti no podéis ser asustados mientras estés consciente. A nivel 18, el aura se expande a 30 pies." },
                        { nivel: 11, rasgos: "<b>Castigo Divino Mejorado:</b> Estás tan imbuido de poder justo que todos tus ataques con arma c/c llevan poder divino. Siempre que golpeas a una criatura con un arma c/c, la criatura recibe 1d8 de daño radiante extra. Si también usas Castigo Divino, añades este daño al daño extra del Castigo Divino." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Nuevos espacios de conjuro:</b> Ahora tienes acceso a espacios de conjuro de nivel 4 (ver tabla)." },
                        { nivel: 14, rasgos: "<b>Toque de Purificación:</b> Puedes usar tu acción para terminar un conjuro sobre ti mismo o sobre una criatura que toques. Puedes usar este rasgo un número de veces igual a tu modificador de Carisma (mínimo 1). Recuperas todos los usos al finalizar un descanso prolongado." },
                        { nivel: 15, rasgos: "<b>Rasgo de Juramento Sagrado:</b> Recibes otro rasgo de tu juramento sagrado (ver subclase)." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Nuevos espacios de conjuro:</b> Ahora tienes acceso a espacios de conjuro de nivel 5 (ver tabla)." },
                        { nivel: 18, rasgos: "<b>Auras mejoradas:</b> El radio del Aura de Protección y del Aura de Coraje (y el aura de tu juramento si la tiene) se expanden de 10 a <b>30 pies</b>." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Rasgo Sagrado de Juramento (Capstone):</b> Recibes el rasgo final y más poderoso de tu juramento sagrado (ver subclase)." }
                    ],
                    subclases_titulo: "Juramentos Sagrados",
                    subclases: [
                        { 
                            nombre: "Juramento de Devoción", 
                            rasgos: `<p>El Juramento de Devoción ata a un paladín a los ideales más elevados de la justicia, la virtud y el orden. A veces llamados caballeros, caballeros blancos o guerreros santos, estos paladines se reúnen bajo el ideal platónico del caballero en brillante armadura que actúa en favor del bien.</p>

                            <p><b>Principios del Juramento:</b> Honestidad (No mentiras ni engaños), Coraje (Nunca el miedo en el combate), Compasión (Ayuda a los demás, protege a los débiles), Honor (Trata a los demás con equidad), Deber (Respeta a la autoridad legítima).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Conjuros de Juramento</h5>
                            <table style="font-size:0.85em; margin:8px 0;">
                                <tr><th>Nivel Paladín</th><th>Conjuros</th></tr>
                                <tr><td>3</td><td><i>Protección contra el bien y el mal, Santuario</i></td></tr>
                                <tr><td>5</td><td><i>Restauración Menor, Zona de la Verdad</i></td></tr>
                                <tr><td>9</td><td><i>Baliza de Esperanza, Disipar Magia</i></td></tr>
                                <tr><td>13</td><td><i>Libertad de Movimiento, Guardián de la Fe</i></td></tr>
                                <tr><td>17</td><td><i>Comunión, Golpe de Llamas</i></td></tr>
                            </table>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Canalizar Divinidad</h5>
                            <p>Cuando pronuncias el Juramento de Devoción a nivel 3, obtienes las siguientes opciones de Canalizar Divinidad:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Arma Sagrada:</b> Como acción, imbúyes un arma que estés empuñando con energía divina positiva. Durante 1 minuto, añades tu modificador de Carisma a las tiradas de ataque con esa arma (mínimo +1). El arma también emite luz brillante en un radio de 20 pies y luz tenue 20 pies más allá. Si el arma no es mágica, se vuelve mágica durante la duración. Puedes terminar el efecto antes de tiempo como acción adicional. El efecto termina si sueltas el arma.</li>
                                <li><b>Expulsar al Profano:</b> Como acción, presentas tu símbolo sagrado y recitas una oración de condena. Cada infernal o muerto viviente que pueda verte u oírte y que esté a 30 pies de ti debe hacer una tirada de salvación de Sabiduría. Si falla, queda expulsado durante 1 minuto o hasta que reciba daño.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Aura de Devoción</h5>
                            <p>Tú y las criaturas amistosas dentro de 10 pies de ti (30 pies a nivel 18) no podéis ser encantados mientras estés consciente.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Pureza de Espíritu</h5>
                            <p>Estás siempre bajo el efecto del conjuro <i>protección contra el bien y el mal</i>.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 20 — Nimbo Sagrado</h5>
                            <p>Como acción, puedes emanar un aura de luz solar. Durante 1 minuto, emites luz brillante en un radio de 30 pies y luz tenue 30 pies más allá. Los enemigos que comiencen su turno en la luz brillante reciben 10 de daño radiante. Además, tienes ventaja en las tiradas de salvación contra conjuros lanzados por infernales o muertos vivientes. Una vez usas este rasgo, debes terminar un descanso prolongado para volver a usarlo.</p>`
                        },
                        { 
                            nombre: "Juramento de los Ancestros", 
                            rasgos: `<p>El Juramento de los Ancestros es tan antiguo como la raza de los elfos y los rituales del mundo natural. A veces llamados guardianes fey, cuernos verdes o caballeros del despertar, los paladines que pronuncian este juramento se ponen del lado de lo salvaje y de la vitalidad de la naturaleza en lugar de las nociones abstractas del bien.</p>

                            <p><b>Principios del Juramento:</b> Enciende la luz (Aleja la desesperación de tus aliados), Refugia la luz (Protege a quienes no pueden protegerse), Preserva tu propia luz (No cedes ante la corrupción), Sé la luz (Eres una llama en las tinieblas).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Conjuros de Juramento</h5>
                            <table style="font-size:0.85em; margin:8px 0;">
                                <tr><th>Nivel Paladín</th><th>Conjuros</th></tr>
                                <tr><td>3</td><td><i>Golpe Aprisionador, Hablar con Animales</i></td></tr>
                                <tr><td>5</td><td><i>Rayo Lunar, Paso Brumoso</i></td></tr>
                                <tr><td>9</td><td><i>Crecimiento Vegetal, Protección contra la Energía</i></td></tr>
                                <tr><td>13</td><td><i>Tormenta de Hielo, Piel Pétrea</i></td></tr>
                                <tr><td>17</td><td><i>Comunión con la Naturaleza, Andar por los Árboles</i></td></tr>
                            </table>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Canalizar Divinidad</h5>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Ira de la Naturaleza:</b> Como acción, puedes hacer que vides espectrales broten del suelo en un espacio que puedas ver dentro de 10 pies de ti. Una criatura en ese espacio debe superar una tirada de salvación de Fuerza o Destreza (a su elección) o quedar apresada. Mientras esté apresada, la criatura puede usar su acción para hacer una prueba de Fuerza o Destreza contra tu CD de conjuros para liberarse. Las vides desaparecen si el objetivo tiene éxito o si usas esta opción de nuevo.</li>
                                <li><b>Expulsar a los Infieles:</b> Como acción, presentas tu símbolo sagrado y pronuncias una plegaria que llena de horror a los seres infernales. Cada hada o infernal que pueda verte u oírte y que esté a 30 pies de ti debe superar una tirada de salvación de Sabiduría o quedar expulsado durante 1 minuto o hasta que reciba daño.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Aura de Custodia</h5>
                            <p>Antiguas magias protectoras te rodean. Tú y las criaturas amistosas dentro de 10 pies de ti (30 pies a nivel 18) tenéis resistencia al daño de los conjuros.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Centinela Imperecedero</h5>
                            <p>Cuando serías reducido a 0 Puntos de Golpe y no mueres directamente, puedes en su lugar caer a 1 Punto de Golpe. Una vez usas este rasgo, debes terminar un descanso prolongado para volver a usarlo.<br>Además, no puedes envejecer mágicamente, y aunque aún puedes morir de vejez, el ritmo en que envejeces es extremadamente lento.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 20 — Campeón Ancestral</h5>
                            <p>Como acción, puedes asumir la forma de un campeón ancestral. Durante 1 minuto obtienes los siguientes beneficios:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li>Al comienzo de cada uno de tus turnos, recuperas 10 Puntos de Golpe.</li>
                                <li>Los conjuros de paladín que normalmente tienen tiempo de lanzamiento de 1 acción puedes lanzarlos como acción adicional.</li>
                                <li>Los lanzadores de conjuros enemigos dentro de 10 pies de ti tienen desventaja en las tiradas de salvación de Constitución para mantener la concentración.</li>
                            </ul>
                            <p>Una vez usas este rasgo, debes terminar un descanso prolongado para volver a usarlo.</p>`
                        },
                        { 
                            nombre: "Juramento de Venganza", 
                            rasgos: `<p>El Juramento de Venganza es un solemne compromiso de castigar a aquellos que han cometido un pecado atroz. Cuando el mal destruye la bondad de un pueblo, los paladines que pronuncian este juramento son el último recurso, la última barrera ante la oscuridad. Estos paladines sacrifican incluso la misericordia para lograr sus fines.</p>

                            <p><b>Principios del Juramento:</b> Luchar contra el mal mayor (El fin justifica los medios), Sin piedad para los malvados (Los monstruos que amenazan vidas no merecen misericordia), Por cualquier medio necesario (Tus opciones en tu búsqueda no están limitadas), Restitución (Los culpables deben rendir cuentas).</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Conjuros de Juramento</h5>
                            <table style="font-size:0.85em; margin:8px 0;">
                                <tr><th>Nivel Paladín</th><th>Conjuros</th></tr>
                                <tr><td>3</td><td><i>Maldición, Marca del Cazador</i></td></tr>
                                <tr><td>5</td><td><i>Sostener Persona, Paso Brumoso</i></td></tr>
                                <tr><td>9</td><td><i>Apresuramiento, Protección contra la Energía</i></td></tr>
                                <tr><td>13</td><td><i>Destierro, Puerta Dimensional</i></td></tr>
                                <tr><td>17</td><td><i>Sostener Monstruo, Escrutinio</i></td></tr>
                            </table>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Canalizar Divinidad</h5>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li><b>Abjurar Enemigo:</b> Como acción, eliges a una criatura que puedas ver dentro de 60 pies de ti y presentas tu símbolo sagrado. Esa criatura debe hacer una tirada de salvación de Sabiduría, a menos que sea inmune a quedar asustada. Las hadas, infernales y muertos vivientes tienen desventaja en esta tirada. Si falla, queda <i>asustada</i> y su velocidad cae a 0 durante 1 minuto o hasta que reciba daño. Si tiene éxito, su velocidad se reduce a la mitad hasta el final de su siguiente turno.</li>
                                <li><b>Voto de Enemistad:</b> Como acción adicional, puedes pronunciar un voto de enemistad contra una criatura que puedas ver dentro de 10 pies de ti. Obtienes ventaja en las tiradas de ataque contra esa criatura durante 1 minuto o hasta que caiga a 0 Puntos de Golpe o pierda la consciencia.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 7 — Vengador Implacable</h5>
                            <p>Tu velocidad sobrenatural te permite ir tras los enemigos que caen ante ti. Cuando golpeas a una criatura con un ataque de oportunidad, puedes moverte hasta la mitad de tu velocidad como parte de la misma reacción. Este movimiento no provoca ataques de oportunidad.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 15 — Alma de Venganza</h5>
                            <p>La autoridad con la que pronuncias tu Voto de Enemistad te otorga mayor poder sobre tu enemigo. Cuando una criatura bajo el efecto de tu Voto de Enemistad haga un ataque de arma, puedes usar tu reacción para hacer un ataque c/c contra esa criatura si está dentro de tu alcance.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 20 — Ángel Vengador</h5>
                            <p>Puedes asumir la forma de un ángel de la muerte divina. Usando tu acción, sufres una transformación. Durante 1 hora ganas los siguientes beneficios:</p>
                            <ul style="line-height:1.8; padding-left:20px;">
                                <li>Brotan alas de tu espalda y ganas una velocidad de vuelo de 60 pies.</li>
                                <li>Emanas un aura de amenaza en un radio de 30 pies. La primera vez que cualquier criatura hostil entre en el aura o comience su turno en ella durante el combate, debe superar una tirada de salvación de Sabiduría contra tu CD de conjuros o quedar <i>asustada</i> de ti durante 1 minuto, o hasta que reciba daño.</li>
                                <li>Los ataques contra criaturas asustadas por tu aura tienen ventaja.</li>
                            </ul>
                            <p>Una vez usas este rasgo, debes terminar un descanso prolongado para volver a usarlo.</p>`
                        }
                    ]
                },
                { 
                    nombre: "Pícaro", 
                    descripcion: "Los pícaros confían en sus habilidades, el sigilo y las vulnerabilidades de sus oponentes para lograr ventaja en cualquier situación. Tienen un don para encontrar la solución a prácticamente cualquier problema, demostrando un ingenio y versatilidad que es la piedra angular de cualquier grupo de aventureros.",
                    creacion_rapida: "Primero, Destreza debe ser tu puntuación más alta. Si planeas tomar el arquetipo de Bribón Arcano, haz que la Inteligencia sea la siguiente más alta; si prefieres énfasis en engaño e interacciones sociales, escoge Carisma. Segundo, escoge el trasfondo de Criminal.",
                    rasgos_basicos: `<ul class="race-traits">
                        <li><b>Dados de Golpe:</b> 1d8 por nivel de pícaro.</li>
                        <li><b>Puntos de Golpe a nivel 1:</b> 8 + tu modificador de Constitución.</li>
                        <li><b>Puntos de Golpe a niveles superiores:</b> 1d8 (o 5) + tu modificador de Constitución por cada nivel de pícaro después del primero.</li>
                        <li><b>Competencias — Armadura:</b> Armadura ligera.</li>
                        <li><b>Competencias — Armas:</b> Armas simples, ballesta de mano, espada larga, espada ropera, espada corta.</li>
                        <li><b>Competencias — Herramientas:</b> Herramientas de ladrón.</li>
                        <li><b>Tiradas de Salvación:</b> Destreza, Inteligencia.</li>
                        <li><b>Habilidades:</b> Escoge cuatro entre Acrobacias, Atletismo, Engaño, Interpretación, Intimidación, Investigación, Juego de Manos, Percepción, Perspicacia, Persuasión y Sigilo.</li>
                        <li><b>Equipo Inicial:</b> (a) espada ropera o (b) espada corta. (a) arco corto y carcaj con 20 flechas o (b) espada corta. (a) equipo de ladrón, (b) equipo de saqueador o (c) equipo de explorador. Armadura de cuero, dos dagas y herramientas de ladrón.</li>
                    </ul>
                    <h5 style="color:var(--primary-color); margin-top:20px; border-bottom:1px solid var(--border-color); padding-bottom:5px;">Tabla del Pícaro — Ataque Furtivo</h5>
                    <div style="overflow-x:auto;">
                    <table style="font-size:0.85em; min-width:500px;">
                        <tr><th>Nivel</th><th>Bon. Competencia</th><th>Ataque Furtivo</th><th>Rasgos</th></tr>
                        <tr><td>1</td><td>+2</td><td>1d6</td><td>Experto, Ataque Furtivo, Jerga de Ladrones</td></tr>
                        <tr><td>2</td><td>+2</td><td>1d6</td><td>Acción Astuta</td></tr>
                        <tr><td>3</td><td>+2</td><td>2d6</td><td>Arquetipo de Pícaro</td></tr>
                        <tr><td>4</td><td>+2</td><td>2d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>5</td><td>+3</td><td>3d6</td><td>Esquiva Asombrosa</td></tr>
                        <tr><td>6</td><td>+3</td><td>3d6</td><td>Experto</td></tr>
                        <tr><td>7</td><td>+3</td><td>4d6</td><td>Evasión</td></tr>
                        <tr><td>8</td><td>+3</td><td>4d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>9</td><td>+4</td><td>5d6</td><td>Rasgo de Arquetipo de Pícaro</td></tr>
                        <tr><td>10</td><td>+4</td><td>5d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>11</td><td>+4</td><td>6d6</td><td>Talento Seguro</td></tr>
                        <tr><td>12</td><td>+4</td><td>6d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>13</td><td>+5</td><td>7d6</td><td>Rasgo de Arquetipo de Pícaro</td></tr>
                        <tr><td>14</td><td>+5</td><td>7d6</td><td>Sentido Ciego</td></tr>
                        <tr><td>15</td><td>+5</td><td>8d6</td><td>Mente Escurridiza</td></tr>
                        <tr><td>16</td><td>+5</td><td>8d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>17</td><td>+6</td><td>9d6</td><td>Rasgo de Arquetipo de Pícaro</td></tr>
                        <tr><td>18</td><td>+6</td><td>9d6</td><td>Escurridizo</td></tr>
                        <tr><td>19</td><td>+6</td><td>10d6</td><td>Mejora de Puntuación de Características</td></tr>
                        <tr><td>20</td><td>+6</td><td>10d6</td><td>Golpe de Suerte</td></tr>
                    </table>
                    </div>`,
                    niveles: [
                        { nivel: 1, rasgos: "<b>Experto:</b> Escoge dos de tus competencias con habilidades, o una de tus competencias con habilidades y tu competencia con herramientas de ladrón. Tu bonificador de competencia se duplica para cualquier prueba de característica que hagas usando esas competencias elegidas.<br><br><b>Ataque Furtivo:</b> Una vez por turno, si tienes ventaja en la tirada de ataque puedes infligir 1d6 de daño extra a la criatura que golpees. El ataque debe usar un arma sutil o a distancia. No necesitas ventaja si tu objetivo tiene otro adversario a 5 pies que no esté incapacitado y tú no tengas desventaja en la tirada.<br><br><b>Jerga de Ladrones:</b> Conoces la jerga secreta de los ladrones, que mezcla dialectos, argot y código para esconder mensajes en conversación aparentemente normal. Solo otra criatura que conozca la jerga puede entender esos mensajes. También entiendes signos y símbolos secretos usados para transmitir mensajes cortos (zona peligrosa, territorio de una cofradía, botín cercano, casas seguras)." },
                        { nivel: 2, rasgos: "<b>Acción Astuta:</b> Tu rapidez mental y agilidad te permiten actuar rápido. Puedes utilizar una acción adicional en cada uno de tus turnos de combate, pero solo para las acciones de Carrera, Retirada o Esconderse." },
                        { nivel: 3, rasgos: "<b>Arquetipo de Pícaro:</b> Eliges un arquetipo que refleja cómo usas tus habilidades: Ladrón, Asesino o Bribón Arcano. Tu elección te otorga rasgos a nivel 3 y de nuevo en los niveles 9, 13 y 17." },
                        { nivel: 4, rasgos: "<b>Mejora de Puntuación de Características:</b> Aumentas una puntuación de característica en 2, o dos en 1 cada una. No puedes superar 20 con este rasgo." },
                        { nivel: 5, rasgos: "<b>Esquiva Asombrosa:</b> Cuando un atacante que puedes ver te golpea con un ataque, puedes usar tu reacción para reducir a la mitad el daño que sufras." },
                        { nivel: 6, rasgos: "<b>Experto (mejora):</b> Puedes escoger dos competencias más (habilidades o herramientas de ladrón) para duplicar tu bonificador de competencia en ellas." },
                        { nivel: 7, rasgos: "<b>Evasión:</b> Cuando estás sometido a un efecto que te permite hacer una tirada de salvación de Destreza para sufrir solo la mitad del daño, en lugar de eso no sufres ningún daño si tienes éxito, y solo la mitad del daño si fallas." },
                        { nivel: 8, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 9, rasgos: "<b>Rasgo de Arquetipo de Pícaro:</b> Ver subclase elegida." },
                        { nivel: 10, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 11, rasgos: "<b>Talento Seguro:</b> Has refinado tus habilidades casi a la perfección. Siempre que hagas una prueba de característica que te permita añadir tu bonificador de competencia, si en la tirada d20 obtienes un 9 o menor puedes tratarlo como un 10." },
                        { nivel: 12, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 13, rasgos: "<b>Rasgo de Arquetipo de Pícaro:</b> Ver subclase elegida." },
                        { nivel: 14, rasgos: "<b>Sentido Ciego:</b> Si eres capaz de oír, eres consciente de dónde se encuentra cualquier criatura escondida o invisible a 10 pies (3 metros) o menos de ti." },
                        { nivel: 15, rasgos: "<b>Mente Escurridiza:</b> Has adquirido una fuerza mental excepcional. Obtienes competencia en tiradas de salvación de Sabiduría." },
                        { nivel: 16, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 17, rasgos: "<b>Rasgo de Arquetipo de Pícaro:</b> Ver subclase elegida." },
                        { nivel: 18, rasgos: "<b>Escurridizo:</b> Eres tan evasivo que raramente los atacantes pueden comprometer tu defensa. Ninguna tirada de ataque tiene ventaja contra ti mientras no estés incapacitado." },
                        { nivel: 19, rasgos: "<b>Mejora de Puntuación de Características.</b>" },
                        { nivel: 20, rasgos: "<b>Golpe de Suerte:</b> Si tu ataque no golpea a un objetivo dentro de tu rango, puedes convertir el fallo en un golpe. Además, si fallas una prueba de característica, puedes tratar la tirada d20 como un 20. Una vez usado, no puedes volver a usarlo hasta terminar un descanso corto o prolongado." }
                    ],
                    subclases_titulo: "Arquetipos de Pícaro",
                    subclases: [
                        { 
                            nombre: "Ladrón", 
                            rasgos: `<p>Perfeccionas tus habilidades en las artes del robo. Ladrones, bandidos, rateros y criminales siguen este arquetipo, pero también buscadores de tesoros profesionales, exploradores, saqueadores e investigadores. Además de mejorar tu agilidad y sigilo, aprendes habilidades útiles para adentrarte en ruinas antiguas, leer lenguas extrañas y usar objetos mágicos que normalmente no podrías utilizar.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Manos Rápidas</h5>
                            <p>Puedes usar la acción adicional que te otorga tu Acción Astuta para hacer una prueba de Destreza (Juego de Manos), usar tus herramientas de ladrón para desarmar una trampa o abrir una cerradura, o para ejecutar la acción de Usar un Objeto.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Trabajo en el Segundo Piso</h5>
                            <p>Ganas la habilidad de trepar más rápido de lo normal: trepar ya no te costará movimiento extra. Además, cuando haces un salto en carrera la distancia que cubres se incrementa en un número de pies igual a tu modificador de Destreza.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 9 — Sigilo Supremo</h5>
                            <p>Tienes ventaja en una prueba de Destreza (Sigilo) si no te mueves más de la mitad de tu velocidad en el mismo turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 13 — Usar Objeto Mágico</h5>
                            <p>Has aprendido tanto sobre cómo funciona la magia que puedes improvisar el uso de objetos incluso cuando no están pensados para ti. Ignoras todos los requisitos de clase, raza y nivel para el uso de objetos mágicos.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 17 — Reflejos de Ladrón</h5>
                            <p>Te has convertido en un experto en tender emboscadas y escapar rápidamente del peligro. Puedes llevar a cabo dos turnos durante el primer asalto de cualquier combate. Realizas tu primer turno con tu iniciativa normal y el segundo turno con tu iniciativa menos 10. No puedes usar esta característica cuando eres sorprendido.</p>`
                        },
                        { 
                            nombre: "Asesino", 
                            rasgos: `<p>Concentras tu entrenamiento en el macabro arte de la muerte. Asesinos pagados, cazarrecompensas, espías e incluso clérigos ungidos para exterminar los enemigos de su deidad siguen este arquetipo. El sigilo, el veneno y el disfraz te ayudan a eliminar a tus enemigos con eficacia mortal.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Competencias Adicionales</h5>
                            <p>Ganas competencia con el equipo de disfraz y el equipo de envenenador.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Asesinar</h5>
                            <p>Eres aún más mortal cuando te anticipas a tus enemigos. Tienes ventaja en las tiradas de ataque realizadas contra cualquier criatura que no haya resuelto su turno aún. Además, cualquier golpe que inflijas contra una criatura sorprendida es un golpe crítico.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 9 — Experto en Infiltración</h5>
                            <p>Puedes crear falsas identidades de manera infalible. Debes emplear siete días y 25 po para establecer la historia, profesión y afiliaciones de una nueva identidad (no puede pertenecer a alguien real). Con la vestimenta adecuada, cartas de introducción y certificaciones falsas puedes establecerte como, por ejemplo, miembro de un gremio de una ciudad remota. Si adoptas la identidad como disfraz, las criaturas creen que eres esa persona hasta que tengan un motivo obvio para no hacerlo.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 13 — Impostor</h5>
                            <p>Ganas la habilidad de imitar el habla, la escritura y el comportamiento de otra persona. Debes emplear al menos tres horas estudiando estos tres componentes: escuchándola hablar, examinando su escritura y observando sus peculiaridades. Tu ardid es indiscernible frente al observador casual. Si una criatura alerta sospecha que algo está mal, tienes ventaja en cualquier tirada de Carisma (Engaño) para evitar ser detectado.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 17 — Golpe Mortal</h5>
                            <p>Te conviertes en un maestro de la muerte instantánea. Cuando atacas y golpeas a una criatura sorprendida, ésta debe hacer una tirada de salvación de Constitución (CD 8 + tu modificador de Destreza + tu bonificador de competencia). Si falla, duplica el daño de tu ataque contra esa criatura.</p>`
                        },
                        { 
                            nombre: "Bribón Arcano", 
                            rasgos: `<p>Algunos pícaros potencian sus refinadas habilidades de sigilo y agilidad mediante la magia, aprendiendo trucos de encantamiento e ilusión. Estos pícaros abarcan rateros y asaltantes, pero también bromistas, traviesos y un número significativo de aventureros.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Lanzamiento de Conjuros</h5>
                            <p>Ganas la habilidad de lanzar conjuros de la lista de mago. <b>Inteligencia</b> es tu característica de lanzamiento.</p>
                            <ul style="line-height:1.7; padding-left:20px; margin-top:8px;">
                                <li><b>Trucos:</b> Aprendes tres trucos: <i>mano de mago</i> y otros dos a tu elección de la lista del mago. Aprendes otro truco al nivel 10.</li>
                                <li><b>Conjuros conocidos de nivel 1 y superior:</b> Conoces tres conjuros de mago de nivel 1, dos de los cuales deben ser de la escuela de Encantamiento o Ilusión. Los conjuros que aprendes en niveles 8, 14 y 20 pueden pertenecer a cualquier escuela.</li>
                                <li><b>CD de Salvación:</b> 8 + bonificador de competencia + modificador de INT.</li>
                                <li><b>Modificador de Ataque:</b> bonificador de competencia + modificador de INT.</li>
                            </ul>

                            <h5 style="color:#d32f2f; margin-top:15px;">Tabla de Conjuros del Bribón Arcano</h5>
                            <div style="overflow-x:auto;">
                            <table style="font-size:0.85em; min-width:500px; margin-top:8px;">
                                <tr><th>Nivel Pícaro</th><th>Trucos</th><th>Conj. Conocidos</th><th>Espacios Nv1</th><th>Espacios Nv2</th><th>Espacios Nv3</th><th>Espacios Nv4</th></tr>
                                <tr><td>3</td><td>3</td><td>3</td><td>2</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>4</td><td>3</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>5</td><td>3</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>6</td><td>3</td><td>4</td><td>3</td><td>—</td><td>—</td><td>—</td></tr>
                                <tr><td>7</td><td>3</td><td>5</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>8</td><td>3</td><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>9</td><td>3</td><td>6</td><td>4</td><td>2</td><td>—</td><td>—</td></tr>
                                <tr><td>10</td><td>4</td><td>7</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>11</td><td>4</td><td>8</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>12</td><td>4</td><td>8</td><td>4</td><td>3</td><td>—</td><td>—</td></tr>
                                <tr><td>13</td><td>4</td><td>9</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>14</td><td>4</td><td>10</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>15</td><td>4</td><td>10</td><td>4</td><td>3</td><td>2</td><td>—</td></tr>
                                <tr><td>16</td><td>4</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>17</td><td>4</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>18</td><td>4</td><td>11</td><td>4</td><td>3</td><td>3</td><td>—</td></tr>
                                <tr><td>19</td><td>4</td><td>12</td><td>4</td><td>3</td><td>3</td><td>1</td></tr>
                                <tr><td>20</td><td>4</td><td>13</td><td>4</td><td>3</td><td>3</td><td>1</td></tr>
                            </table>
                            </div>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 3 — Prestigitación de Mano del Mago</h5>
                            <p>Cuando lanzas <i>mano de mago</i>, puedes hacer invisible la mano espectral y además puedes: depositar un objeto en un contenedor portado por otra criatura, coger un objeto de un contenedor portado por otra criatura, o usar herramientas de ladrón a distancia para abrir cerraduras y desarmar trampas. Puedes realizar estas acciones sin ser percibido si tienes éxito en una prueba de Destreza (Juego de Manos) enfrentada a la Sabiduría (Percepción) de la criatura. Además, puedes usar la acción adicional de Acción Astuta para controlar la mano.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 9 — Emboscada Mágica</h5>
                            <p>Si estás escondido de una criatura en el momento en que lanzas un conjuro sobre ella, la criatura tiene desventaja en cualquier tirada de salvación contra el conjuro ese turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 13 — Bribón Versátil</h5>
                            <p>Ganas la habilidad de distraer objetivos con tu <i>mano de mago</i>. Como acción adicional en tu turno puedes designar una criatura que no esté a más de 5 pies de la mano espectral. Hacerlo te proporciona ventaja en las tiradas de ataque contra esa criatura hasta el final del turno.</p>

                            <h5 style="color:#d32f2f; margin-top:15px;">Nivel 17 — Ladrón de Conjuros</h5>
                            <p>Ganas la habilidad de robar mágicamente el conocimiento para lanzar un conjuro de otro lanzador. Inmediatamente después de que una criatura lance un conjuro del cual seas objetivo o te incluya en su área de efecto, puedes usar tu reacción para forzarla a hacer una tirada de salvación con su modificador de característica de lanzamiento (CD = la CD de salvación de tus conjuros). Si falla, niegas el efecto del conjuro en ti y robas su conocimiento si es al menos de nivel 1 y de un nivel que puedas lanzar. Durante las siguientes 8 horas conoces el conjuro y puedes lanzarlo con tus espacios. La criatura no puede lanzarlo hasta que pasen las 8 horas. Una vez usado, no puedes volver a usarlo hasta terminar un descanso prolongado.</p>`
                        }
                    ]
                }
            ],
            "Razas": [
                { 
                    nombre: "Enano", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Constitución +2.</li>
                        <li><b>Edad:</b> 50 a 350 años.</li>
                        <li><b>Alineamiento:</b> Legal, tienden al bien.</li>
                        <li><b>Tamaño:</b> Mediano (4 a 5 pies, 150 lb).</li>
                        <li><b>Velocidad:</b> 25 pies. Tu velocidad no se ve reducida cuando llevas armadura pesada.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Resistencia Enana:</b> Tienes ventaja en las tiradas de salvación contra veneno, y posees resistencia contra el daño por veneno.</li>
                        <li><b>Entrenamiento de Combate Enano:</b> Eres competente con el hacha de batalla, hacha de mano, martillo arrojadizo y martillo de guerra.</li>
                        <li><b>Competencia con Herramientas:</b> Ganas competencia con unas herramientas de artesano a tu elección: herramientas de herrero, materiales de cervecería o herramientas de albañil.</li>
                        <li><b>Afinidad con la Piedra:</b> Doble bonificador de competencia en pruebas de Inteligencia (Historia) relacionadas con el origen de un trabajo hecho en piedra.</li>
                        <li><b>Idiomas:</b> Común y Enano.</li>
                    </ul>`,
                    subrazas: [
                        { nombre: "Enano de las Colinas", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Sabiduría +1.</li><li><b>Dureza Enana:</b> Tus puntos de golpe máximos aumentan en 1, y aumentan en 1 cada vez que ganes un nivel.</li></ul>` },
                        { nombre: "Enano de las Montañas", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Fuerza +2.</li><li><b>Entrenamiento con Armadura Enana:</b> Tienes competencia con las armaduras ligeras y medias.</li></ul>` }
                    ]
                },
                { 
                    nombre: "Elfo", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Destreza +2.</li>
                        <li><b>Edad:</b> 100 a 750 años.</li>
                        <li><b>Alineamiento:</b> Generalmente Caótico Bueno.</li>
                        <li><b>Tamaño:</b> Mediano (varían entre menos de 5 pies y más de 6 pies).</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Sentidos Agudos:</b> Eres competente con la habilidad de Percepción.</li>
                        <li><b>Ascendencia Feérica:</b> Tienes ventaja en las tiradas de salvación contra Encantamiento, y no puedes ser dormido mediante la magia.</li>
                        <li><b>Trance:</b> Meditación profunda de 4 horas en lugar de dormir.</li>
                        <li><b>Idiomas:</b> Común y Élfico.</li>
                    </ul>`,
                    subrazas: [
                        { nombre: "Alto Elfo", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Inteligencia +1.</li><li><b>Entrenamiento en Armas Élficas:</b> Competencia con espada larga, espada corta, arco largo y arco corto.</li><li><b>Truco:</b> Conoces un truco de mago a tu elección (INT).</li><li><b>Idioma Adicional:</b> Uno a tu elección.</li></ul>` },
                        { nombre: "Elfo de los Bosques", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Sabiduría +1.</li><li><b>Entrenamiento en Armas Élficas:</b> Competencia con espada larga, espada corta, arco largo y arco corto.</li><li><b>Ligero de Pies:</b> Velocidad 35 pies.</li><li><b>Máscara de la Espesura:</b> Puedes intentar esconderte incluso cuando sólo estás ligeramente cubierto por el follaje o clima.</li></ul>` },
                        { nombre: "Elfo Oscuro (Drow)", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Carisma +1.</li><li><b>Visión Oscuridad Mejorada:</b> 120 pies.</li><li><b>Sensibilidad a la Luz del Sol:</b> Desventaja en ataques y percepción bajo luz solar.</li><li><b>Magia Drow:</b> Conoces el truco <i>luces danzantes</i>. En nivel 3 ganas <i>fuego feérico</i> (1/día). En nivel 5 ganas <i>oscuridad</i> (1/día).</li><li><b>Entrenamiento en Armas Drow:</b> Competencia con espadas roperas, espadas cortas y ballestas de mano.</li></ul>` }
                    ]
                },
                { 
                    nombre: "Mediano", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Destreza +2.</li>
                        <li><b>Edad:</b> Alcanza la madurez a los 20 y vive unos 150 años.</li>
                        <li><b>Alineamiento:</b> Generalmente Legal Bueno.</li>
                        <li><b>Tamaño:</b> Pequeño (aprox. 3 pies, 40 lb).</li>
                        <li><b>Velocidad:</b> 25 pies.</li>
                        <li><b>Suertudo:</b> Si sacas un 1 en ataque, característica o salvación, puedes volver a tirar el dado y usar el nuevo resultado.</li>
                        <li><b>Valiente:</b> Ventaja en tiradas de salvación contra ser asustado.</li>
                        <li><b>Agilidad Mediana:</b> Puedes moverte a través del espacio de cualquier criatura de tamaño mayor al tuyo.</li>
                        <li><b>Idiomas:</b> Común y Mediano.</li>
                    </ul>`,
                    subrazas: [
                        { nombre: "Piesligeros", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Carisma +1.</li><li><b>Sigiloso por Naturaleza:</b> Puedes intentar esconderte incluso cuando estás cubierto tras una criatura al menos un tamaño mayor que tú.</li></ul>` },
                        { nombre: "Fornido", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Constitución +1.</li><li><b>Resistencia Fornida:</b> Ventaja en salvaciones contra veneno, resistencia al daño de veneno.</li></ul>` }
                    ]
                },
                { 
                    nombre: "Humano", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Todas las puntuaciones de característica aumentan en +1.</li>
                        <li><b>Edad:</b> Alcanzan la madurez a los 20 y rara vez viven más de 100 años.</li>
                        <li><b>Alineamiento:</b> No tienen alineamiento concreto.</li>
                        <li><b>Tamaño:</b> Mediano (5 a 6 pies).</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Idiomas:</b> Común y 1 idioma extra a tu elección.</li>
                        <li><b>Regla Variante:</b> Si tu DM lo permite, en lugar del +1 a todas, recibes: +1 a dos características diferentes, 1 Habilidad a elección, y 1 Dote a elección.</li>
                    </ul>`
                },
                { 
                    nombre: "Dracónido", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Fuerza +2, Carisma +1.</li>
                        <li><b>Edad:</b> Madurez a los 15, viven unos 80 años.</li>
                        <li><b>Alineamiento:</b> Extremos (Bien o Mal).</li>
                        <li><b>Tamaño:</b> Mediano (más de 6 pies, 250 lb).</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Ascendencia Dracónica:</b> Elige un tipo de dragón que determina tu arma de aliento y resistencia al daño.</li>
                        <li><b>Arma de Aliento:</b> Acción para exhalar energía destructiva. Salvación (CD 8 + mod CON + Prof) basada en tu ascendencia. 2d6 daño (mitad si salva), incrementa a nivel 6, 11 y 16. Recupera uso en descanso corto o prolongado.</li>
                        <li><b>Resistencia al Daño:</b> Tienes resistencia al tipo de daño asociado a tu ancestro.</li>
                        <li><b>Idiomas:</b> Común y Dracónico.</li>
                    </ul>`
                },
                { 
                    nombre: "Gnomo", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Inteligencia +2.</li>
                        <li><b>Edad:</b> Madurez a los 40, viven entre 350 y 500 años.</li>
                        <li><b>Alineamiento:</b> Mayormente Buenos.</li>
                        <li><b>Tamaño:</b> Pequeño (3 a 4 pies, 40 lb).</li>
                        <li><b>Velocidad:</b> 25 pies.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Astucia Gnoma:</b> Ventaja en todas las tiradas de salvación de Inteligencia, Sabiduría y Carisma contra magia.</li>
                        <li><b>Idiomas:</b> Común y Gnómico.</li>
                    </ul>`,
                    subrazas: [
                        { nombre: "Gnomo de los Bosques", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Destreza +1.</li><li><b>Ilusionista Natural:</b> Conoces el truco <i>ilusión menor</i> (INT).</li><li><b>Hablar con Bestias Pequeñas:</b> A través de sonidos/gestos comunicas ideas simples a bestias Pequeñas o menores.</li></ul>` },
                        { nombre: "Gnomo de las Rocas", rasgos: `<ul class="race-traits"><li><b>Mejora de Característica:</b> Constitución +1.</li><li><b>Conocimiento de Artífice:</b> Doble bonificador de proficiencia en pruebas de Historia sobre objetos mágicos, alquímicos o aparatos tecnológicos.</li><li><b>Hojalatero:</b> Competencia con herramientas de hojalatero. Gastas 1 hr y 10 po para crear un artefacto de cuerda diminuto (Juguete, Encendedor o Caja de Música).</li></ul>` }
                    ]
                },
                { 
                    nombre: "Semielfo", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Carisma +2 y otras dos puntuaciones a tu elección aumentan en +1.</li>
                        <li><b>Edad:</b> Madurez a los 20, exceden los 180 años.</li>
                        <li><b>Alineamiento:</b> Caótico en general.</li>
                        <li><b>Tamaño:</b> Mediano (5 a 6 pies).</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Ascendencia Feérica:</b> Ventaja en salvaciones contra Encantamiento, no te pueden dormir con magia.</li>
                        <li><b>Versatilidad con Habilidades:</b> Competencia en 2 habilidades a tu elección.</li>
                        <li><b>Idiomas:</b> Común, Élfico y otro idioma de tu elección.</li>
                    </ul>`
                },
                { 
                    nombre: "Semiorco", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Fuerza +2, Constitución +1.</li>
                        <li><b>Edad:</b> Madurez a los 14, viven hasta 75 años.</li>
                        <li><b>Alineamiento:</b> Caótico, con tendencia al mal.</li>
                        <li><b>Tamaño:</b> Mediano (5 a más de 6 pies).</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Amenazante:</b> Competencia en Intimidación.</li>
                        <li><b>Resistencia Incansable:</b> Cuando caes a 0 PG pero no mueres directamente, quedas a 1 PG en su lugar (1/descanso prolongado).</li>
                        <li><b>Ataques Salvajes:</b> Cuando logras un crítico con un arma c/c, tiras un dado de daño del arma adicional.</li>
                        <li><b>Idiomas:</b> Común y Orco.</li>
                    </ul>`
                },
                { 
                    nombre: "Tiflin", 
                    rasgos: `<ul class="race-traits">
                        <li><b>Incremento de Puntuación:</b> Inteligencia +1, Carisma +2.</li>
                        <li><b>Edad:</b> Madurez similar a los humanos pero viven unos pocos años más.</li>
                        <li><b>Alineamiento:</b> Caótico.</li>
                        <li><b>Tamaño:</b> Mediano.</li>
                        <li><b>Velocidad:</b> 30 pies.</li>
                        <li><b>Visión en la Oscuridad:</b> 60 pies.</li>
                        <li><b>Resistencia Infernal:</b> Resistencia al daño por fuego.</li>
                        <li><b>Legado Infernal:</b> Conoces el truco <i>taumaturgia</i>. A nivel 3 ganas <i>reprensión infernal</i> (1/día). A nivel 5 ganas <i>oscuridad</i> (1/día). Carisma es tu característica de lanzamiento.</li>
                        <li><b>Idiomas:</b> Común e Infernal.</li>
                    </ul>`
                }
            ],
            "Trasfondos": [
                { nombre: "Acólito", habilidades: "Perspicacia, Religión", desc: "Has pasado tu vida al servicio de un templo. Ganas la característica: Refugio de los Fieles." },
                { nombre: "Criminal", habilidades: "Engaño, Sigilo", desc: "Criminal experimentado. Ganas la característica: Contacto Criminal." },
                { nombre: "Erudito/Sabio", habilidades: "Arcano, Historia", desc: "Pasaste años aprendiendo. Ganas la característica: Investigador." },
                { nombre: "Héroe del Pueblo", habilidades: "Trato con Animales, Supervivencia", desc: "Vienes de una clase humilde, destinado a más. Ganas la característica: Hospitalidad Rústica." },
                { nombre: "Soldado", habilidades: "Atletismo, Intimidación", desc: "La guerra ha sido tu vida. Ganas la característica: Rango Militar." }
            ]
        };

        // Render Navigation
        const navMenu = document.getElementById('nav-menu');
        Object.keys(database).forEach((category, index) => {
            const btn = document.createElement('button');
            btn.textContent = category;
            btn.onclick = () => {
                document.getElementById('global-search').value = ""; // Limpiar buscador global al cambiar pestaña
                renderSection(category);
            };
            if (index === 0) btn.className = 'active';
            navMenu.appendChild(btn);
        });

        const contentArea = document.getElementById('content-area');
        const globalSearch = document.getElementById('global-search');

        // Función Matemática de Búsqueda Normalizada
        const normalizeStr = str => str.normalize("NFD").replace(/[\u0300-\u036f]/g, "").toLowerCase();

        function renderSection(category) {
            // Update Active Nav
            document.querySelectorAll('#nav-menu button').forEach(btn => btn.classList.remove('active'));
            const activeBtn = Array.from(document.querySelectorAll('#nav-menu button')).find(b => b.textContent === category);
            if(activeBtn) activeBtn.classList.add('active');

            // Resetear la selección específica de clase o raza al cambiar la pestaña
            currentSelectedRace = null;
            currentSelectedClass = null;

            let html = `
                <div class="section-header">
                    <h2 class="section-title">${category}</h2>
                    <input type="text" class="search-bar" id="local-search" placeholder="Buscar en ${category}..." style="max-width: 300px;">
                </div>
                <div id="section-results"></div>
            `;
            contentArea.innerHTML = html;

            const localSearch = document.getElementById('local-search');
            const resultsArea = document.getElementById('section-results');

            function updateResults(query) {
                const data = database[category];
                const filteredData = data.filter(item => {
                    if (!query) return true;
                    // Búsqueda profunda en los valores del objeto (útil para el HTML interno de las razas y clases)
                    const searchStr = normalizeStr(JSON.stringify(item));
                    return searchStr.includes(normalizeStr(query));
                });

                if (filteredData.length === 0) {
                    resultsArea.innerHTML = `<p>No se encontraron resultados para "${query}".</p>`;
                    return;
                }

                let resultHtml = "";

                // LÓGICA ESPECIAL PARA RAZAS
                if (category === "Razas") {
                    if (currentSelectedRace) {
                        let item = currentSelectedRace;
                        resultHtml += `<button class="search-bar" style="cursor:pointer; width:auto; margin-bottom:20px; background-color: var(--surface-color);" onclick="currentSelectedRace=null; document.getElementById('local-search').dispatchEvent(new Event('input'))">&larr; Volver a la lista de Razas</button>`;
                        
                        resultHtml += `<div class="card" style="transform:none; cursor:default; border-color:var(--primary-color);">`;
                        resultHtml += `<h3 style="font-size: 1.8em; border-bottom: 1px solid var(--border-color); padding-bottom: 10px;">${item.nombre}</h3>`;
                        resultHtml += `<div class="race-traits">${item.rasgos}</div>`;
                        
                        if (item.subrazas && item.subrazas.length > 0) {
                            resultHtml += `<h4 style="color: var(--primary-color); margin-top: 20px; border-bottom: 1px solid var(--border-color); padding-bottom: 5px;">Subrazas</h4>`;
                            item.subrazas.forEach(sub => {
                                resultHtml += `<div class="race-subrace">`;
                                resultHtml += `<h5>${sub.nombre}</h5>`;
                                resultHtml += `<div>${sub.rasgos}</div>`;
                                resultHtml += `</div>`;
                            });
                        }
                        resultHtml += `</div>`;
                        resultsArea.innerHTML = resultHtml;
                    } else {
                        // Vista por defecto: Grid de botones/tarjetas de Razas
                        resultHtml += `<div class="card-grid">`;
                        filteredData.forEach(item => {
                            resultHtml += `<div class="card" style="cursor:pointer; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center;" onclick="currentSelectedRace=database['Razas'].find(r=>r.nombre==='${item.nombre}'); document.getElementById('local-search').dispatchEvent(new Event('input'))">`;
                            resultHtml += `<h3 style="font-size: 1.5em; margin-bottom: 10px;">${item.nombre}</h3>`;
                            resultHtml += `<span style="color: var(--text-secondary); font-size: 0.9em;">Click para ver rasgos${item.subrazas ? ' y subrazas' : ''}</span>`;
                            resultHtml += `</div>`;
                        });
                        resultHtml += `</div>`;
                        resultsArea.innerHTML = resultHtml;
                    }
                    return; 
                }

                // LÓGICA ESPECIAL PARA CLASES
                if (category === "Clases") {
                    if (currentSelectedClass) {
                        let item = currentSelectedClass;
                        resultHtml += `<button class="search-bar" style="cursor:pointer; width:auto; margin-bottom:20px; background-color: var(--surface-color);" onclick="currentSelectedClass=null; document.getElementById('local-search').dispatchEvent(new Event('input'))">&larr; Volver a la lista de Clases</button>`;
                        
                        resultHtml += `<div class="card" style="transform:none; cursor:default; border-color:var(--primary-color);">`;
                        resultHtml += `<h3 style="font-size: 2em; border-bottom: 1px solid var(--border-color); padding-bottom: 10px;">${item.nombre}</h3>`;
                        resultHtml += `<p style="font-size: 1.1em; color: var(--text-secondary);"><i>${item.descripcion}</i></p>`;
                        
                        if (item.creacion_rapida) {
                            resultHtml += `<div class="race-subrace" style="border-left-color: #64b5f6;">
                                <h5 style="color: #64b5f6;">Creación Rápida</h5>
                                <p style="margin:0;">${item.creacion_rapida}</p>
                            </div>`;
                        }

                        resultHtml += `<h4 style="color: var(--primary-color); margin-top: 25px; border-bottom: 1px solid var(--border-color); padding-bottom: 5px;">Rasgos Básicos</h4>`;
                        resultHtml += `<div class="race-traits">${item.rasgos_basicos}</div>`;
                        
                        if(item.niveles && item.niveles.length > 0){
                            resultHtml += `<h4 style="color: var(--primary-color); margin-top: 25px; border-bottom: 1px solid var(--border-color); padding-bottom: 5px;">Progresión de Clase</h4>`;
                            resultHtml += `<div style="display: flex; flex-direction: column; gap: 10px; margin-top: 15px;">`;
                            item.niveles.forEach(n => {
                                resultHtml += `<div class="level-card">
                                    <h5>Nivel ${n.nivel}</h5>
                                    <div style="font-size:0.95em; line-height:1.5;">${n.rasgos}</div>
                                </div>`;
                            });
                            resultHtml += `</div>`;
                        }

                        if (item.subclases && item.subclases.length > 0) {
                            resultHtml += `<h4 style="color: var(--primary-color); margin-top: 30px; border-bottom: 1px solid var(--border-color); padding-bottom: 5px;">${item.subclases_titulo || "Subclases"}</h4>`;
                            item.subclases.forEach(sub => {
                                resultHtml += `<div class="race-subrace">`;
                                resultHtml += `<h5 style="font-size:1.3em;">${sub.nombre}</h5>`;
                                resultHtml += `<div style="line-height:1.5;">${sub.rasgos}</div>`;
                                resultHtml += `</div>`;
                            });
                        }

                        if (item.invocaciones) {
                            resultHtml += item.invocaciones;
                        }
                        
                        resultHtml += `</div>`;
                        resultsArea.innerHTML = resultHtml;
                    } else {
                        // Vista por defecto: Grid de botones/tarjetas de Clases
                        resultHtml += `<div class="card-grid">`;
                        filteredData.forEach(item => {
                            resultHtml += `<div class="card" style="cursor:pointer; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center;" onclick="currentSelectedClass=database['Clases'].find(c=>c.nombre==='${item.nombre}'); document.getElementById('local-search').dispatchEvent(new Event('input'))">`;
                            resultHtml += `<h3 style="font-size: 1.5em; margin-bottom: 10px;">${item.nombre}</h3>`;
                            resultHtml += `<span style="color: var(--text-secondary); font-size: 0.9em;">Click para ver progresión, rasgos y subclases</span>`;
                            resultHtml += `</div>`;
                        });
                        resultHtml += `</div>`;
                        resultsArea.innerHTML = resultHtml;
                    }
                    return;
                }

                // Lógica original para Armaduras y Armas
                if (category === "Armaduras" || category === "Armas") {
                    resultHtml += `<table><tr>`;
                    Object.keys(filteredData[0]).forEach(key => {
                        resultHtml += `<th>${key.charAt(0).toUpperCase() + key.slice(1).replace('_', ' ')}</th>`;
                    });
                    resultHtml += `</tr>`;
                    
                    filteredData.forEach(item => {
                        resultHtml += `<tr>`;
                        Object.values(item).forEach(val => {
                            resultHtml += `<td>${val}</td>`;
                        });
                        resultHtml += `</tr>`;
                    });
                    resultHtml += `</table>`;
                } else {
                    // Lógica original para el resto (Trasfondos, Conjuros)
                    resultHtml += `<div class="card-grid">`;
                    filteredData.forEach(item => {
                        resultHtml += `<div class="card"><h3>${item.nombre}</h3>`;
                        Object.entries(item).forEach(([key, val]) => {
                            if (key !== 'nombre') {
                                if(key === 'desc') {
                                    resultHtml += `<p>${val}</p>`;
                                } else {
                                    resultHtml += `<span class="tag"><b>${key.charAt(0).toUpperCase() + key.slice(1).replace('_', ' ')}:</b> ${val}</span>`;
                                }
                            }
                        });
                        resultHtml += `</div>`;
                    });
                    resultHtml += `</div>`;
                }
                resultsArea.innerHTML = resultHtml;
            }

            updateResults("");

            localSearch.addEventListener('input', (e) => {
                updateResults(e.target.value);
            });
        }

        function renderGlobalSearch(query) {
            document.querySelectorAll('#nav-menu button').forEach(btn => btn.classList.remove('active'));
            let html = `<h2 class="section-title">Resultados de búsqueda: "${query}"</h2><br>`;
            let foundAny = false;

            Object.keys(database).forEach(category => {
                const filteredData = database[category].filter(item => {
                    const searchStr = normalizeStr(JSON.stringify(item));
                    return searchStr.includes(normalizeStr(query));
                });

                if(filteredData.length > 0) {
                    foundAny = true;
                    html += `<h3 style="color: var(--text-secondary); border-bottom: 1px solid var(--border-color); padding-bottom: 5px;">En ${category}</h3>`;
                    
                    if (category === "Razas") {
                        // Tarjeta especial para resultados de búsqueda global en razas
                        html += `<div class="card-grid" style="margin-bottom: 30px;">`;
                        filteredData.forEach(item => {
                            html += `<div class="card" style="cursor:pointer;" onclick="document.getElementById('global-search').value=''; renderSection('Razas'); currentSelectedRace=database['Razas'].find(r=>r.nombre==='${item.nombre}'); document.getElementById('local-search').dispatchEvent(new Event('input'))">`;
                            html += `<h3>${item.nombre}</h3>`;
                            html += `<p>Vuelve a la pestaña <b>Razas</b> o haz clic aquí para ver todos sus detalles.</p>`;
                            html += `</div>`;
                        });
                        html += `</div>`;
                    }
                    else if (category === "Clases") {
                        // Tarjeta especial para resultados de búsqueda global en clases
                        html += `<div class="card-grid" style="margin-bottom: 30px;">`;
                        filteredData.forEach(item => {
                            html += `<div class="card" style="cursor:pointer;" onclick="document.getElementById('global-search').value=''; renderSection('Clases'); currentSelectedClass=database['Clases'].find(c=>c.nombre==='${item.nombre}'); document.getElementById('local-search').dispatchEvent(new Event('input'))">`;
                            html += `<h3>${item.nombre}</h3>`;
                            html += `<p>Vuelve a la pestaña <b>Clases</b> o haz clic aquí para ver progresión.</p>`;
                            html += `</div>`;
                        });
                        html += `</div>`;
                    }
                    else if (category === "Armaduras" || category === "Armas") {
                        html += `<table style="margin-bottom: 30px;"><tr>`;
                        Object.keys(filteredData[0]).forEach(key => {
                            html += `<th>${key.charAt(0).toUpperCase() + key.slice(1).replace('_', ' ')}</th>`;
                        });
                        html += `</tr>`;
                        filteredData.forEach(item => {
                            html += `<tr>`;
                            Object.values(item).forEach(val => {
                                html += `<td>${val}</td>`;
                            });
                            html += `</tr>`;
                        });
                        html += `</table>`;
                    } else {
                        html += `<div class="card-grid" style="margin-bottom: 30px;">`;
                        filteredData.forEach(item => {
                            html += `<div class="card"><h3>${item.nombre}</h3>`;
                            Object.entries(item).forEach(([key, val]) => {
                                if (key !== 'nombre' && key !== 'desc') {
                                    html += `<span class="tag"><b>${key.charAt(0).toUpperCase() + key.slice(1)}:</b> ${val}</span>`;
                                }
                            });
                            if(item.desc) html += `<p>${item.desc}</p>`;
                            html += `</div>`;
                        });
                        html += `</div>`;
                    }
                }
            });

            if(!foundAny) html += `<p>La búsqueda no arrojó resultados.</p>`;
            contentArea.innerHTML = html;
        }

        globalSearch.addEventListener('input', (e) => {
            const query = e.target.value;
            if (query.length > 1) {
                renderGlobalSearch(query);
            } else {
                const activeBtn = document.querySelector('#nav-menu button.active') || document.querySelector('#nav-menu button');
                renderSection(activeBtn.textContent);
            }
        });

        // Iniciar la vista
        renderSection("Armaduras");

    </script>
</body>
</html>
