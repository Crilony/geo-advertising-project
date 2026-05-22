# Отчёт о разработке игры «Арканоид»

### База игры и идея:
https://developer.mozilla.org/en-US/docs/Games/Tutorials/2D_breakout_game_Phaser

## 1. Исследование предметной области

### Цель
Разработать классическую аркадную игру «Арканоид» с элементами динамического усложнения — кирпичи постоянно надвигаются сверху, а игрок управляет битой для отбивания мяча.

### Анализ требований
| Категория | Требование |
|-----------|-------------|
| Игровая механика | Управление битой мышью/тачем, отскок мяча, разрушение кирпичей |
| Динамика | Кирпичи движутся вниз, новая волна при очистке экрана |
| Визуал | Тёмный фон, яркие розовые кирпичи, зелёная бита, жёлтый мяч |
| Интерфейс | Отображение счёта, жизней, кнопка перезапуска |
| Адаптивность | Поддержка мыши и сенсорного управления |

### Выбор технологий
- **HTML5 Canvas** — для отрисовки графики в реальном времени.
- **JavaScript (ES6)** — вся логика игры, физика, коллизии.
- **CSS3** — стилизация интерфейса, градиенты, адаптация.

### Этапы проектирования
1. Прототип с базовой физикой (мяч, бита, стены).
2. Добавление статичных кирпичей.
3. Реализация движения кирпичей вниз.
4. Система жизней и волн (усложнение).
5. Отрисовка и анимация.
6. Отладка коллизий и баланс скорости.

---

# Техническое руководство: Создаём свою игру «Арканоид» с нуля

> Для кого: начинающие веб-разработчики, знакомые с основами HTML, CSS и JS.

## Шаг 1. Подготовка среды

Создайте файл `index.html` и откройте его в любом современном браузере.

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Мой Арканоид</title>
    <style>
        /* Базовые стили (можно скопировать из финального кода) */
        body { margin: 0; background: #0a0f1e; display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        canvas { border-radius: 24px; box-shadow: 0 12px 28px rgba(0,0,0,0.5); cursor: none; }
        .info-panel { margin-top: 20px; display: flex; gap: 20px; color: white; }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="900" height="650"></canvas>
    <div class="info-panel">
        <div>Счёт: <span id="scoreValue">0</span></div>
        <div>Жизни: <span id="livesValue">3</span></div>
        <button id="restartButton">Новая игра</button>
    </div>
    <script src="game.js"></script>
</body>
</html>
```

## Шаг 2. Рисуем игровые объекты

В `game.js` получаем canvas и контекст:

```javascript
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const W = 900, H = 650;
```

### Бита (прямоугольник)

```javascript
const PADDLE_W = 120, PADDLE_H = 18;
let paddleX = (W - PADDLE_W) / 2;
const paddleY = H - PADDLE_H - 12;

function drawPaddle() {
    ctx.fillStyle = "#6fda3c";
    ctx.fillRect(paddleX, paddleY, PADDLE_W, PADDLE_H);
}
```

### Мяч (круг)

```javascript
let ball = {
    x: W/2, y: paddleY - 12, radius: 9,
    dx: 2.8, dy: -3.2
};

function drawBall() {
    ctx.beginPath();
    ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI*2);
    ctx.fillStyle = "#ffdd77";
    ctx.fill();
}
```

### Кирпичи (массив)

```javascript
const BRICK_W = 72, BRICK_H = 24;
const COLS = Math.floor(W / BRICK_W); // 12
const brickOffsetX = (W - (COLS * BRICK_W)) / 2;
let bricks = [];

function initBricks() {
    for(let row = 0; row < 5; row++) {
        for(let col = 0; col < COLS; col++) {
            bricks.push({
                x: brickOffsetX + col * BRICK_W,
                y: 45 + row * (BRICK_H + 2),
                w: BRICK_W, h: BRICK_H,
                status: 1  // 1 = активен, 0 = уничтожен
            });
        }
    }
}
```

## Шаг 3. Движение мяча и границы

```javascript
function updateBall() {
    ball.x += ball.dx;
    ball.y += ball.dy;

    // Стены
    if(ball.x + ball.radius > W || ball.x - ball.radius < 0) ball.dx = -ball.dx;
    if(ball.y - ball.radius < 0) ball.dy = -ball.dy;

    // Падение вниз
    if(ball.y + ball.radius > H) {
        loseLife();
    }
}
```

## Шаг 4. Управление битой мышью

```javascript
canvas.addEventListener('mousemove', (e) => {
    const rect = canvas.getBoundingClientRect();
    let mouseX = (e.clientX - rect.left) * (W / rect.width);
    mouseX = Math.min(Math.max(mouseX, 0), W - PADDLE_W);
    paddleX = mouseX;
});
```

## Шаг 5. Отскок от биты (с изменением угла)

```javascript
function checkPaddleCollision() {
    if(ball.y + ball.radius >= paddleY && ball.y - ball.radius <= paddleY + PADDLE_H &&
       ball.x + ball.radius > paddleX && ball.x - ball.radius < paddleX + PADDLE_W) {
        let hitPos = (ball.x - paddleX) / PADDLE_W;
        let angle = (hitPos - 0.5) * 1.2;
        let speed = Math.hypot(ball.dx, ball.dy);
        ball.dx = Math.sin(angle) * speed;
        ball.dy = -Math.cos(angle) * speed;
        ball.y = paddleY - ball.radius;
    }
}
```

## Шаг 6. Столкновение с кирпичами

```javascript
function handleBrickCollision() {
    for(let i = 0; i < bricks.length; i++) {
        let b = bricks[i];
        if(b.status === 0) continue;
        // Проверка пересечения
        if(ball.x + ball.radius > b.x && ball.x - ball.radius < b.x + b.w &&
           ball.y + ball.radius > b.y && ball.y - ball.radius < b.y + b.h) {
            b.status = 0;
            score += 10;
            updateScoreUI();
            // Меняем направление мяча
            if(ball.y + ball.radius > b.y && ball.y - ball.radius < b.y + b.h/2)
                ball.dy = -ball.dy;
            else
                ball.dx = -ball.dx;
            break;
        }
    }
}
```

## Шаг 7. Надвигающиеся кирпичи (уникальная механика)

```javascript
let bricksSpeedY = 0.75;

function moveBricksDown() {
    for(let b of bricks) {
        if(b.status === 1) b.y += bricksSpeedY;
    }
    // Добавляем новый ряд сверху, если самый верхний кирпич ушёл вниз
    let minY = Math.min(...bricks.filter(b=>b.status===1).map(b=>b.y));
    if(minY > 35) {
        let newRowY = minY - BRICK_H - 3;
        for(let col = 0; col < COLS; col++) {
            bricks.push({ x: brickOffsetX + col*BRICK_W, y: newRowY, w: BRICK_W, h: BRICK_H, status: 1 });
        }
    }
    // Проверка: кирпичи дошли до биты → потеря жизни
    for(let b of bricks) {
        if(b.status === 1 && b.y + b.h >= paddleY - 5) loseLife();
    }
}
```

## Шаг 8. Жизни и перезапуск

```javascript
let lives = 3, score = 0, gameRunning = true;

function loseLife() {
    lives--;
    updateLivesUI();
    if(lives <= 0) {
        gameRunning = false;
        alert("Game Over");
    } else {
        // Сброс позиции мяча на биту
        ball.x = paddleX + PADDLE_W/2;
        ball.y = paddleY - 15;
        ball.dx = 2.8 * (Math.random() > 0.5 ? 1 : -1);
        ball.dy = -3.2;
    }
}
```

## Шаг 9. Игровой цикл и отрисовка

```javascript
function gameLoop() {
    if(gameRunning) {
        updateBall();
        checkPaddleCollision();
        handleBrickCollision();
        moveBricksDown();
    }
    draw(); // рисуем всё
    requestAnimationFrame(gameLoop);
}
```

### Функция `draw()` объединяет:
- Очистку экрана
- Отрисовку кирпичей
- Биту
- Мяч
- UI-счёт, жизни

## Шаг 10. Запуск и кнопка рестарта

```javascript
function resetGame() {
    gameRunning = true;
    lives = 3; score = 0;
    initBricks();
    paddleX = (W - PADDLE_W)/2;
    ball = { x: W/2, y: paddleY-12, radius: 9, dx: 2.8, dy: -3.2 };
    updateScoreUI(); updateLivesUI();
}
document.getElementById('restartButton').onclick = resetGame;
initBricks();
gameLoop();
```

---

## Пример кода (финальный фрагмент)

Вот минимальный рабочий вариант для проверки идей (основные коллизии):

```javascript
// Только суть — полный код смотри в исходном brickgame.html
function update() {
    ball.x += ball.dx;
    ball.y += ball.dy;
    if(ball.x < 0 || ball.x > W) ball.dx = -ball.dx;
    if(ball.y < 0) ball.dy = -ball.dy;
    if(ball.y + ball.radius > paddleY && ball.x > paddleX && ball.x < paddleX+PADDLE_W) {
        ball.dy = -ball.dy;
        ball.y = paddleY - ball.radius;
    }
    // ... остальное
}
```

---

## Рекомендации для начинающих

| Проблема | Решение |
|----------|---------|
| Мяч застревает в кирпичах | После коллизии сдвигайте мяч на 2–3 пикселя |
| Слишком быстрое движение | Уменьшите `ball.dx/dy` или добавьте ограничение скорости |
| Не работает сенсор | Добавьте `touchmove` с `preventDefault()` |
| Кирпичи не надвигаются плавно | Используйте `requestAnimationFrame` и увеличивайте `bricksSpeedY` при новой волне |

---

## Заключение

Вы создали классический арканоид с уникальной механикой «надвигающихся кирпичей». Дальнейшее развитие:
- Добавление бонусов (расширение биты, несколько мячей).
- Уровни с разными типами кирпичей (неразрушаемые).
- Звуковые эффекты.
- Таблица рекордов.

Полный рабочий код находится в файле `brickgame.html` в папке game

