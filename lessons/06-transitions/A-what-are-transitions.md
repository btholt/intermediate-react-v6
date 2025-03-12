---
description: >-
  Learn how to use React's useTransition hook to optimize user experience when
  selecting options with poor network connectivity, ensuring the user interface
  remains interactive by building a sports score app with delayed API responses,
  as taught by Brian Holt in the 'Intermediate React v6' course for Frontend
  Masters.
keywords:
  - React
  - useTransition
  - Brian Holt
  - Frontend Masters
  - UI optimization
  - web development
  - sports app
---

You know how your remote has those buttons for streaming services on it? Have you ever accidentally pressed "Uncle Pete's Streaming Service" instead of "Netflix" and it takes forever to load and you're held hostage to the unoptimized loading of their crappy app? Really frustrating, right?

Now think of that happening on a website you've been on – you have several options to pick from and you accidentally choose the wrong one, and now you have to wait for the UI to be interactive again for you to be able to correct your mistake. Really annoying, especially on slower sites, slower connections and/or slower devices.

This is what transitions are for in React. They allow you to keep UIs interactive while loading is happening behind the scene.

We're going to build a little app that shows sports scores with an intentionally slow API. We're going to make it so users who accidentally select the wrong score to look at can still select a new game even while others are loading in the background.

Most React apps in the past would have just locked the user out of being able to make new choices while data is loading - it's the easy way to code this up. But with the `useTransition` hook, this became pretty easy to do. Let's do it.
