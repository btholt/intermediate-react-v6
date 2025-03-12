---
title: Client-Side React
description: >-
  Learn about React Server Components in the 'Intermediate React v6' course by
  Brian Holt on Frontend Masters. Understand the trade-offs of React Server
  Components and different React rendering methods, including client-side,
  server-side, and static site generation. Explore the benefits and limitations
  of these approaches and optimize your web development skills using advanced
  React techniques.
keywords:
  - React
  - Server Components
  - Brian Holt
  - Frontend Masters
  - web development
---

> 💡 This course assumes you have taken or at least understand the concepts in [The Complete Intro to React v9][v9]. If you don't, please go back and take that one first, this will make a lot more sense.

React Server Components (which I will now abbreviate as RSC) are a concept that have been teased for a few years before landing in stable React in early 2025 when React 19 was officially released. No doubt they are a very interesting piece of technology and offer a lot of upside. They have some interesting trade-offs and we'll talk about them, but don't be quick to paint them as bad as some have, and definitely don't use them _everywhere_ either, they have a time and place.

Before we dive right into what an RSC is, it's helpful to understand _three_ other ways React renders your app.

## Client-side React

This is just normal React, what your learned in the Complete Intro. Your server does nothing for you; it just sends the client browser an empty HTML page and a full bundle of JS to render your app. All React components are rendered on the client - your server does not help.

This is how we've written React for a very long time (10+ years now) and will continue writing React for a long time yet this way.

Brian's (not-so) 🔥 Take: this is the default way you should write React. Everything is a contextual performance enhancement, and should only be reached for when: 1. the performance enhancement actually meaningfully helps (it frequently doesn't) and 2. you actually have a need for the performance enhancement (you frequently don't). This style of rendering yields the simplest apps that are easiest to write, easiest to maintain, and easiest to debug.

We're not going to write a client-side React app today – [Complete Intro to React][v9] covers that in-depth. So let's move on to writing SSG and SSR apps.

[v9]: https://react-v9.holt.courses
