> Fun fact: this very website is rendered via SSG with Next.js! [See the code here][code]

Static Site generation allows you to use React to generate a fully static site. This is perfect for sites like blogs or course materials (like this site) where it's just a bunch of static pages that don't need the interactivity of React. It's enough to render this out once - it doesn't need the interactivity. The result is the end user is just shipped fully rendered flat HTML files - it doesn't need a server or client to do it. The developer can still add some interactivity via React, but minimally so.

This is just useful for static content. I love it because it helps me ship these courses faster, but it's not a good fit for anything with much more than basic interactivity.

## Let's Build It

This is going to be almost insultingly easy but I want you to see that at its core, they frameworks are mostly doing dev experience work and tools around the actual static site generation and that React itself is doing the heavy lifting.

Create a new folder. In that folder, run

```bash
npm init -y
npm i react react-dom
```

In your package.json, add `"type": "module"` as a top level item. Also add `"build": "node ./build.js"` to your scripts.

> 💡 We're going to do this with vanilla JS and not JSX so we don't need to bring in Babel/Vite/Webpack/whatever. You could absolutely bring in those if you wanted to, I'm just trying to keep these examples as simple as possible.

Create index.html, put this in there:

```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>SSG Example</title>
  </head>
  <body>
    <div id="root"><!--ROOT--></div>
  </body>
</html>
```

Create an App.js file, put in there

```javascript
import { createElement as h } from "react";

function App() {
  return h(
    "div",
    null,
    h("h1", null, "Hello Frontend Masters"),
    h("p", null, "This is SSG")
  );
}

export default App;
```

Now create a build.js and put

```javascript
import { renderToStaticMarkup } from "react-dom/server";
import { createElement as h } from "react";
import {
  readFileSync,
  writeFileSync,
  existsSync,
  mkdirSync,
  readdirSync,
  unlinkSync,
} from "node:fs";
import { fileURLToPath } from "node:url";
import path, { dirname } from "node:path";
import App from "./App.js";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const distPath = path.join(__dirname, "dist");

const shell = readFileSync(path.join(__dirname, "index.html"), "utf8");

const app = renderToStaticMarkup(h(App));
const html = shell.replace("<!--ROOT-->", app);

// Create dist folder if it doesn't exist
if (!existsSync(distPath)) {
  mkdirSync(distPath);
} else {
  // Delete all files in dist folder if it exists
  const files = readdirSync(distPath);
  for (const file of files) {
    unlinkSync(path.join(distPath, file));
  }
}

writeFileSync(path.join(distPath, "index.html"), html);
```

- Most of the code is making sure the dist folder exists, and if it does, is empty
- We just take our App, render it to string, and stick it in the HTML shell
- You actually _could_ just have React render everything, `<html>`, `<body>` and all.
- We didn't include any CSS nor JS, but obviously you could.
- For mild amounts of interactivity, you could include JS file with the React run time and hydrate the app, but we don't need to.
- We're using renderToStaticMarkup - this very similar to the renderToString function but doesn't include any hints for React to hydrate later for SSR (server-side rendering, our next lesson). renderToString would work, it'll just include superfluous React stuff we don't need.

This generates one page. You could have it generate many pages any number of ways. We could write a `routes.js` file that defines a route and its route component and then have build.js loop over that. We could use `react-router` or something like and then make our build.js use those routes to generate route. Any of these are viable.

## Okay but just use Astro, Next.js, Gatsby, etc.

I wanted you to see how simple this is, but in reality I can't fathom why you'd do this. You are just `npm create astro@latest` from a full-featured framework that is optimized around making static content sites and I can't see why you wouldn't do that. This site is built on Next.js because it's the one I knew the best but if I were to rebuild my course builder website I'd use Astro.

## SSG with Next.js

This site is entirely SSG'd. In general you will never directly write SSG code yourself; you will rely on a framework to do it for you. I would say the two primary ways to do this are either using [Next.js's export mode][export] (which is what this site does) or using [Astro][astro]. Both are great, choose your favorite.

Let's dive in a second on how this course gets built.

- [Here's the link to this page's markdown file][md]
- [Here's the link to this site's Next.js config][config]
- [Here's the link to this page's built HTML][html]
- [Here's the link to the entire site's built-out HTML][site-html]

Using this toolkit, I'm able to use React to write the wrappers of the pages of the course, write all the content in Markdown, and get all sorts of developer experience tools, SEO tools, and other tools that help make it easy for Frontend Masters to film these videos. I could write raw HTML or a Notion doc or something, but there's a ton of value being realized here by using Next.js

[code]: https://github.com/btholt/intermediate-react-v6/
[export]: https://nextjs.org/docs/pages/building-your-application/deploying/static-exports
[astro]: https://astro.build/
[md]: https://github.com/btholt/intermediate-react-v6/blob/main/lessons/02-react-render-modes/B-static-site-generation.md
[config]: https://github.com/btholt/intermediate-react-v6/blob/main/next.config.js#L9
[html]: https://github.com/btholt/intermediate-react-v6/blob/gh-pages/lessons/react-render-modes/static-site-generation.html
[site-html]: https://github.com/btholt/intermediate-react-v6/tree/gh-pages
