---
description: >-
  Learn how to manage computationally expensive components in React to prevent
  UI lag and improve performance in this Intermediate React v6 course by Brian
  Holt for Frontend Masters. Discover techniques to handle jank during frequent
  re-renders and optimize browser interactions in a client-side image editor
  project.
keywords:
  - React
  - performance
  - UI
  - jank
  - image editor
---
What happens if you have expensive (i.e. computationally difficult) components that need to re-render frequently? If you don't do something about it, it'll cause the infamous bad actor of frontend UIs: the dreaded **JANK** 👻

So how can we handle this? You have a UI that's getting all sorts of new input and we need to re-render at _some_ interval, but it'd be great if we could slow it down a bit so we don't get a janky UI - we just slow down the parts we need so that we don't cause the rest of the UI to hang.

So let's fathom our project : you and I are building an image editor that happens all client-side. We are tasked with building the filter side of it, so users can adjust brightness, saturation, sepia-tone, contract, and blur.

> We're going to have to simulate jank as we're going to use CSS instead of actual image processing, and the browser is too good at applying CSS 😅. But in reality if we were trying to actual image processing in the browser, jank would be a real concern.

You'll see a place where jank is frequently introduced - where we're talking user input and applying the values to UI elements. The browser measures so frequently that it becomes an issue.

The other thing we want: if you have a fast device, we want it to render more frequently, and if you have an old device (or low battery!) want it to render way less frequently. This is _very_ hard to do without hooking into React itself to accomplish.
