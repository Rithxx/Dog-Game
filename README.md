import React, { useState, useEffect } from 'react';
import { Heart } from 'lucide-react';

const DogGame = () => {
  const [position, setPosition] = useState(0);
  const [treats, setTreats] = useState(0);
  const [isJumping, setIsJumping] = useState(false);
  const [obstacles, setObstacles] = useState([]);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setObstacles((prev) => [
        ...prev,
        { id: Date.now(), position: 100 }
      ].filter((obstacle) => obstacle.position > -10));
    }, 2000);

    return () => clearInterval(intervalId);
  }, []);

  useEffect(() => {
    const moveInterval = setInterval(() => {
      setObstacles((prev) =>
        prev.map((obstacle) => ({ ...obstacle, position: obstacle.position - 5 }))
      );
    }, 50);

    return () => clearInterval(moveInterval);
  }, []);

  const handleJump = () => {
    if (!isJumping) {
      setIsJumping(true);
      setPosition(30);
      setTimeout(() => {
        setPosition(0);
        setIsJumping(false);
      }, 500);
    }
  };

  useEffect(() => {
    const checkCollision = () => {
      obstacles.forEach((obstacle) => {
        if (
          obstacle.position <= 10 &&
          obstacle.position >= 0 &&
          position < 20
        ) {
          setTreats((prev) => prev + 1);
        }
      });
    };

    checkCollision();
  }, [obstacles, position]);

  return (
    <div className="relative w-full h-64 bg-blue-200 overflow-hidden" onClick={handleJump}>
      <div
        className="absolute bottom-0 left-0 w-16 h-16 bg-brown-500 transition-all duration-500"
        style={{ transform: `translateY(-${position}px)` }}
      >
        🐶
      </div>
      {obstacles.map((obstacle) => (
        <div
          key={obstacle.id}
          className="absolute bottom-0 w-8 h-8 bg-red-500"
          style={{ right: `${obstacle.position}%` }}
        >
          🦴
        </div>
      ))}
      <div className="absolute top-2 left-2 flex items-center">
        <Heart className="text-red-500 mr-2" />
        <span className="text-xl font-bold">{treats}</span>
      </div>
    </div>
  );
};

export default DogGame;
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
