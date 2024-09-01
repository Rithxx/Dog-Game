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
