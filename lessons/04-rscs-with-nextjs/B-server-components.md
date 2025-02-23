So let's now do a server component where a user can read their own individual notes. This will look a lot like what we did with the no framework version!

Make a folder inside the app directory called `my`. Inside that directory, put page.js. This will make the route /my where this page will show up.

```javascript
import { AsyncDatabase } from "promised-sqlite3";

// this page assumes that you are logged in as user 1
export default async function MyNotes() {
  async function fetchNotes() {
    const db = await AsyncDatabase.open("./notes.db");
    const fromPromise = db.all(
      "SELECT n.id as id, n.note as note, f.name as from_user, t.name as to_user FROM notes n JOIN users f ON f.id = n.from_user JOIN users t ON t.id = n.to_user WHERE from_user = ?",
      ["1"]
    );
    const toPromise = db.all(
      "SELECT n.id as id, n.note as note, f.name as from_user, t.name as to_user FROM notes n JOIN users f ON f.id = n.from_user JOIN users t ON t.id = n.to_user WHERE to_user = ?",
      ["1"]
    );
    const [from, to] = await Promise.all([fromPromise, toPromise]);
    return {
      from,
      to,
    };
  }

  const notes = await fetchNotes();

  return (
    <div>
      <h1>My Notes</h1>
      <fieldset>
        <legend>Notes To You</legend>
        <table>
          <thead>
            <tr>
              <th>From</th>
              <th>To</th>
              <th>Note</th>
            </tr>
          </thead>
          <tbody>
            {notes.to.map(({ id, note, from_user, to_user }) => (
              <tr key={id}>
                <td>{from_user}</td>
                <td>{to_user}</td>
                <td>{note}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </fieldset>
      <fieldset>
        <legend>Notes From You</legend>
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
      </fieldset>
    </div>
  );
}
```

- We've built this to essentially the user is always logged in as user 1, brian. Feel free afterwards to add your own auth and make it a full-fledged app. [Neon Auth][neon] (which I helped build!) and [Clerk][clerk] are two great options here.
- It's an async function
- We're able to use the SQLite driver which is server-only normally
- Again, we don't have to say `"use server";` because it's assumed that any componet without "use client" is a server component
- Make sure to check out the network traffic! It's cool to see all the React Flight protocol stuff in action!

[neon]: https://neon.tech/blog/neon-auth-is-here-get-authentication-in-a-couple-of-clicks
[clerk]: https://clerk.com
