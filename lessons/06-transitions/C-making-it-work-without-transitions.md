Let's first make this "wrong" first - the problem where the UI locks up during transitional times.

Let's make a Score.jsx file

```javascript
export default function Score({ isPending, home, away, game }) {
  return (
    <div className="score">
      <div>
        <h2>HOME {game}</h2>
        <h3>{isPending ? "–" : home}</h3>
      </div>
      <div>
        <h2>AWAY {game}</h2>
        <h3>{isPending ? "–" : away}</h3>
      </div>
    </div>
  );
}
```

- Key here is that if the score is pending, we show a dash. That will visually indicate to the user we haven't loaded it yet.

Okay, let's make a quick function fetch from the API. Make a file called getScore.js

```javascript
export default async function getScore(game) {
  const response = await fetch("/score?game=" + game);
  const score = await response.json();
  return score;
}
```

No surprises here.

Let's go modify our App.jsx. Put this in there

```javascript
import { useState, useEffect } from "react";
import Score from "./Score";
import getScore from "./getScore";

export default function App() {
  const [isPending, setIsPending] = useState(true);
  const [game, setGame] = useState(1);
  const [score, setScore] = useState({ home: "–", away: "–" });

  async function getNewScore(game) {
    setIsPending(true);
    setGame(game);
    const newScore = await getScore(game);
    setScore(newScore);
    setIsPending(false);
  }

  useEffect(() => {
    getNewScore(game);
  }, []);

  return (
    <div className="app">
      <h1>Game {game}</h1>
      <select
        disabled={isPending}
        onChange={(e) => {
          getNewScore(e.target.value);
        }}
      >
        <option value={1}>Game 1</option>
        <option value={2}>Game 2</option>
        <option value={3}>Game 3</option>
        <option value={4}>Game 4</option>
        <option value={5}>Game 5</option>
      </select>
      <div className={`loading-container ${isPending ? "loading" : ""}`}>
        {" "}
        <span className="spinner">⚽️</span>
      </div>
      <div>
        <Score
          isPending={isPending}
          game={game}
          home={score.home}
          away={score.away}
        />
      </div>
    </div>
  );
}
```

- So this works, and this is how most people would have coded this - just wait until the API request finishes.
- Why do we need to disable the select while stuff is loading? Because if we don't people can make multiple requests in that time, and they'll return in a jumbled order and it'll freak people out. We have to make sure it finsihes it first so we don't have a UI that's responding to old requests.
