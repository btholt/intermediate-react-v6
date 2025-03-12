---
description: >-
  Explore the differences between useMemo and useCallback Hooks in React, as
  explained by Brian Holt in the Intermediate React v6 course for Frontend
  Masters. Understand when to use these Hooks for performance optimization and
  learn about the potential pitfalls of overusing memoization, alongside
  insights into React Compiler. Enhance your React skills with practical advice
  on state management and performance enhancement techniques.
keywords:
  - useMemo
  - useCallback
  - React Hooks
  - performance optimization
  - memoization
  - React Compiler
  - Brian Holt
---

## useMemo vs useCallback

What's the difference between useCallback and useMemo? Not much. You could actually rewrite our code to look like this and it'd be equivalent.

```javascript
// render and render2 are functionally equivalent
const render = useMemo(() => (text) => marked.parse(text), []);
const render2 = useCallback((text) => marked.parse(text), []);
```

Notice there's an extra `() => [fn]`. useCallback is only a thin wrapper on useMemo that automatically wraps it in the additional function. It's otherwise 100% equivalent.

## What about strings and numbers?

If you're just passing in strings and numbers to a `memo`ized function, you don't need useMemo or useCallback since those will just pass the `===` test already. It's just objects and functions! So instead of having options as an object, we could have had theme and text as top level props and we could have skipped using useMemo!

That's it! While you should use these tools sparingly, they're handy when you do need them. They let you create a better performance profile when it's important.

## Don't just use memo all the time 🙏

As your potential future coworker, please please please don't just use memo on every function. I know it sounds like a good idea but let me tell you why. When you memoize something, it doesn't re-render when its props haven't changed. You're correct in thinking that is normally true of most components. However I **guarantee** you will start getting bugs of a component not re-rendering when you expect it to, and that is a hard bug to find if it's an errant memo somewhere in your codebase (spoken by someone who has had to debug this.) Just don't do it.

## React Compiler

We talk a bit about [React Compiler][compiler] in the intro course, but let's mention it again here. The function of React Compiler is to examine your code and determine what components could be memoized without causing unexpected behavior. So just what I told you not to do? React is actually going to do this for you free. It's still in a rough alpha but they're encouraging people to try it out.

[See my Intro lesson on it][intro].

[compiler]: https://react.dev/learn/react-compiler
[intro]: https://react-v9.holt.courses/lessons/whats-next/react-compiler
