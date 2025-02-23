Let's put together the most simple React app so that we can render it using our newly-created framework.

Let's make a directory, `src` and put a file in there a file called App.jsx

```javascript
import { Suspense } from "react";
import ServerComponent from "./ServerComponent";
import ClientComponent from "./ClientComponent";

export default function App() {
  console.log("rendering App server component");
  return (
    <Suspense fallback={<h1>Loading...</h1>}>
      <h1>Notes App</h1>
      <ServerComponent />
      <ClientComponent />
    </Suspense>
  );
}
```

We're going to putting a lot of console logs in here just so you can see where things are happening and in what order. Great, so we'll have two components: a client and a server component to show you the difference. The client component won't render at all on the server and will be included in the bundle. Likewise the server component will only be rendered in the server and won't be included in the bundle.

So where does the App component render? On the server. By default _everything_ renders on the server. By default you can't use any hooks that have interactivity like useState; you have to declare it a client component to do that.

Okay, so let's make our client component. Make ClientComponent.jsx and put in there

```javascript
"use client";

import { useState } from "react";

export default function ClientComponent() {
  console.log("rendering ClientComponent client component");
  const [counter, setCounter] = useState(0);

  return (
    <fieldset>
      <legend>Client Component</legend>
      <p>Counter: {counter}</p>
      <button onClick={() => setCounter(counter + 1)}>Increment</button>
    </fieldset>
  );
}
```

Nothing you haven't seen before except the weird `"use client";` at the top. This is how you declare a component is to be rendered on the client and not on the server. After that it's just React as you know and love it. One note: `"use server;"` is not necessary, it's assumed.

Okay, let's make `ServerComponent.jsx`

```javascript
import { AsyncDatabase } from "promised-sqlite3";
import path from "node:path";

// this page assumes that you are logged in as user 1
export default async function MyNotes() {
  console.log("rendering MyNotes server component");
  async function fetchNotes() {
    console.log("running server function fetchNotes");
    const dbPath = path.resolve(__dirname, "../../notes.db");
    const db = await AsyncDatabase.open(dbPath);
    const from = await db.all(
      "SELECT n.id as id, n.note as note, f.name as from_user, t.name as to_user FROM notes n JOIN users f ON f.id = n.from_user JOIN users t ON t.id = n.to_user WHERE from_user = ?",
      ["1"]
    );
    return {
      from,
    };
  }

  const notes = await fetchNotes();

  return (
    <fieldset>
      <legend>Server Component</legend>
      <div>
        <table>
          <thead>
            <tr>
              <th>From</th>
              <th>To</th>
              <th>Note</th>
            </tr>
          </thead>
          <tbody>
            {notes.from.map(({ id, note, from_user, to_user }) => (
              <tr key={id}>
                <td>{from_user}</td>
                <td>{to_user}</td>
                <td>{note}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </fieldset>
  );
}
```

- We're doing SQL in React!? Well, yes, but also no. This all happening server-side before it's beening sent down to the browser. So it's basically the same as doing an API request but it's doing the full React lifecycle here on the server. That's one of the nicest aspects of RSC - you get to write all of this as if it was being done server side but instead of having a server portion and a client portion, it's all the same file!
- Notice that it's an `async` function - this is a fun ability that only server components have. Since it's all rendering once and on the server, you can do async functions for react server components.

This is deliberately a pared-down feature set as we're going to only do the bare minimum to implement a by-hand RSC-server implementation. Once we get into Next.js I'll show you more advance React server component features.

Okay, one more file, call it Client.jsx and put this in there.

```javascript
import { createRoot } from "react-dom/client";
import { createFromFetch } from "react-server-dom-webpack/client";
import "doodle.css/doodle.css";

console.log("fetching flight response");
const fetchPromise = fetch("/react-flight");
const root = createRoot(document.getElementById("root"));
const p = createFromFetch(fetchPromise);
console.log("rendering root", p);
root.render(p);
```

`createFromFetch` allows us to turn a fetch request to an API endpoint into a React component directly. This is the magic of React server components and probably one you'll never write by hand again - your framework will always do this for you. But I wanted to demystify what it's doing for you. You make a request to an API endpoint, get a promise, and hand it to React to render. That's it!
