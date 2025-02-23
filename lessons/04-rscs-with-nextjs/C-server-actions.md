One of the most common interactions that users have with websites is some variation on submitting a form - whether that's a very traditional "fill out this form and hit submit" or something similar where the developer uses a form under the hood to handle inputs and then uses a submit event to fire off an API call.

The React team leaned into this with a feature called server actions. You can essentially create an RSC that has an action on the form that will cause an action to fire on the server. It makes it really simple to hook up a frontend form and a backend function to process it. Let's first craft our form.

Make a folder inside app called `write`. In there put page.js, so that we can have the route `/write`.

```javascript
import { AsyncDatabase } from "promised-sqlite3";
import postNote from "./postNote";

export default async function Write() {
  async function getUsers() {
    const db = await AsyncDatabase.open("./notes.db");
    return db.all("SELECT * FROM users");
  }
  const users = await getUsers();

  return (
    <div>
      <fieldset className="note-fieldset">
        <legend>Write a new note</legend>
        <form action={postNote} className="note-form">
          <label>
            From
            <select name="from_user">
              {users.map((user) => (
                <option key={user.id} value={user.id}>
                  {user.name}
                </option>
              ))}
            </select>
          </label>
          <label>
            To
            <select defaultValue={2} name="to_user">
              {users.map((user) => (
                <option key={user.id} value={user.id}>
                  {user.name}
                </option>
              ))}
            </select>
          </label>
          <label>
            Note
            <textarea name="note" />
          </label>
          <button type="submit">Save</button>
        </form>
      </fieldset>
    </div>
  );
}
```

`<form action={postNote} className="note-form">` is the interesting part here. We're going to go code this up now but essentially it's allowing us to directly plug a backend function into the frontend form and React will take care of the transport of the data from frontend to backend. We get to skip writing all the `fetch` logic - React does it for us. We could have totally written this in the no-framework version of our app but it would have just been some extra steps in the server code. This is a React feature that Next.js implements.

Let's go write postNote.js in the same directory.

```javascript
"use server";
import { AsyncDatabase } from "promised-sqlite3";

export default async function postNote(formData) {
  console.log("postNote called", formData);

  const from = formData.get("from_user");
  const to = formData.get("to_user");
  const note = formData.get("note");

  if (!from || !to || !note) {
    throw new Error("All fields are required");
  }

  const db = await AsyncDatabase.open("./notes.db");
  await db.run(
    "INSERT INTO notes (from_user, to_user, note) VALUES (?, ?, ?)",
    [from, to, note]
  );
}
```

_Now_ we need the `"use server";` directive at the top as this must be run on the server and we need to disambiguate that for React. Try commenting it out - the app will crash.

Pretty cool, right? I love not having to write the whole API handshake code and just to have it written for me.

Be sure to watch the network tab as well - it's cool to see the React Flight protocol handle it!
