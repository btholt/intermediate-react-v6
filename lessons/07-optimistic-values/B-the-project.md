Let's open the project.

1. Open optimisitic folder in your editor of choice
1. Run `npm install`
1. Glance at server.js - it responds to GET and POST on /thoughts
1. Run `npm run dev:server` to run the server
1. Run `npm run dev:client` in another terminal window to run the Vite server

You'll notice two things here in the server.js file, the DELAY and the ERROR_RATE. I intentionally slowed down the POST request so you can see how the loading state works. Feel free to change how long the delay is (it's in milliseconds). I also wanted you to see what errors look like, so it fails 20% of the time, this is also configurable.

Okay, let's build a UI that shows users deep thoughts, and allow them to post their own. In App.jsx, put

```javascript
import { useEffect, useState } from "react";

export default function App() {
  const [thoughts, setThoughts] = useState([]);
  const [thought, setThought] = useState("");

  async function postDeepThought() {
    setThought("");
    const response = await fetch("/thoughts", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ thought }),
    });
    if (!response.ok) {
      alert("This thought was not deep enough. Please try again.");
      return;
    }
    const { thoughts: newThoughts } = await response.json();
    setThoughts(newThoughts);
  }

  useEffect(() => {
    fetch("/thoughts")
      .then((res) => res.json())
      .then((data) => {
        setThoughts(data);
      });
  }, []);

  return (
    <div className="app">
      <h1>Deep Thoughts</h1>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          postDeepThought();
        }}
      >
        <label htmlFor="thought">What's on your mind?</label>
        <textarea
          id="thought"
          name="thought"
          rows="5"
          cols="33"
          value={thought}
          onChange={(e) => setThought(e.target.value)}
        />
        <button type="submit">Submit</button>
      </form>
      <ul>
        {thoughts.map((thought, index) => (
          <li key={thought}>{thought}</li>
        ))}
      </ul>
    </div>
  );
}
```

- Nothing surprising here. We get thoughts on load, and we reload them on POST
- The big issue here is that the user is shown no feedback while the POST happening.
- We could just append to thoughts and hope for the best - and this is what we used to do.
- However we can use `useOptimisticState` and get a lot of the error cases and other problems wrapped up and taken care of for us.
