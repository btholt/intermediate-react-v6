One question you should probably have by this point is "how do I mix server and client component?" Super valid - we're obviously going to need both in order to ship complete apps. So how do we do that? Just by being a little judicious of how nest things and using React's innate ability to nest components.

Let's say we have a secret teacher view that allows the teacher to see all the notes passed between everyone in their class. And we want that data to be consistently updated so a teacher can always see the latest notes. How would we do that? Let's do it with polling - we'll query the database for rows in the database and then we'll continually update it.

Now, we could just make it totally a client component but we're going to make it load with complete data for the first time and then we'll start polling.

So how do we do that? We'll have a server component loading the first page load's data, and then we'll have a child client component doing the polling and re-rendering. Best of both worlds!

Make a folder called `teacher` in the app directory and put a page.js file in there with this in it:

```javascript
import TeacherClientPage from "./clientPage";
import fetchNotes from "./fetchNotes";

export default async function TeacherView() {
  const initialNotes = await fetchNotes();
  return (
    <TeacherClientPage initialNotes={initialNotes} fetchNotes={fetchNotes} />
  );
}
```

This a server component that will load initial notes from a fetchNotes function and then feed it in as a prop. Let's go write that fetchNote.js file.

```javascript
"use server";
import { AsyncDatabase } from "promised-sqlite3";

export default async function fetchNotes(since) {
  const db = await AsyncDatabase.open("./notes.db");
  let rows;
  if (since) {
    rows = await db.all(
      "SELECT n.id as id, n.note as note, f.name as from_user, t.name as to_user FROM notes n JOIN users f ON f.id = n.from_user JOIN users t ON t.id = n.to_user WHERE n.id > ? LIMIT 50",
      [since]
    );
  } else {
    rows = await db.all(
      "SELECT n.id as id, n.note as note, f.name as from_user, t.name as to_user FROM notes n JOIN users f ON f.id = n.from_user JOIN users t ON t.id = n.to_user LIMIT 50"
    );
  }
  return rows;
}
```

Another server function to fetch our latest update of rows. It'll only grab whatever is newer than what the client had. What's nice is we can share this function between the inital payload and the update function in the client. Let's go make clientPage.js then.

```javascript
"use client";
import { useState, useEffect } from "react";

export default function TeacherClientPage({ fetchNotes, initialNotes }) {
  const [notes, setNotes] = useState(initialNotes ? initialNotes : []);

  useEffect(() => {
    const interval = setInterval(async () => {
      let since;
      if (notes.length > 0) {
        since = notes[notes.length - 1]?.id ?? null;
      }
      const newNotes = await fetchNotes(since);
      setNotes([...notes, ...newNotes]);
    }, 5000);
    return () => clearInterval(interval);
  }, []);

  return (
    <div>
      <h1>Teacher's View</h1>
      <ul>
        {notes.map((note) => (
          <li key={note.id}>
            <fieldset>
              <h2>
                from: {note.from_user} | to: {note.to_user}
              </h2>
              <p>{note.note}</p>
            </fieldset>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

Now we have a client component that is using a server function to poll for changes, and its initial payload is seeded by a server component. Pretty cool, right? Best of both worlds.
