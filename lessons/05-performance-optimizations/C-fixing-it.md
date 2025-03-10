When does a componet re-render? It re-renders when its parents have changed, always. But what if we could say "only re-render when your props have changed"? Frankly that's most components, but this is an opt-in pattern for React. Generally, like we said, renders are so cheap that introducing this "memoization" layer just makes it harder to debug. But in this case we need some help, so let's do it!

In MarkdownPreview.jsx

```javascript
// at top
import { memo } from "react";

// wrap function
export default memo(function MarkdownPreview({ render, options }) {
  // code
});
```

Now we've told React "only re-render this when the props haven't changed. But it's still re-rendering? Why?

Well, answer me two questions

```javascript
const objectA = {};
const objectB = {};

console.log(objectA === objectB); // is this true or false?

const functionA = function () {};
const functionB = function () {};

console.log(functionA === functionB); // is this true or false?
```

If you run this, what will be the two logs? Go ahead and try it. Copy and paste it into your console.

Did you get two `false`? It's because despite being equivalent in terms of what we've instantiated them with, they are two separate entities. They're two different pointers, if you're familiar with C++ or other languages.

So with our example, despite our functions and objects being constructed equivalently like in our little example here, they're considered **not equal** when React compares them.

Well, damn, okay, how do we deal with that? useCallback and useMemo. Let's go do it in our app.

```javascript
// at top
import { useState, useCallback, useMemo } from "react";

// replace our options and render
const render = useCallback((text) => marked.parse(text), []);
const options = useMemo(() => ({ text, theme }), [text, theme]);
```

- There you go! Now it should be rendering and only re-rendering when needed! 🎉
- Now it gives _the same_ options and _the same_ render each render so the `===` comparison will be true.
- The array following it works just like useEffect - it's essentially the cache key. If one of those things changes in the array, it invalidates the memo/callback and makes it render again.
