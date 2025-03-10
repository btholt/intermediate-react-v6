---
description: >-
  Explore performance optimization techniques in React with Brian Holt's course
  "Intermediate React v6" as you build a markdown previewer using artificial
  jank for testing. This segment covers creating MarkdownPreview.jsx and
  App.jsx, addressing rendering issues, and demonstrating the use of
  dangerouslySetInnerHTML to prevent XSS attacks.
keywords:
  - React
  - Brian Holt
  - web development
  - markdown previewer
  - performance optimization
---
> You will need to open the folder `perf` from the repo in your project.
>
> - [The starter template][starter]
> - [The completed project][completed]

Let's open the `perf` project in our repo and run `npm install`.

We're going to build a markdown previewer! On some laptops this can be pretty slow to parse and re-render to the DOM. On my laptop it's actually fast enough to get through it so we're going to introduce some artifical jank. Your computer may not need it.

I left a long markdown file for you to use as a sample in markdownContent.js. I asked Claude to make some jokes for us. I'd call it a middling success.

Make a new file called MarkdownPreview.jsx. Put this in there

```javascript
const JANK_DELAY = 100;

export default function MarkdownPreview({ render, options }) {
  const expensiveRender = () => {
    const start = performance.now();
    while (performance.now() - start < JANK_DELAY) {}
    return null;
  };
  return (
    <div>
      <h1>Last Render: {Date.now()}</h1>
      <div
        className="markdown-preview"
        dangerouslySetInnerHTML={{ __html: render(options.text) }}
        style={{ color: options.theme }}
      ></div>
      {expensiveRender()}
    </div>
  );
}
```

- This is the artificial jank. It ties up the main thread so it'll run slower. Feel free to modify `JANK_DELAY`. Right now I have it delaying 100ms so you can see the jank more pronounced.
- I'm also showing the time so can see how often the component runs.
- `dangerouslySetInnerHTML` is fun. It just means you need to _really_ trust what you're putting in there. If you just put raw user generated content in there, they could drop a script tag in there and do a good ol' [XSS attack][xss]. In this case the user can only XSS themself so that's fine enough.

Okay, let's make our App.jsx

```javascript
import { useEffect } from "react";
import { marked } from "marked";
import { useState } from "react";

import MarkdownPreview from "./MarkdownPreview";
import markdownContent from "./markdownContent";

export default function App() {
  const [text, setText] = useState(markdownContent);
  const [time, setTime] = useState(Date.now());
  const [theme, setTheme] = useState("green");

  useEffect(() => {
    const interval = setInterval(() => {
      setTime(Date.now());
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  const options = { text, theme };
  const render = (text) => marked.parse(text);

  return (
    <div className="app">
      <h1>Performance with React</h1>
      <h2>Current Time: {time}</h2>
      <label htmlFor={"theme"}>
        Choose a theme:
        <select value={theme} onChange={(e) => setTheme(e.target.value)}>
          <option value="green">Green</option>
          <option value="blue">Blue</option>
          <option value="red">Red</option>
          <option value="yellow">Yellow</option>
        </select>
      </label>
      <div className="markdown">
        <textarea
          className="markdown-editor"
          value={text}
          onChange={(e) => setText(e.target.value)}
        ></textarea>
        <MarkdownPreview options={options} render={render} />
      </div>
    </div>
  );
}
```

Alright, go play with it now (you may need to mess with the JANK_DELAY as well as the interval of how often the interval runs). The scroll is probably either janky or it has a hard time re-rendering. Typing in it should hard as well. Also notice that the current render. Re-rendering the theme is tough too.

So how can we fix at least the scroll portion, as well make the other two a little less painful (as they'll only re-renderingo once as opposed to continually.)

[xss]: https://owasp.org/www-community/attacks/xss/
[starter]: https://github.com/btholt/irv6-project/tree/main/starter/perf
[completed]: https://github.com/btholt/irv6-project/tree/main/completed/perf
