// Онлайн техаский покер на React с ботами
import React, { useEffect, useState, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";

const playersMock = [
  { id: 1, name: "Я", isActive: true, isBot: false },
  { id: 2, name: "Иван (бот)", isActive: false, isBot: true },
  { id: 3, name: "Саша (бот)", isActive: false, isBot: true },
  { id: 4, name: "Лена (бот)", isActive: false, isBot: true }
];

const PokerTable = () => {
  const [players, setPlayers] = useState(playersMock);
  const [currentTurn, setCurrentTurn] = useState(0);
  const [timeLeft, setTimeLeft] = useState(10);
  const timerRef = useRef(null);

  const nextTurn = () => {
    setCurrentTurn((prev) => (prev + 1) % players.length);
    setTimeLeft(10);
  };

  // Действие бота
  useEffect(() => {
    const currentPlayer = players[currentTurn];
    if (currentPlayer.isBot) {
      const delay = Math.floor(Math.random() * 5000) + 1000;
      const botTimer = setTimeout(() => {
        console.log(`${currentPlayer.name} делает ход.`);
        nextTurn();
      }, delay);
      return () => clearTimeout(botTimer);
    }
  }, [currentTurn]);

  useEffect(() => {
    timerRef.current = setInterval(() => {
      setTimeLeft((prev) => {
        if (prev === 1) {
          nextTurn();
          return 10;
        }
        return prev - 1;
      });
    }, 1000);
    return () => clearInterval(timerRef.current);
  }, [currentTurn]);

  return (
    <div className="p-6 grid gap-4">
      <h1 className="text-2xl font-bold">Онлайн Техасский Покер</h1>
      <div className="grid grid-cols-2 gap-4">
        {players.map((player, index) => (
          <Card key={player.id} className="p-4">
            <CardContent>
              <div className="font-semibold">{player.name}</div>
              <div className="mt-2">Карты: 🂠 🂠</div>
              {index === currentTurn && (
                <div className="text-red-500 font-bold mt-2">
                  Ход игрока • Осталось: {timeLeft} сек
                </div>
              )}
            </CardContent>
          </Card>
        ))}
      </div>
      <div className="mt-4">
        {players[currentTurn].isActive && !players[currentTurn].isBot ? (
          <div className="flex gap-2">
            <Button onClick={nextTurn}>Чек</Button>
            <Button onClick={nextTurn}>Ставка</Button>
            <Button onClick={nextTurn}>Сброс</Button>
          </div>
        ) : (
          <div className="text-gray-500">Ожидание других игроков...</div>
        )}
      </div>
    </div>
  );
};

export default PokerTable;
