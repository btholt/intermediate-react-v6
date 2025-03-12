---
description: >-
  Learn how to optimize React performance using 'useDeferredValue' to manage low
  priority rendering, a tool that dynamically adapts to user's device speed, in
  Brian Holt's "Intermediate React v6" course by Frontend Masters.
keywords:
  - React
  - useDeferredValue
  - performance optimization
  - deferred rendering
  - Brian Holt
---
So how can we make things less janky?

In the past we have to use something like [throttle or debounce][throttle] - we'd limit how frequently we'd allow the functions to update. This works but it's a blunt instrument - it's complicated code (I have to look up debounce vs throttle every time) and it doesn't scale with the user's device. Slow and fast devices alike will get slowed down.

Enter `useDeferredValue`. It uses React's low priority rendering to say "hey, if you're still busy doing high priority stuff, feel free to delay changing this low priority stuff" to React. What's cool about that is this _will_ scale with the user's device. A fast device will chew through updates quickly and get to the low priority updates quickly while a slower device will be given space to sort through high priority updates and wait for the lower ones.

Let's make it happen.

```javascript
// replace filterStyles
const deferredBlur = useDeferredValue(blur);
const deferredBrightness = useDeferredValue(brightness);
const deferredContrast = useDeferredValue(contrast);
const deferredSaturate = useDeferredValue(saturate);
const deferredSepia = useDeferredValue(sepia);

const filterStyle = `
  blur(${deferredBlur}px)
  brightness(${deferredBrightness}%)
  contrast(${deferredContrast}%)
  saturate(${deferredSaturate}%)
  sepia(${deferredSepia}%)`;


// change deferred
<Slider
  value={blur}
  deferred={deferredBlur}
  onChange={(e) => setBlur(e.target.value)}
  name="Blur"
/>
<Slider
  value={brightness}
  deferred={deferredBrightness}
  onChange={(e) => setBrightness(e.target.value)}
  name="Brightness"
/>
<Slider
  value={contrast}
  deferred={deferredContrast}
  onChange={(e) => setContrast(e.target.value)}
  name="Contrast"
/>
<Slider
  value={saturate}
  deferred={deferredSaturate}
  onChange={(e) => setSaturate(e.target.value)}
  name="Saturate"
/>
<Slider
  value={sepia}
  deferred={deferredSepia}
  onChange={(e) => setSepia(e.target.value)}
  name="Sepia"
/>
```

- We still have jank - that's expected, we have a 200ms delay chucked into our code. But it's working _much_ better than it was.
- There's probably a clever way to just useDeferredValue with just the filterStyle or other things - notice it's deferred **value** and not deferred **state** - you can use it with any value.

Let's take it one step further. As long as a deferred value hasn't changed, that means the filterStyle string hasn't changed. Using `memo` like we learned before allows us to prevent the notorious jank-inducer `expensiveRender` from running. Let's go do that.

In DisplayImage

```javascript
import { memo } from "react";

export default memo(function DisplayImage({ filterStyle }) {
  // other code here
});
```

- So much less jank
- Now watch that `Last render` value - notice it doesn't change until the deferred value catches up

That's it! Using our new tools we can even work around heavy computation by using a little help from the React scheduler!

[throttle]: https://css-tricks.com/debouncing-throttling-explained-examples/
