import { useState, useEffect } from "react";

export default function HeritageDemo() { const [screen, setScreen] = useState("home"); const [name, setName] = useState(""); const [house, setHouse] = useState("Fer");

const [xp, setXp] = useState(0); const [level, setLevel] = useState(1); const [actionsCount, setActionsCount] = useState(0); const [streak, setStreak] = useState(0); const [lastActionDate, setLastActionDate] = useState(null);

const [virtues, setVirtues] = useState({ vaillance: 0, sagesse: 0, determination: 0, creation: 0, harmonie: 0, });

const xpToNextLevel = level * 100; const progressPercent = Math.min((xp / xpToNextLevel) * 100, 100);

useEffect(() => { const saved = JSON.parse(localStorage.getItem("heritage_demo")); if (saved) { setName(saved.name); setHouse(saved.house); setXp(saved.xp); setLevel(saved.level); setActionsCount(saved.actionsCount); setVirtues(saved.virtues); setStreak(saved.streak || 0); setLastActionDate(saved.lastActionDate || null); setScreen("game"); } }, []);

useEffect(() => { const data = { name, house, xp, level, actionsCount, virtues, streak, lastActionDate }; localStorage.setItem("heritage_demo", JSON.stringify(data)); }, [name, house, xp, level, actionsCount, virtues, streak, lastActionDate]);

function addXP(amount) { let newXP = xp + amount; let newLevel = level;

while (newXP >= newLevel * 100) {
  newXP -= newLevel * 100;
  newLevel += 1;
}

setXp(newXP);
setLevel(newLevel);

}

function validateAction(type) { addXP(1);

const today = new Date().toDateString();

if (lastActionDate !== today) {
  if (lastActionDate) {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1);
    if (lastActionDate === yesterday.toDateString()) {
      setStreak(prev => prev + 1);
    } else {
      setStreak(1);
    }
  } else {
    setStreak(1);
  }
  setLastActionDate(today);
}

setActionsCount(prev => {
  const newCount = prev + 1;
  if (newCount % 10 === 0) addXP(5);
  return newCount;
});

setVirtues(prev => ({
  ...prev,
  [type]: prev[type] + 1,
}));

}

function fightMonster(rank) { const xpGain = rank === "bas" ? 5 : 10; addXP(xpGain); }

if (screen === "home") { return ( <div className="min-h-screen flex flex-col items-center justify-center bg-gray-900 text-white p-6 space-y-6"> <h1 className="text-4xl font-bold">Heritage</h1> <button className="px-6 py-3 bg-white text-black rounded-lg" onClick={() => setScreen("create")}>Commencer</button> </div> ); }

if (screen === "create") { return ( <div className="min-h-screen bg-gray-900 text-white p-6 space-y-4"> <h2 className="text-2xl font-bold">Créer ton Avatar</h2> <input className="w-full p-2 rounded text-black" placeholder="Nom" value={name} onChange={(e) => setName(e.target.value)} /> <select className="w-full p-2 rounded text-black" value={house} onChange={(e) => setHouse(e.target.value)}> <option value="Fer">Maison du Fer</option> <option value="Couronne">Maison de la Couronne</option> <option value="Voile">Maison du Voile</option> </select> <button className="px-6 py-3 bg-white text-black rounded-lg" onClick={() => setScreen("game")} disabled={!name}>Valider</button> </div> ); }

return ( <div className="min-h-screen bg-gray-900 text-white p-6 space-y-6"> <div className="flex gap-4"> <button onClick={() => setScreen("game")} className="underline">Jeu</button> <button onClick={() => setScreen("profile")} className="underline">Profil</button> </div>

{screen === "profile" && (
    <div className="bg-gray-800 p-4 rounded-lg space-y-2">
      <h2 className="text-xl font-bold">Profil du Brave</h2>
      <p>Nom : {name}</p>
      <p>Maison : {house}</p>
      <p>Streak : {streak} jours</p>
      <p>Actions totales : {actionsCount}</p>
    </div>
  )}

  {screen === "game" && (
    <>
      <div className="bg-gray-800 p-4 rounded-lg space-y-2">
        <p>Niveau : {level}</p>
        <div className="w-full bg-gray-700 h-4 rounded">
          <div className="bg-white h-4 rounded" style={{ width: `${progressPercent}%` }}></div>
        </div>
        <p>XP : {xp} / {xpToNextLevel}</p>
        <p>Streak : {streak} jours</p>
      </div>

      <div className="bg-gray-800 p-4 rounded-lg space-y-2">
        <h3 className="font-bold">Actions</h3>
        <button className="block w-full bg-white text-black py-2 rounded" onClick={() => validateAction("vaillance")}>Entraînement</button>
        <button className="block w-full bg-white text-black py-2 rounded" onClick={() => validateAction("sagesse")}>Lecture</button>
        <button className="block w-full bg-white text-black py-2 rounded" onClick={() => validateAction("determination")}>Méditation</button>
        <button className="block w-full bg-white text-black py-2 rounded" onClick={() => validateAction("creation")}>Écriture</button>
        <button className="block w-full bg-white text-black py-2 rounded" onClick={() => validateAction("harmonie")}>Planification</button>
      </div>

      <div className="bg-gray-800 p-4 rounded-lg space-y-2">
        <h3 className="font-bold">Donjons</h3>
        <button className="block w-full bg-gray-600 py-2 rounded" onClick={() => fightMonster("bas")}>Monstre Bas Rang</button>
        <button className="block w-full bg-gray-600 py-2 rounded" onClick={() => fightMonster("moyen")}>Monstre Moyen Rang</button>
      </div>

      <div className="bg-gray-800 p-4 rounded-lg">
        <h3 className="font-bold">Vertus</h3>
        {Object.entries(virtues).map(([key, value]) => (
          <div key={key} className="mb-2">
            <p className="capitalize">{key} : {value}</p>
            <div className="w-full bg-gray-700 h-3 rounded">
              <div className="bg-white h-3 rounded" style={{ width: `${Math.min(value * 5, 100)}%` }}></div>
            </div>
          </div>
        ))}
      </div>
    </>
  )}
</div>

); }
