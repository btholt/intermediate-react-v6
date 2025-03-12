---
description: >-
  Explore how to use Fastify to serve a React app with server components by
  setting up a Node.js server that handles both client-side and server-side
  rendering. Written by Brian Holt for Frontend Masters, this guide offers
  step-by-step integration of React and Fastify, demonstrating the use of React
  Flight Protocol and reinforcing React's modular framework for advanced web
  development.
keywords:
  - React
  - Fastify
  - Node.js
  - Brian Holt
  - server components
  - web development
  - React Flight Protocol
---
So now we have a React app ready to be served to clients. Let's write a quick Fastify server to do this. I just chose Fastify because I like it; there's no reason you couldn't do Express, Restify, or any other API framework for Node.js.

Let's create a `server` directory in the root directory of our project. Put in there a main.js file and put in there

```javascript
const reactServerregister = require("react-server-dom-webpack/node-register");
reactServerregister();

const babelRegister = require("@babel/register");
babelRegister({
  ignore: [/[\\\/](dist|server|node_modules)[\\\/]/],
  plugins: ["@babel/transform-modules-commonjs"],
});

require("./server")();
```

- We're doing CommonJS for our server portion. I had it working with ES modules but it took so much extra code that this was simpler to teach you. Feel free to convert it later yourself. Everything else in this course will be ES modules.
- This file does three things: it makes our Node.js server able to read React/JSX files, it makes it able to ES modules in our client code, and it makes it able to do RSCs via the webpack bundler by hooking into the module systems to render some portions and not others. Remember the `--conditions react-server` that we have in our CLI command? This is what that is for.
- Why is this in a separate file? It's so all subsequent `require`/`import` calls can be run through Babel and the react-server-dom-webpack modules.

Okay, let's start crafting our server.js file in the same directory. Let's just start with the basic machinery.

```javascript
const path = require("node:path");
const { readFileSync } = require("node:fs");
const Fastify = require("fastify");
const fastifyStaticPlugin = require("@fastify/static");
const React = require("react");
const { renderToPipeableStream } = require("react-server-dom-webpack/server");
const AppImport = require("../src/App.jsx");

const App = AppImport.default;

const MANIFEST = readFileSync(
  path.resolve(__dirname, "../dist/react-client-manifest.json"),
  "utf8"
);
const MODULE_MAP = JSON.parse(MANIFEST);
const PORT = process.env.PORT ? process.env.PORT : 3000;

const fastify = Fastify({
  logger: {
    transport: {
      target: "pino-pretty",
    },
  },
});

fastify.register(fastifyStaticPlugin, {
  root: path.join(__dirname, "../dist"),
  prefix: "/assets/",
});

fastify.register(fastifyStaticPlugin, {
  root: path.join(__dirname, "../public"),
  decorateReply: false,
});

fastify.get("/", async function rootHandler(request, reply) {
  return reply.sendFile("index.html");
});

fastify.get("/react-flight", function reactFlightHandler(request, reply) {
  // we'll do this code in a sec
});

module.exports = async function start() {
  try {
    await fastify.listen({ port: PORT });
  } catch (err) {
    fastify.log.error(err);
    process.exit(1);
  }
};
```

- Pretty standard Fastify app. We're serving two static folders, `public` which is our CSS but also could be images and some other stuff. It's stuff that doesn't need to be compiled and just served directly. `dist` is our compiled Webpack stuff.
- We're importing `react-server-dom-webpack/server` to be able to correctly serve our app here momentarily.
- Same with our App.jsx. Notice we're not importing Client.jsx - that's all code that'll only execute client side.
- The module map is something that `react-server-dom-webpack` generates and let's React how to know what module to what.

Alright, let's render our app next. But let's cheat. Let's just give it directly some "React Flight" to render instead of the actual app.

```javascript
// replace the react-flight route
fastify.get("/react-flight", function reactFlightHandler(request, reply) {
  try {
    reply.header("Content-Type", "application/octet-stream");
    // be careful about whitespace as React Flight is sensitive to it. Make your editor isn't inserting any
    return reply.send(`1:{"name":"App","env":"Server","key":null,"owner":null,"props":{}}
0:D"$1"
0:["$","div",null,{"children":["$","h1",null,{"children":"Notes App"},"$1"]},"$1"]
`);
  } catch (err) {
    request.log.error("react-flight err", err);
    throw err;
  }
});
```

> 💡 This is prone to breaking as this protocol is unstable and they may choose to change it in the future. However the principles remain the same. If you see something about webpack complaining in the errors, just skip this code sample and go to the next code block.

- So what is that weird string in there? It's called React Flight Protocol and it's how React's server and client protocol work with each other.
- You'll never write this by hand. But I wanted to show you it's not very scary, it's just a DSL for communicating between a client and a server, and in theory you could hand author it - you just never would.

Let's take it one half step further.

```javascript
// replace reply.send

// be careful about whitespace as React Flight is sensitive to it. Make your editor isn't inserting any
return reply.send(`3:I["./src/ClientComponent.jsx",["vendors-node_modules_react_jsx-dev-runtime_js","vendors-node_modules_react_jsx-dev-runtime_js.chunk.js","client0","client0.chunk.js"],""]
1:{"name":"App","env":"Server","key":null,"owner":null,"props":{}}
0:D"$1"
0:["$","div",null,{"children":[["$","h1",null,{"children":"Notes App"},"$1"],["$","$L3",null,{},"$1"]]},"$1"]
`);
```

- This may not work for you due to paths (and again, you'd never hand author this so don't work if it doesn't.)
- This is cool though because you can see how React is referencing a client component. It's basically saying here's a div, and in it has an h1 and a "$L3". It then defines a "3" on the first line where it has `3:I`. Here it's saying anywhere that you have a 3 component, it refers to a ClientComponent, and that's going to be in this specific bundle, go find that and use it.
- Pretty cool, right?
- I genuinely don't know what some of the pieces here do, but it's fun to hack around and see what you can bend React to do.

Okay, let's actually put the real code in here.

```javascript
fastify.get("/react-flight", function reactFlightHandler(request, reply) {
  try {
    reply.header("Content-Type", "application/octet-stream");
    const { pipe } = renderToPipeableStream(
      React.createElement(App),
      MODULE_MAP
    );
    pipe(reply.raw);
  } catch (err) {
    request.log.error("react-flight err", err);
    throw err;
  }
});
```

- This will actually render your app based on what your App.jsx has (whereas we were "rendering" it ourselves before.)
- The way we coded this it will only really work for the base route of your app. If you tried to introduce a router or more components here, we haven't provided for the ability to route to different routes. You'd have to code that up yourself.
- Likewise we're not handing any props to App.
- This is _not_ server-side rendering. The way you'd do that is you'd render the React Flight protocol, dump it into a script tag in the HTML, and have your app read that and use it to render your app on load. A little awkward but again, generally your framework will handle that for you.
- Check out the react-flight request in your network tab to see how it gets rendered out.
- I also like it shows you `Server` logs directly in the browser, makes it easy to see client and server console.logs in the same place.

So there you go! Now your app is rendering partially on the server, partially on the client, and you've handcoded it all! This is a pain to do but once you involve a framework like Next.js this gets so much easier. Still, amazing job! Hopefully you peeked a little bit behind the curtain to see what React is doing for you.
