---
title: "Abbreviated Intro to Next.js"
---

This is not an intro to Next.js course, intentionally. Frontend Masters has an amazing teacher for that, Scott Moss. We are going to use Next.js here, but only as a tool to teach about RSCs. We'll leave the rest of "how to write great Next.js code" to Scott and his Intro to Next.js Course. You'll learn some Next.js incidentally, but we'll be glossing over it.

Next.js is a fantastic React.js complete framework built by the fine folks at Vercel. Much of the React core team works at Vercel and therefore a lot of the newest changes show up in Next.js first because it's the same people working on both.

Next.js has gone through several iterations but the latest version makes RSCs a first class citizen where everything is an RSC by default and you have to opt into client-side components. I'll say this has some trade-offs - RSCs are more complicated to deal with, but it's definitely a good opinion for lots of apps out there. It's worth having as a tool in your tool belt.

You can use Next.js in one of two ways (in my opinion): it can be your entirely monolithic server (great answer for some apps) or you can have it as a "middle-end" server where it just serves your front-end and you have another server that's just your backend (this could be a Fastify, FastAPI, Flask, Sinatra, etc.) server.

Let's get our app started

```javascript
npx create-next-app@15.1.7 --js --app --src-dir --turbopack
```

You can call the app whatever you want. I'd omit ESLint and Tailwind (no big deal if you want to include them, we're just not going to use them today.)

Okay, let's shape this app to be what we need. We're going to continue on our path to building Note Passer, so let's install a few more depenencies

```javascript
npm i doodle.css@0.0.2 promised-sqlite3@2.1.0 sqlite3@5.1.7
```

Now let's create our DB. Here's a few options for you

- Download [this notes.db file][db] and put it in the root directory of your project
- Download [this seed SQL file][seed] and use it to set up your SQLite DB

Either works. You can copy/paste that seed file into SQLite session or you can also run `.read seed.sql` (if seed is in the same directory as the notes.db file) to read the local file.

Next we'll modify src/page.js

```javascript
import Link from "next/link";

export default function Home() {
  return (
    <div>
      <ul>
        <li>
          <Link href="/my">My Notes</Link>
        </li>
        <li>
          <Link href="/write">Write a Note</Link>
        </li>
        <li>
          <Link href="/teacher">Secret Teacher Feed</Link>
        </li>
        <li>
          <Link href="/who-am-i">Who Am I</Link>
        </li>
      </ul>
    </div>
  );
}
```

And now layout.js

```javascript
import Link from "next/link";
import "doodle.css/doodle.css";
import "./globals.css";

export const metadata = {
  title: "Note Passer",
  description: "Example app for Frontend Masters",
};

export default async function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className="doodle">
        <nav>
          <h1>
            <Link href="/">Note Passer</Link>
          </h1>
        </nav>
        {children}
      </body>
    </html>
  );
}
```

Great! This should be all fairly non-interesting. The only thing here is that page.js will be `/` homepage for our app and layout.js will be the layout file that wraps all inner components. This is a Next.js thing with their App router. Again, no need to worry about any of that right now - you'll cover App router more in-depth in the Intro to Next.js class.

Lastly, update [the globals.css to be this][css]. Go ahead and delete page.module.css. CSS modules are awesome but not in scope for this class so I've just written all the CSS for you.

[seed]:
[db]:
[css]:
