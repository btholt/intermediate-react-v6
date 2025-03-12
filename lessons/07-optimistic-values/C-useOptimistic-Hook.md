---
title: "useOptimistic Hook"
description: >-
  Discover how optimistic UI updates in messaging apps like iMessage or WhatsApp
  give users immediate visual feedback before a message is truly sent. Learn
  with Brian Holt in the Intermediate React v6 course for Frontend Masters,
  focusing on using React Server Components and hooks like useOptimistic
  and useTransition for seamless web development.
keywords:
  - optimistic UI
  - React
  - useOptimistic
  - useTransition
  - Brian Holt
  - Frontend Masters
  - web development
---

Let's refactor to use transitions and `useOptimistic`.

```javascript
// at the top
import { useEffect, useOptimistic, useState, useTransition } from "react";

// add new hooks to top of render function
const [isPending, startTransition] = useTransition(); // we don't actually need isPending here today
const [optimisticThoughts, addOptimisticThought] = useOptimistic(
  thoughts,
  (oldThoughts, newThought) => [newThought, ...oldThoughts]
);

// inside postDeepThought
startTransition(async () => {
  addOptimisticThought(`${thought} (Loading…)`);
  [...] // rest of code
});

// change thoughts rendering to use optimisticThoughts
{optimisticThoughts.map((thought, index) => (
  [...] // code
))}
```

- That's it! This will now do an optimistic view of the data
- Immediately the user is shown their value plus "Loading" to let them know that their value is loading
- Once it's done loading, it will replace state which will kick off a new re-render. This will cause the Loading... to stop showing
- Because everything is wrapped in a transition, it's marked as a low priority re-render which means it's it easy for high priority re-renders to block this (which is what we want.)
- Error? Great, it will automatically rollback the state to where it was stable without the the optimistic value in there. Easy!

This is it for optimistic values! Very useful tool for any time you want temporary UI in front of the user. A lot of the edge cases are just handled for you!
