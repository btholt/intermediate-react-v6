---
description: >-
  Discover how to enhance your React app's performance with server-side
  rendering (SSR) as demonstrated by Brian Holt in the Intermediate React v6
  course for Frontend Masters. Learn how to implement SSR by hand to optimize
  time to interactive and first meaningful paint, along with considerations for
  complexity and device performance. Explore practical examples of SSR app setup
  with Fastify, Vite, and React, including handling browser-only scripts and
  overcoming hydration errors.
keywords:
  - React
  - server-side rendering
  - SSR
  - Brian Holt
  - Frontend Masters
---
> 💡 The previous version of this course does a pretty in-depth dive on migrating a client-side React app to being server-side render. [Check it out here][v5]. Nothing has changed so if you want more SSR magic ✨ this still 100% applies.

When you have a client-side React request, the general flow looks like this:

![User requests app, user waits for HTML and JS bundle to be requested, once downloaded they can finally see the app](/images/request1.png)

Generally speaking, this is acceptable for many apps. A lot of apps you interact with on a daily basis work just fine like this. However, you may run into situations where it may behoove you to alter the performance profile of your app a bit.

![User requests app, server pre-renders app, users sees complete HTML while the React app bootstraps itself](/images/request2.png)

Notice the time to interactive and time to first meaningful paint are different now. In the previous version they were the same moment, we have now separated them.

You may be looking at this thinking "this is better!" and in many cases it is: people see something quickly and before they can decide to take an action, generally the app will be bootstrapped. It will _feel_ faster to the user despite the time to interactive will nearly always be tens of milliseconds later (as your app has to render on the server and that takes time). However be careful with this assumption as it in some cases it _isn't_ faster.

- SSR carries with it complexity - some code can be executed in the browser and can't in Node.js (e.g. Google Analytics! it relies on browser APIs). You now need to cleanly separate what runs in browser and what doesn't.
- On fast devices with fast connections, it will tend to actually be a bit slower to get both first paint and first interactive. If you're writing an app for iPhone 16 users in San Francisco, you really don't need SSR. If you're writing it for rural farmers in Montana or the Indian country-side, maybe SSR could help!
- The key here is _measure_. SSR can be a great tool in your toolbox but make sure it's actually making a positive difference to your users.

## SSR by hand

Okay, so let's write a very simple SSR app.

In a new folder, run

```bash
npx init -y
npm i fastify @fastify/static react react-dom vite
```

Make sure your package.json has `"type": "module"` in it. Add this to the scripts:

```json
"scripts": {
  "build": "vite build",
  "start": "node ./server.js"
},
```

In the project root, create an index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>SSR Example</title>
    <script async defer type="module" src="./Client.js"></script>
  </head>
  <body>
    <div id="root"><!--ROOT--></div>
  </body>
</html>
```

> 💡 We're doing vanilla JS again, but it's easier now to add JSX if you want to. Vite has `--ssr` flag you can add to compile an app for use w/ React doing SSR. We're not covering it today but feel free to try later.

Create an App.js, put in there:

```javascript
import { createElement as h, useState } from "react";

function App() {
  const [count, setCount] = useState(0);
  return h(
    "div",
    null,
    h("h1", null, "Hello Frontend Masters"),
    h("p", null, "This is SSR"),
    h("button", { onClick: () => setCount(count + 1) }, `Count: ${count}`)
  );
}

export default App;
```

Now create a Client.js file

```javascript
import { hydrateRoot } from "react-dom/client";
import { createElement as h } from "react";
import App from "./App.js";

hydrateRoot(document.getElementById("root"), h(App));
```

This is code that will _only_ execute in the browser. If you have Google Analytics or local storage or anything like that, you'd do those sorts of things that _need_ to happen in the browser but don't need to be run in Node.js. Specifically, hydrateRoot will only run on the browser and can't run on the server.

Now for the spicy bit, let's do server-side rendering

```javascript
import fastify from "fastify";
import fastifyStatic from "@fastify/static";
import { readFileSync } from "node:fs";
import { fileURLToPath } from "node:url";
import path, { dirname } from "node:path";
import { renderToString } from "react-dom/server";
import { createElement as h } from "react";
import App from "./App.js";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const shell = readFileSync(path.join(__dirname, "dist", "index.html"), "utf8");

const app = fastify();

app.register(fastifyStatic, {
  root: path.join(__dirname, "dist"),
  prefix: "/",
});

const parts = shell.split("<!--ROOT-->");
app.get("/", (req, reply) => {
  reply.raw.write(parts[0]);
  const reactApp = renderToString(h(App));
  reply.raw.write(reactApp);
  reply.raw.write(parts[1]);
  reply.raw.end();
});

app.listen({
  port: 3000,
});
```

> If you're getting hydration errors, you may have a whitespace problem (I did when writing this.) React is _super_ sensitive to anything being different between client and server. I had to make sure that `<div><!--ROOT--></div>` had no newlines in it.

The interesting part is the "/" get handler. We immediately write the head to the user. This allows the browser to see the script tag and immediately start downloading the React app. We then render the app and send it to the user. This means by the time the app is rendered and sent to the user, it'll be pretty close to the time the user finishes downloading the script and should get a faster time to first meaningful pain and a decent time to interactive. We finish it off by sending the rest of the closing tags to the user.

> We did this with renderToString. You can do this with renderToPipeableStream as well and on a larger app see some improvement as it'll stream markup as it finishes to the user. I wanted to show you renderToString which works well for this micro app, but on a bigger codebase I'd choose streaming. We're about to do use renderToPipeableStream for React Server Components so you're about to learn how to use it anyway.

[v5]: https://frontendmasters.com/courses/intermediate-react-v5/server-side-rendering/
