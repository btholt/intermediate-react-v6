---
title: Limitations of RSCs
description: >-
  In the Intermediate React v6 course by Brian Holt for Frontend Masters, learn
  how to work with React Server Components (RSC) by implementing a client
  component that renders a server component, with special emphasis on overcoming
  data transmission barriers between them using pre-rendering and client-side
  APIs. Discover key insights on optimizing React applications with this
  advanced web development tutorial.
keywords:
  - React
  - Server Components
  - Client Components
  - Web Development
  - Brian Holt
---

Okay, so we saw using a server component as a parent and passing data to a client component. What about going the other way? A client component rendering a server component? Well, it works, sort of, with a some caveats. A good thing to keep in mind is that, by necessity, server components render first and client components second. So we can't cross that barrier back the other way - going from the second/client components to the first/server components. But there's a little trick that we can do to somewhat get around this.

Make a folder in app, call it `who-am-i`. Put a page.js file in there.

```javascript
import ClientPage from "./clientPage";
import WhoAmI from "./whoAmI";

export default async function WhoAmIPage() {
  return (
    <ClientPage id={1}>
      <WhoAmI />
    </ClientPage>
  );
}
```

This is the secret trick: we can just pre-render the server component first, and then pass the result to a client component to render it. Pretty cool, right? We'll talk more about it in a sec, but let's write whoAmI.js first.

```javascript
import { AsyncDatabase } from "promised-sqlite3";

// this page assumes that you are logged in as user 1
async function getWhoAmI() {
  const db = await AsyncDatabase.open("./notes.db");
  return db.get("SELECT * FROM users WHERE id = ?", ["1"]);
}

export default async function WhoAmI() {
  const user = await getWhoAmI();

  return (
    <div>
      <h1>Who Am I?</h1>
      <p>
        You are {user.name} and your id is {user.id}
      </p>
    </div>
  );
}
```

And now clientPage.js

```javascript
"use client";
import updateUsername from "./updateUsername";

export default function ClientWhoAmIPage({ children, id }) {
  return (
    <div>
      {children}
      <form action={updateUsername}>
        <h2>Enter new username</h2>
        <input type="text" name="username" placeholder="username" />
        <input type="hidden" name="id" value={id} />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

Now we have a client page rendering a server page! Let's go write that form action, updateUsername

```javascript
"use server";
import { AsyncDatabase } from "promised-sqlite3";
import { redirect } from "next/navigation";

export default async function updateUsername(formData) {
  console.log("updateUsername called", formData);

  const username = formData.get("username");
  const id = formData.get("id");

  if (!username || !id) {
    throw new Error("All fields are required");
  }

  const db = await AsyncDatabase.open("./notes.db");
  await db.run("UPDATE users SET name = ? WHERE id = ?", [username, id]);
  redirect("/");
}
```

Done! Everything should work now.

So let's talk a bit more in-depth about the caveats here

- You can't have client data from a client component fed into a server component. And it's going to annoy you. It's probably the most annoying thing to me about writing lots of RSCs.
- Rather, what you need to do is make more client components, and go back to the old way of using an API to make requests for data. So in that regard it's no worse than writing normal, old React.
- You can do this little hack I showed you, as long as you're not making the server component dependent on anything from client-side state.
- If you do want to have something like that, you'll need to gather the data from the client and trigger a full refresh of the page with the new client side data to use it (which sorta makes sense since they're server components.)

This is it! You now understand all the various ways of looking at client and server components!
