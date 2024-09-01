<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Dog Game</title>
    <style>
        body { margin: 0; overflow: hidden; }
        #gameCanvas { background: skyblue; }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let dogY = canvas.height - 50;
        let isJumping = false;
        let score = 0;
        let bones = [];

        function drawDog() {
            ctx.fillStyle = 'brown';
            ctx.fillRect(50, dogY, 40, 40);
        }

        function drawBones() {
            ctx.fillStyle = 'white';
            bones.forEach(bone => {
                ctx.fillRect(bone.x, canvas.height - 30, 20, 20);
            });
        }

        function drawScore() {
            ctx.fillStyle = 'black';
            ctx.font = '20px Arial';
            ctx.fillText('Score: ' + score, 10, 30);
        }

        function updateGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            if (isJumping) {
                dogY -= 5;
                if (dogY <= canvas.height - 150) isJumping = false;
            } else if (dogY < canvas.height - 50) {
                dogY += 5;
            }

            bones.forEach((bone, index) => {
                bone.x -= 5;
                if (bone.x < -20) bones.splice(index, 1);
                if (bone.x < 90 && bone.x > 30 && dogY > canvas.height - 70) {
                    score++;
                    bones.splice(index, 1);
                }
            });

            if (Math.random() < 0.02) {
                bones.push({ x: canvas.width });
            }

            drawDog();
            drawBones();
            drawScore();

            requestAnimationFrame(updateGame);
        }

        canvas.addEventListener('click', () => {
            if (dogY >= canvas.height - 50) isJumping = true;
        });

        updateGame();
    </script>
</body>
</html>
