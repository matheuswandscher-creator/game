<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Super Arcade Soccer Pro</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            background: #141419;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            color: #fff;
            padding: 15px;
            overflow-x: hidden;
        }

        h1 {
            font-size: 2rem;
            text-transform: uppercase;
            color: #4eed58;
            margin-bottom: 10px;
            text-align: center;
            letter-spacing: 2px;
            text-shadow: 0 2px 10px rgba(79, 237, 88, 0.3);
        }

        #scoreboard {
            display: flex;
            align-items: center;
            gap: 25px;
            font-size: 1.8rem;
            font-weight: 800;
            margin-bottom: 15px;
            background: #1f1f26;
            padding: 10px 35px;
            border-radius: 50px;
            border: 2px solid #2d2d38;
            box-shadow: 0 5px 15px rgba(0,0,0,0.4);
        }

        .player-score { color: #3498db; }
        .cpu-score { color: #e74c3c; }

        /* Container do Jogo com tamanho fixado proporcionalmente */
        #gameContainer {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin: 0 auto;
        }

        canvas {
            width: 100%;
            height: auto;
            display: block;
            background: #2e7d32;
            border-radius: 12px;
            box-shadow: 0 12px 36px rgba(0,0,0,0.6);
            border: 4px solid #222;
        }

        /* Telas de Menu Intermediárias */
        .overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(15, 15, 20, 0.9);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 10;
            border-radius: 8px;
            transition: opacity 0.2s ease;
        }

        .hidden {
            display: none !important;
            opacity: 0;
        }

        .overlay h2 {
            font-size: 2.8rem;
            margin-bottom: 25px;
            text-transform: uppercase;
            text-align: center;
            font-weight: 900;
        }

        button {
            background: #4eed58;
            color: #000;
            border: none;
            padding: 14px 40px;
            font-size: 1.3rem;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(79, 237, 88, 0.4);
            transition: transform 0.1s, background-color 0.2s;
            text-transform: uppercase;
        }

        button:hover {
            background: #3cd146;
            transform: scale(1.03);
        }

        #goalMessage {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            font-size: 4.5rem;
            font-weight: 900;
            text-shadow: 3px 3px 0 #000, -3px -3px 0 #000, 3px -3px 0 #000, -3px 3px 0 #000;
            transition: transform 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            pointer-events: none;
            z-index: 5;
            text-transform: uppercase;
        }

        #goalMessage.show {
            transform: translate(-50%, -50%) scale(1);
        }

        /* Controles Mobile Baseados em PointerEvents Universais */
        #mobileControls {
            display: none;
            grid-template-columns: repeat(3, 65px);
            grid-template-rows: repeat(3, 65px);
            gap: 12px;
            margin-top: 20px;
        }

        .btn-dir {
            background: #22222b;
            border: 2px solid #3d3d4f;
            border-radius: 50%;
            color: #fff;
            font-size: 1.6rem;
            display: flex;
            align-items: center;
            justify-content: center;
            touch-action: none;
            cursor: pointer;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
        }

        .btn-dir:active {
            background: #4eed58;
            color: #000;
            border-color: #4eed58;
        }

        #btnUp { grid-column: 2; grid-row: 1; }
        #btnLeft { grid-column: 1; grid-row: 2; }
        #btnRight { grid-column: 3; grid-row: 2; }
        #btnDown { grid-column: 2; grid-row: 3; }

        @media (pointer: coarse) {
            #mobileControls { display: grid; }
            h1 { font-size: 1.5rem; }
        }
    </style>
</head>
<body>

    <h1>Arcade Soccer Pro</h1>

    <div id="scoreboard">
        <span class="player-score" id="pScore">0</span>
        <span>x</span>
        <span class="cpu-score" id="cScore">0</span>
    </div>

    <div id="gameContainer">
        <div id="startScreen" class="overlay">
            <h2 style="color: #4eed58;">Super Soccer</h2>
            <button id="startBtn">Jogar Partida</button>
        </div>

        <div id="gameOverScreen" class="overlay hidden">
            <h2 id="winnerText">Fim de Jogo</h2>
            <button id="restartBtn">Jogar Novamente</button>
        </div>

        <div id="goalMessage">GOL!</div>
        
        <canvas id="gameCanvas" width="800" height="500