---
title: "RSC Dependencies"
---

There's a lot to getting this set up by hand, but bear with me, and I promsie at the end of this you're going to understand RSCs at a depth that allows you to make trade-offs of when to use them and when to avoid them.

So, first, we're going to use Webpack and Babel directly (despite normally I'd suggest Vite.) Why? Because this allows us to use the React team's code directly without a layer indirection between Vite and Webpack. In general I suggest Vite for React devs.

So let's get our project started. In a new directory run

```bash
npm init -y
npm install @babel/core@7.26.8 @babel/plugin-transform-modules-commonjs@7.26.3 @babel/preset-react@7.26.3 @babel/register@7.25.9 @fastify/static@8.1.0 babel-loader@9.2.1 css-loader@7.1.2 doodle.css@0.0.2 fastify@5.2.1 html-webpack-plugin@5.6.3 nodemon@3.1.9 pino-pretty@13.0.0 promised-sqlite3@2.1.0 react@19.0.0 react-dom@19.0.0 react-server-dom-webpack@19.0.0 sqlite3@5.1.7 style-loader@4.0.0 webpack@5.97.1 webpack-cli@6.0.1
```

You can either run that, or just copy this package.json into your project and run `npm i`.

```json
{
  "name": "no-framework",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {},
  "keywords": [],
  "author": "Brian Holt",
  "license": "Apache-2.0",
  "description": "React Server Components without a framework!",
  "dependencies": {
    "@babel/core": "^7.26.8",
    "@babel/plugin-transform-modules-commonjs": "^7.26.3",
    "@babel/preset-react": "^7.26.3",
    "@babel/register": "^7.25.9",
    "@fastify/static": "^8.1.0",
    "babel-loader": "^9.2.1",
    "css-loader": "^7.1.2",
    "doodle.css": "^0.0.2",
    "fastify": "^5.2.1",
    "html-webpack-plugin": "^5.6.3",
    "nodemon": "^3.1.9",
    "pino-pretty": "^13.0.0",
    "promised-sqlite3": "^2.1.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "react-server-dom-webpack": "^19.0.0",
    "sqlite3": "^5.1.7",
    "style-loader": "^4.0.0",
    "webpack": "^5.97.1",
    "webpack-cli": "^6.0.1"
  }
}
```

Either works! We need a lot of machinery to get this to work but the high level is we're going to be building a Fastify server that is going to be serving RSCs via the React Flight format.

Let's set up Webpack. Create a webpack.config.js

```javascript
const path = require("node:path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const ReactServerWebpackPlugin = require("react-server-dom-webpack/plugin");

const mode = process.env.NODE_ENV || "development";
const development = mode === "development";

const config = {
  mode,
  entry: "./src/Client.jsx",
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: "babel-loader",
        exclude: /node_modules/,
      },
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
  resolve: {
    extensions: [".js", ".jsx"],
  },
  plugins: [
    new HtmlWebpackPlugin({
      inject: true,
      publicPath: "/assets/",
      template: "./index.html",
    }),
    new ReactServerWebpackPlugin({ isServer: false }),
  ],
  output: {
    chunkFilename: development
      ? "[id].chunk.js"
      : "[id].[contenthash].chunk.js",
    path: path.resolve(__dirname, "dist"),
    filename: "[name].js",
    clean: true,
  },
  optimization: {
    runtimeChunk: "single",
  },
};

module.exports = config;
```

This isn't a Webpack class so let's not dive too deep here - we're just making a Webpack config that's going to compile our React from JSX to usable JS code, use style-loader to load CSS, use the HTML plugin to generate a valid HTML for us, and make sure it's all living in one bundle so our React Flight protocol can find client side components in the bundle.

Let's set up the Babel config. Make a babel.config.js file.

```javascript
const development = (process.env.NODE_ENV || "development") === "development";

module.exports = {
  presets: [
    [
      "@babel/preset-react",
      {
        runtime: "automatic",
        useSpread: true,
        development: true,
      },
    ],
  ],
};
```

Now everything will work with Babel.

Let's make an index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>React Server Components without a Framework!</title>
    <link rel="stylesheet" href="/index.css" />
  </head>
  <body class="doodle">
    <div id="root"><!--ROOT--></div>
  </body>
</html>
```

Looks quite similar to our previous ones. Let's make a /public/index.css. [Copy it from here][css]

Copy [this SQLite file][sqlite] to your root directory as well.

> Both of these files should be in the root directory of the project if you cloned it. notes.db and index.css. Put them into your project

Lastly add these scripts to your package.json

```json
"scripts": {
  "dev:client": "webpack --watch",
  "dev:server": "node --watch --conditions react-server server/main.js"
},
```

Okay, this should give us everything that's needed for our app to function before we actually write the server and the React app. One bit to highlight is `const ReactServerWebpackPlugin = require("react-server-dom-webpack/plugin");` in our Webpack file - this is the magic plugin that will allow Webpack to _not_ render server components and only include client components. Other than that, this is a very standard React + Webpack set up.

Another new thing you might be the `--conditions react-server` part of running our server app. This lets Node.js know how to resolve its modules - we're in a react-server condition so only import those and know not to import client modules. I had never used this feature of Node.js before but it's pretty cool, even if it's a bit niche.

[css]: https://raw.githubusercontent.com/btholt/irv6-project/refs/heads/main/completed/no-framework/public/index.css
[sqlite]: https://github.com/btholt/irv6-project/blob/main/notes.db
