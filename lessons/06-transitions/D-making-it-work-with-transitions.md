Luckily the useTransition code is only slightly more complex.

```javascript
// add useTransition at top
import { useState, useTransition, useEffect } from "react";

// replace isPending useState
const [isPending, startTransition] = useTransition();

// replace getNewScore
async function getNewScore(game) {
  setGame(game);
  startTransition(async () => {
    const newScore = await getScore(game);
    startTransition(() => {
      setScore(newScore);
    });
  });
}
```

That's it!

- startTransition gives you back a isPending flag just like we had before.
- It now gives us a function to call that we're starting our UI transiton. We're essentially signalling to React that we're starting some sort of transition (in our case, an API request and a subsequent UI render) that we could elect to interrupt with another transition.
- Why two `startTransition` calls? I struggled with this as well. It's in the React docs. It's because in theory React updates aren't instant. If you're a Facebook-sized app, a React update can actually be on the order tens if not hundreds of milliseconds which is forever in terms of code execution, and this means it needs to be captured this way so that the use could in theory interrupt it after the API request has returned but while React is re-rendering.
- I'll say _in this particular case_ it's unnecessary, our app is tiny. But it's to make sure each transition is captured an atomic bit that can be interrupted.
