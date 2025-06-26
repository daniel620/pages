---
title: "Snake Game - Classic Arcade Experience"
weight: 0
tags: ["game", "arcade", "javascript", "canvas"]
categories: ["demo"]
---

# Classic Snake Game - Portal Edition

Experience the enhanced arcade classic! Control the snake to eat food and grow longer. Now with portal walls - go through walls and emerge on the other side! Only hitting yourself ends the game.

## Game Controls

- **Arrow Keys** or **WASD** to move the snake
- **Space** to pause/resume
- **R** to restart
- **New Feature**: Walk through walls to emerge on the opposite side!

<div id="snake-game-demo">
  <div id="game-info" style="display: flex; justify-content: space-between; margin-bottom: 10px; font-weight: bold;">
    <div>Score: <span id="score">0</span></div>
    <div>High Score: <span id="high-score">0</span></div>
    <div id="game-status">Press any arrow key to start - Portal walls enabled!</div>
  </div>
  
  <canvas id="game-canvas" width="400" height="400" style="border: 2px solid #333; background: #000; display: block; margin: 0 auto; touch-action: none;"></canvas>
  
  <div style="text-align: center; margin-top: 15px; color: #666; font-size: 14px;">
    <div id="desktop-controls">Use arrow keys or WASD to control • Space to pause • R to restart • Walk through walls!</div>
    <div id="mobile-controls" style="display: none;">
      <div style="margin: 15px 0; font-weight: bold; color: #333; font-size: 16px;">Touch controls - Portal walls enabled:</div>
      <div style="margin: 15px 0;">
        <button id="mobile-up" style="padding: 20px 28px; margin: 8px; background: #228B22; color: white; border: none; border-radius: 12px; font-size: 24px; touch-action: manipulation; user-select: none; min-width: 70px; min-height: 70px; box-shadow: 0 4px 8px rgba(0,0,0,0.2);">↑</button>
      </div>
      <div style="display: flex; justify-content: center; align-items: center; gap: 8px;">
        <button id="mobile-left" style="padding: 20px 28px; margin: 8px; background: #228B22; color: white; border: none; border-radius: 12px; font-size: 24px; touch-action: manipulation; user-select: none; min-width: 70px; min-height: 70px; box-shadow: 0 4px 8px rgba(0,0,0,0.2);">←</button>
        <button id="mobile-down" style="padding: 20px 28px; margin: 8px; background: #228B22; color: white; border: none; border-radius: 12px; font-size: 24px; touch-action: manipulation; user-select: none; min-width: 70px; min-height: 70px; box-shadow: 0 4px 8px rgba(0,0,0,0.2);">↓</button>
        <button id="mobile-right" style="padding: 20px 28px; margin: 8px; background: #228B22; color: white; border: none; border-radius: 12px; font-size: 24px; touch-action: manipulation; user-select: none; min-width: 70px; min-height: 70px; box-shadow: 0 4px 8px rgba(0,0,0,0.2);">→</button>
      </div>
    </div>
    <div style="margin-top: 20px;">
      <button id="pause-btn" style="padding: 15px 25px; margin: 8px; background: #666; color: white; border: none; border-radius: 8px; font-size: 16px; touch-action: manipulation; min-width: 100px; box-shadow: 0 2px 4px rgba(0,0,0,0.2);">Pause</button>
      <button id="restart-btn" style="padding: 15px 25px; margin: 8px; background: #dc3545; color: white; border: none; border-radius: 8px; font-size: 16px; touch-action: manipulation; min-width: 100px; box-shadow: 0 2px 4px rgba(0,0,0,0.2);">Restart</button>
    </div>
  </div>
</div>

<script>
class SnakeGame {
  constructor(canvas) {
    this.canvas = canvas;
    this.ctx = canvas.getContext('2d');
    this.gridSize = 20;
    this.tileCount = canvas.width / this.gridSize;
    
    this.snake = [
      {x: 10, y: 10}
    ];
    this.food = this.generateFood();
    this.dx = 0;
    this.dy = 0;
    this.score = 0;
    this.gameRunning = false;
    this.gamePaused = false;
    
    this.loadHighScore();
    this.setupEventListeners();
    this.setupMobileControls();
    this.draw();
  }
  
  loadHighScore() {
    this.highScore = localStorage.getItem('snakeHighScore') || 0;
    document.getElementById('high-score').textContent = this.highScore;
  }
  
  saveHighScore() {
    if (this.score > this.highScore) {
      this.highScore = this.score;
      localStorage.setItem('snakeHighScore', this.highScore);
      document.getElementById('high-score').textContent = this.highScore;
    }
  }
  
  setupEventListeners() {
    document.addEventListener('keydown', (e) => {
      if (!this.gameRunning && (e.key.includes('Arrow') || 'wasdWASD'.includes(e.key))) {
        this.startGame();
      }
      
      if (this.gameRunning && !this.gamePaused) {
        this.changeDirection(e);
      }
      
      if (e.key === ' ') {
        e.preventDefault();
        this.togglePause();
      }
      
      if (e.key.toLowerCase() === 'r') {
        this.restart();
      }
    });
    
    document.getElementById('pause-btn').addEventListener('click', () => {
      this.togglePause();
    });
    
    document.getElementById('restart-btn').addEventListener('click', () => {
      this.restart();
    });
  }
  
  setupMobileControls() {
    // Detect mobile devices more reliably
    const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) || 
                     'ontouchstart' in window || 
                     navigator.maxTouchPoints > 0;
    
    if (isMobile) {
      // Show mobile controls and hide desktop instructions
      document.getElementById('mobile-controls').style.display = 'block';
      document.getElementById('desktop-controls').style.display = 'none';
      
      // Auto-start game on first touch for mobile
      let gameStarted = false;
      const autoStart = () => {
        if (!gameStarted && !this.gameRunning) {
          this.startGame();
          gameStarted = true;
        }
      };
      
      // Add touch event listeners with haptic feedback simulation
      const addTouchListener = (id, dx, dy) => {
        const button = document.getElementById(id);
        
        button.addEventListener('touchstart', (e) => {
          e.preventDefault();
          button.style.transform = 'scale(0.9)';
          button.style.background = '#1e7e1e';
          button.style.boxShadow = '0 2px 4px rgba(0,0,0,0.3)';
          this.handleMobileDirection(dx, dy);
          autoStart();
        });
        
        button.addEventListener('touchend', (e) => {
          e.preventDefault();
          button.style.transform = 'scale(1)';
          button.style.background = '#228B22';
          button.style.boxShadow = '0 4px 8px rgba(0,0,0,0.2)';
        });
        
        // Fallback click listener
        button.addEventListener('click', (e) => {
          e.preventDefault();
          this.handleMobileDirection(dx, dy);
          autoStart();
        });
      };
      
      addTouchListener('mobile-up', 0, -1);
      addTouchListener('mobile-down', 0, 1);
      addTouchListener('mobile-left', -1, 0);
      addTouchListener('mobile-right', 1, 0);
      
      // Add swipe gestures for additional control
      this.addSwipeControls();
    }
  }
  
  addSwipeControls() {
    let touchStartX = 0;
    let touchStartY = 0;
    let touchEndX = 0;
    let touchEndY = 0;
    
    const minSwipeDistance = 30;
    
    this.canvas.addEventListener('touchstart', (e) => {
      e.preventDefault();
      touchStartX = e.changedTouches[0].screenX;
      touchStartY = e.changedTouches[0].screenY;
    });
    
    this.canvas.addEventListener('touchend', (e) => {
      e.preventDefault();
      touchEndX = e.changedTouches[0].screenX;
      touchEndY = e.changedTouches[0].screenY;
      
      const deltaX = touchEndX - touchStartX;
      const deltaY = touchEndY - touchStartY;
      
      if (Math.abs(deltaX) > minSwipeDistance || Math.abs(deltaY) > minSwipeDistance) {
        if (Math.abs(deltaX) > Math.abs(deltaY)) {
          // Horizontal swipe
          if (deltaX > 0) {
            this.handleMobileDirection(1, 0); // Right
          } else {
            this.handleMobileDirection(-1, 0); // Left
          }
        } else {
          // Vertical swipe
          if (deltaY > 0) {
            this.handleMobileDirection(0, 1); // Down
          } else {
            this.handleMobileDirection(0, -1); // Up
          }
        }
        
        if (!this.gameRunning) {
          this.startGame();
        }
      }
    });
  }
  
  handleMobileDirection(dx, dy) {
    if (!this.gameRunning) {
      this.startGame();
    }
    
    if (this.gameRunning && !this.gamePaused) {
      if (this.dx !== -dx || this.dy !== -dy) {
        this.dx = dx;
        this.dy = dy;
      }
    }
  }
  
  startGame() {
    this.gameRunning = true;
    this.gamePaused = false;
    document.getElementById('game-status').textContent = 'Playing...';
    document.getElementById('pause-btn').textContent = 'Pause';
    
    // Adjust game speed for mobile devices
    const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
    const gameSpeed = isMobile ? 200 : 150; // Slightly slower on mobile for better control
    
    this.gameLoop = setInterval(() => this.update(), gameSpeed);
  }
  
  togglePause() {
    if (!this.gameRunning) return;
    
    this.gamePaused = !this.gamePaused;
    
    if (this.gamePaused) {
      clearInterval(this.gameLoop);
      document.getElementById('game-status').textContent = 'Paused';
      document.getElementById('pause-btn').textContent = 'Resume';
    } else {
      document.getElementById('game-status').textContent = 'Playing...';
      document.getElementById('pause-btn').textContent = 'Pause';
      
      // Use the same speed adjustment for resume
      const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
      const gameSpeed = isMobile ? 200 : 150;
      
      this.gameLoop = setInterval(() => this.update(), gameSpeed);
    }
  }
  
  restart() {
    clearInterval(this.gameLoop);
    this.snake = [{x: 10, y: 10}];
    this.food = this.generateFood();
    this.dx = 0;
    this.dy = 0;
    this.score = 0;
    this.gameRunning = false;
    this.gamePaused = false;
    
    document.getElementById('score').textContent = this.score;
    document.getElementById('game-status').textContent = 'Press any arrow key to start - Portal walls enabled!';
    document.getElementById('pause-btn').textContent = 'Pause';
    
    this.draw();
  }
  
  changeDirection(e) {
    const LEFT_KEY = e.key === 'ArrowLeft' || e.key.toLowerCase() === 'a';
    const RIGHT_KEY = e.key === 'ArrowRight' || e.key.toLowerCase() === 'd';
    const UP_KEY = e.key === 'ArrowUp' || e.key.toLowerCase() === 'w';
    const DOWN_KEY = e.key === 'ArrowDown' || e.key.toLowerCase() === 's';
    
    if (LEFT_KEY && this.dx !== 1) {
      this.dx = -1;
      this.dy = 0;
    }
    
    if (UP_KEY && this.dy !== 1) {
      this.dx = 0;
      this.dy = -1;
    }
    
    if (RIGHT_KEY && this.dx !== -1) {
      this.dx = 1;
      this.dy = 0;
    }
    
    if (DOWN_KEY && this.dy !== -1) {
      this.dx = 0;
      this.dy = 1;
    }
  }
  
  update() {
    if (this.gamePaused) return;
    
    this.moveSnake();
    
    if (this.checkCollision()) {
      this.gameOver();
      return;
    }
    
    if (this.checkFoodCollision()) {
      this.score += 10;
      document.getElementById('score').textContent = this.score;
      this.food = this.generateFood();
    } else {
      this.snake.pop();
    }
    
    this.draw();
  }
  
  moveSnake() {
    let newX = this.snake[0].x + this.dx;
    let newY = this.snake[0].y + this.dy;
    
    // Wrap around walls (穿墙效果)
    if (newX < 0) {
      newX = this.tileCount - 1;
    } else if (newX >= this.tileCount) {
      newX = 0;
    }
    
    if (newY < 0) {
      newY = this.tileCount - 1;
    } else if (newY >= this.tileCount) {
      newY = 0;
    }
    
    const head = {x: newX, y: newY};
    this.snake.unshift(head);
  }
  
  checkCollision() {
    const head = this.snake[0];
    
    // 移除墙壁碰撞检测 - 现在可以穿墙了！
    // Wall collision removed - now wraps around walls!
    
    // Self collision - 只检查撞到自己
    for (let i = 1; i < this.snake.length; i++) {
      if (head.x === this.snake[i].x && head.y === this.snake[i].y) {
        return true;
      }
    }
    
    return false;
  }
  
  checkFoodCollision() {
    const head = this.snake[0];
    return head.x === this.food.x && head.y === this.food.y;
  }
  
  generateFood() {
    let food;
    do {
      food = {
        x: Math.floor(Math.random() * this.tileCount),
        y: Math.floor(Math.random() * this.tileCount)
      };
    } while (this.snake.some(segment => segment.x === food.x && segment.y === food.y));
    
    return food;
  }
  
  gameOver() {
    clearInterval(this.gameLoop);
    this.gameRunning = false;
    this.gamePaused = false;
    this.saveHighScore();
    
    document.getElementById('game-status').textContent = 'Game Over! Only self-collision ends the game';
    document.getElementById('pause-btn').textContent = 'Pause';
    
    // Flash effect
    this.ctx.fillStyle = 'rgba(255, 0, 0, 0.3)';
    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    
    setTimeout(() => this.draw(), 200);
  }
  
  draw() {
    // Clear canvas
    this.ctx.fillStyle = '#000';
    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    
    // Draw portal effect on borders (subtle glow)
    this.ctx.strokeStyle = '#4169E1';
    this.ctx.lineWidth = 3;
    this.ctx.setLineDash([5, 5]);
    this.ctx.strokeRect(1, 1, this.canvas.width - 2, this.canvas.height - 2);
    this.ctx.setLineDash([]); // Reset line dash
    
    // Draw snake
    this.ctx.fillStyle = '#228B22';
    this.snake.forEach((segment, index) => {
      if (index === 0) {
        // Snake head - slightly different color
        this.ctx.fillStyle = '#32CD32';
      } else {
        this.ctx.fillStyle = '#228B22';
      }
      
      this.ctx.fillRect(
        segment.x * this.gridSize + 1,
        segment.y * this.gridSize + 1,
        this.gridSize - 2,
        this.gridSize - 2
      );
    });
    
    // Draw food
    this.ctx.fillStyle = '#FF4500';
    this.ctx.fillRect(
      this.food.x * this.gridSize + 1,
      this.food.y * this.gridSize + 1,
      this.gridSize - 2,
      this.gridSize - 2
    );
    
    // Draw grid lines (subtle)
    this.ctx.strokeStyle = '#111';
    this.ctx.lineWidth = 1;
    
    for (let i = 0; i <= this.tileCount; i++) {
      this.ctx.beginPath();
      this.ctx.moveTo(i * this.gridSize, 0);
      this.ctx.lineTo(i * this.gridSize, this.canvas.height);
      this.ctx.stroke();
      
      this.ctx.beginPath();
      this.ctx.moveTo(0, i * this.gridSize);
      this.ctx.lineTo(this.canvas.width, i * this.gridSize);
      this.ctx.stroke();
    }
  }
}

// Initialize the game
const canvas = document.getElementById('game-canvas');
const game = new SnakeGame(canvas);

// Prevent scrolling when using arrow keys
window.addEventListener('keydown', function(e) {
  if(['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].indexOf(e.code) > -1) {
    e.preventDefault();
  }
}, false);
</script>

<style>
#snake-game-demo {
  border: 1px solid #ddd;
  padding: 20px;
  margin: 24px 0;
  border-radius: 6px;
  background: #f9f9f9;
  color: #333;
  text-align: center;
}

#game-canvas {
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  border-radius: 4px;
}

button {
  font-family: inherit;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

button:hover {
  opacity: 0.85;
  transform: translateY(-1px);
}

button:active {
  transform: translateY(0);
}

#game-info {
  font-size: 16px;
  color: #333;
}

@media (max-width: 480px) {
  #game-canvas {
    width: 320px;
    height: 320px;
    max-width: 90vw;
    max-height: 90vw;
  }
  
  #snake-game-demo {
    padding: 15px;
    margin: 15px 0;
  }
  
  #game-info {
    font-size: 14px;
    flex-direction: column;
    gap: 8px;
    text-align: center;
  }
  
  #game-info > div {
    margin: 3px 0;
  }
  
  /* 移动端按钮增强 */
  #mobile-controls button {
    min-width: 80px !important;
    min-height: 80px !important;
    padding: 22px 30px !important;
    margin: 10px !important;
    font-size: 26px !important;
    border-radius: 15px !important;
  }
  
  #pause-btn, #restart-btn {
    padding: 18px 30px !important;
    margin: 10px !important;
    font-size: 18px !important;
    min-width: 120px !important;
  }
}

@media (max-width: 320px) {
  #game-canvas {
    width: 280px;
    height: 280px;
  }
  
  #snake-game-demo {
    padding: 10px;
  }
  
  #mobile-controls button {
    min-width: 70px !important;
    min-height: 70px !important;
    padding: 18px 24px !important;
    margin: 6px !important;
    font-size: 22px !important;
    border-radius: 12px !important;
  }
  
  #pause-btn, #restart-btn {
    padding: 15px 25px !important;
    margin: 8px !important;
    font-size: 16px !important;
    min-width: 100px !important;
  }
}

/* 增强触摸体验 */
@media (hover: none) and (pointer: coarse) {
  button {
    transition: all 0.1s ease !important;
  }
  
  #mobile-controls button:active {
    transform: scale(0.9) !important;
    background: #1e7e1e !important;
  }
  
  #pause-btn:active, #restart-btn:active {
    transform: scale(0.95) !important;
    opacity: 0.8 !important;
  }
}
</style>
