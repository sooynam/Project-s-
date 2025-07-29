<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Classic Snake Game</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #1a1a2e;
            color: #e6e6e6;
            display: flex;
            flex-direction: column;
            align-items: center;
            height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }

        h1 {
            color: #4cc9f0;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
            margin-bottom: 10px;
        }

        .game-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            aspect-ratio: 1 / 1;
            margin: 20px 0;
            border: 3px solid #4cc9f0;
            border-radius: 5px;
            box-shadow: 0 0 20px rgba(76, 201, 240, 0.3);
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
            background-color: #16213e;
        }

        .score-display {
            font-size: 1.5rem;
            margin-bottom: 10px;
            color: #f72585;
            font-weight: bold;
        }

        .controls {
            margin-top: 10px;
            text-align: center;
            color: #a5a5a5;
        }

        .game-over {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background-color: rgba(0, 0, 0, 0.8);
            z-index: 10;
        }

        .game-over h2 {
            color: #f72585;
            font-size: 2.5rem;
            margin-bottom: 20px;
        }

        .restart-btn {
            padding: 10px 20px;
            background-color: #4cc9f0;
            border: none;
            border-radius: 5px;
            color: white;
            font-size: 1.2rem;
            cursor: pointer;
            transition: all 0.3s;
        }

        .restart-btn:hover {
            background-color: #f72585;
            transform: scale(1.05);
        }

        #food-img {
            display: none;
        }

        @media (max-width: 600px) {
            h1 {
                font-size: 1.5rem;
            }
            .score-display {
                font-size: 1.2rem;
            }
        }
    </style>
</head>
<body>
    <h1>Classic Snake Game</h1>
    <div class="score-display">Score: <span id="score">0</span></div>
    <div class="game-container">
        <canvas id="game-canvas"></canvas>
        <div class="game-over" id="game-over">
            <h2>Game Over!</h2>
            <button class="restart-btn" id="restart-btn">Play Again</button>
        </div>
    </div>
    <div class="controls">
        Use arrow keys to control the snake
    </div>

    <!-- Hidden food image for canvas rendering -->
    <img id="food-img" src="https://placehold.co/20x20" alt="Red shiny apple with green leaf for snake game food item">

    <script>
        // Game constants
        const GRID_SIZE = 20;
        const SNAKE_SPEED = 7; // frames per move
        
        // Game variables
        let canvas, ctx;
        let snake = [{x: 10, y: 10}];
        let food = {x: 0, y: 0};
        let direction = {x: 0, y: 0};
        let lastDirection = {x: 0, y: 0};
        let score = 0;
        let gameOver = false;
        let frameCount = 0;
        let foodImg;
        
        // DOM elements
        const scoreElement = document.getElementById('score');
        const gameOverElement = document.getElementById('game-over');
        const restartBtn = document.getElementById('restart-btn');

        // Initialize game
        function init() {
            canvas = document.getElementById('game-canvas');
            ctx = canvas.getContext('2d');
            
            // Set canvas dimensions
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);
            
            // Lo