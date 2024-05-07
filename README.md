<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo de Tiro</title>
    <style>
        canvas {
            border: 1px solid black;
            display: block;
            margin: 0 auto;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>

    <script>
        // Obtenha o contexto do canvas
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Definir a nave do jogador
        const player = {
            x: canvas.width / 2,
            y: canvas.height - 50,
            width: 50,
            height: 50,
            color: 'blue',
            speed: 5,
            isShooting: false,
            bullets: []
        };

        // Variáveis para armazenar as teclas pressionadas
        const keys = [];

        // Array para armazenar os inimigos
        const enemies = [];

        // Classe para definir os inimigos
        class Enemy {
            constructor(x, y, width, height, color, speed) {
                this.x = x;
                this.y = y;
                this.width = width;
                this.height = height;
                this.color = color;
                this.speed = speed;
            }

            draw() {
                ctx.fillStyle = this.color;
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }

            update() {
                this.y += this.speed;
            }
        }

        // Classe para definir os tiros
        class Bullet {
            constructor(x, y, width, height, color, speed) {
                this.x = x;
                this.y = y;
                this.width = width;
                this.height = height;
                this.color = color;
                this.speed = speed;
            }

            draw() {
                ctx.fillStyle = this.color;
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }

            update() {
                this.y -= this.speed;
            }
        }

        // Função para criar um novo inimigo
        function createEnemy() {
            const x = Math.random() * (canvas.width - 50);
            const y = 0;
            const width = 50;
            const height = 50;
            const color = 'red';
            const speed = 2;
            const newEnemy = new Enemy(x, y, width, height, color, speed);
            enemies.push(newEnemy);
        }

        // Função para criar um novo tiro
        function createBullet() {
            const x = player.x + player.width / 2 - 2.5;
            const y = player.y - 10;
            const width = 5;
            const height = 10;
            const color = 'green';
            const speed = 10;
            const newBullet = new Bullet(x, y, width, height, color, speed);
            player.bullets.push(newBullet);
        }

        // Função para desenhar todos os inimigos
        function drawEnemies() {
            enemies.forEach(enemy => {
                enemy.draw();
            });
        }

        // Função para desenhar todos os tiros
        function drawBullets() {
            player.bullets.forEach(bullet => {
                bullet.draw();
            });
        }

        // Função para atualizar a posição de todos os inimigos
        function updateEnemies() {
            enemies.forEach(enemy => {
                enemy.update();
            });
        }

        // Função para atualizar a posição de todos os tiros
        function updateBullets() {
            player.bullets.forEach(bullet => {
                bullet.update();
            });
        }

        // Função para desenhar a nave do jogador
        function drawPlayer() {
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        // Função para atualizar a posição do jogador
        function updatePlayerPosition() {
            // Mover para a esquerda
            if (keys['ArrowLeft'] || keys['a']) {
                if (player.x - player.speed > 0) {
                    player.x -= player.speed;
                }
            }
            // Mover para a direita
            if (keys['ArrowRight'] || keys['d']) {
                if (player.x + player.width + player.speed < canvas.width) {
                    player.x += player.speed;
                }
            }
        }

        // Event listener para detectar teclas pressionadas
        window.addEventListener('keydown', function(e) {
            keys[e.key] = true;

            // Atirar
            if (e.key === ' ') {
                player.isShooting = true;
            }
        });
        window.addEventListener('keyup', function(e) {
            keys[e.key] = false;

            // Parar de atirar
            if (e.key === ' ') {
                player.isShooting = false;
            }
        });

        // Função para detectar colisões entre dois retângulos
        function detectCollision(rect1, rect2) {
            return rect1.x < rect2.x + rect2.width &&
                   rect1.x + rect1.width > rect2.x &&
                   rect1.y < rect2.y + rect2.height &&
                   rect1.y + rect1.height > rect2.y;
        }

        // Função principal do jogo
        function main() {
            // Limpar o canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Criar inimigos aleatoriamente
            if (Math.random() < 0.02) {
                createEnemy();
            }

            // Atualizar e desenhar os inimigos
            updateEnemies();
            drawEnemies();

            // Atualizar e desenhar os tiros
            updateBullets();
            drawBullets();

            // Atualizar e desenhar o jogador
            updatePlayerPosition();
            drawPlayer();

            // Atirar
            if (player.isShooting) {
                createBullet();
            }

            // Detectar colisões entre os tiros e os inimigos
            player.bullets.forEach(bullet => {
                enemies.forEach(enemy => {
                    if (detectCollision(bullet, enemy)) {
                        // Remover o inimigo e o tiro
                        const enemyIndex = enemies.indexOf(enemy);
                        enemies.splice(enemyIndex, 1);

                        const bulletIndex = player.bullets.indexOf(bullet);
                        player.bullets.splice(bulletIndex, 1);
                    }
                });
            });

            // Chamar a função principal novamente
            requestAnimationFrame(main);
        }

        // Começar o jogo
        main();
    </script>
</body>
</html>
