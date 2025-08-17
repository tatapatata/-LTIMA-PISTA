import React, { useState, useEffect } from "react";
import { motion, AnimatePresence } from "framer-motion";

const numRows = 8;
const numCols = 8;
const targetScore = 10000;

// Usamos diferentes tipos de flores para hacerlas distinguibles
const flowerTypes = ["🌻", "🌹", "🌷", "🌼", "🌺", "🌸"];

function getRandomFlower() {
  return flowerTypes[Math.floor(Math.random() * flowerTypes.length)];
}

function CandyCrushGame() {
  const [grid, setGrid] = useState([]);
  const [score, setScore] = useState(0);
  const [gameWon, setGameWon] = useState(false);

  useEffect(() => {
    const initialGrid = [];
    for (let row = 0; row < numRows; row++) {
      const rowArr = [];
      for (let col = 0; col < numCols; col++) {
        rowArr.push(getRandomFlower());
      }
      initialGrid.push(rowArr);
    }
    setGrid(initialGrid);
  }, []);

  useEffect(() => {
    if (score >= targetScore) {
      setGameWon(true);
    }
  }, [score]);

  const checkMatches = (newGrid) => {
    let newScore = 0;
    const matched = [];

    // Horizontal matches
    for (let row = 0; row < numRows; row++) {
      for (let col = 0; col < numCols - 2; col++) {
        const flower = newGrid[row][col];
        if (
          flower &&
          flower === newGrid[row][col + 1] &&
          flower === newGrid[row][col + 2]
        ) {
          let length = 3;
          while (
            col + length < numCols &&
            newGrid[row][col + length] === flower
          ) {
            length++;
          }
          for (let k = 0; k < length; k++) {
            matched.push([row, col + k]);
          }
          newScore += length * 110; // SIEMPRE enteros
        }
      }
    }

    // Vertical matches
    for (let col = 0; col < numCols; col++) {
      for (let row = 0; row < numRows - 2; row++) {
        const flower = newGrid[row][col];
        if (
          flower &&
          flower === newGrid[row + 1][col] &&
          flower === newGrid[row + 2][col]
        ) {
          let length = 3;
          while (
            row + length < numRows &&
            newGrid[row + length][col] === flower
          ) {
            length++;
          }
          for (let k = 0; k < length; k++) {
            matched.push([row + k, col]);
          }
          newScore += length * 110;
        }
      }
    }

    matched.forEach(([row, col]) => {
      newGrid[row][col] = null;
    });

    setScore((prev) => prev + newScore);

    return matched.length > 0;
  };

  const dropFlowers = (newGrid) => {
    for (let col = 0; col < numCols; col++) {
      let emptySpaces = 0;
      for (let row = numRows - 1; row >= 0; row--) {
        if (!newGrid[row][col]) {
          emptySpaces++;
        } else if (emptySpaces > 0) {
          newGrid[row + emptySpaces][col] = newGrid[row][col];
          newGrid[row][col] = null;
        }
      }
      for (let row = 0; row < emptySpaces; row++) {
        newGrid[row][col] = getRandomFlower();
      }
    }
  };

  const handleCellClick = (row, col) => {
    const newGrid = grid.map((r) => [...r]);
    const hasMatches = checkMatches(newGrid);

    if (hasMatches) {
      dropFlowers(newGrid);
      setGrid(newGrid);
    }
  };

  return (
    <div className="flex flex-col items-center justify-center min-h-screen bg-gradient-to-br from-pink-100 to-yellow-100 p-4">
      <h1 className="text-3xl font-bold mb-4">🌸 Candy Flowers 🌼</h1>
      <p className="mb-2 text-lg font-semibold">Puntaje: {score}</p>
      <div
        className="grid"
        style={{
          gridTemplateColumns: `repeat(${numCols}, 50px)`,
          gridTemplateRows: `repeat(${numRows}, 50px)`,
          gap: "4px",
        }}
      >
        {grid.map((row, rowIndex) =>
          row.map((flower, colIndex) => (
            <AnimatePresence key={`${rowIndex}-${colIndex}-${flower}`}>
              {flower && (
                <motion.div
                  key={`${rowIndex}-${colIndex}`}
                  initial={{ scale: 0, opacity: 0 }}
                  animate={{
                    scale: 1,
                    opacity: 1,
                    y: 0,
                    transition: { type: "spring", stiffness: 120, damping: 12 },
                  }}
                  exit={{ scale: 0, opacity: 0, transition: { duration: 0.3 } }}
                  onClick={() => handleCellClick(rowIndex, colIndex)}
                  className="flex items-center justify-center w-12 h-12 rounded-xl shadow-md border-2 border-black bg-white text-2xl cursor-pointer select-none"
                >
                  {flower}
                </motion.div>
              )}
            </AnimatePresence>
          ))
        )}
      </div>

      {gameWon && (
        <div className="mt-6 p-6 bg-white rounded-2xl shadow-xl text-center">
          <h2 className="text-2xl font-bold mb-2">🎉 ¡Ganaste! 🎉</h2>
          <p className="mb-4">Has llenado la barra con {score} puntos.</p>
          <a
            href="https://open.spotify.com/playlist/6rGxHrCj9w4WLERyNcsbBK?si=rLIOE8kHTDuNQpqXp7lUuw&pi=WxPawjn9RyWAr"
            target="_blank"
            rel="noopener noreferrer"
            className="px-4 py-2 bg-green-500 text-white rounded-lg shadow hover:bg-green-600"
          >
            💚 Escuchar Playlist Especial 💚
          </a>
        </div>
      )}
    </div>
  );
}

export default CandyCrushGame;
