---
description: >-
  Learn how to utilize deferred values and sliders in React to apply dynamic
  filters to images, featuring examples like the 'DisplayImage' and 'Slider'
  components. Part of Intermediate React v6 by Brian Holt for Frontend Masters,
  this lesson covers implementing user input sliders, managing deferred values,
  and optimizing renders by simulating real-world application performance issues
  like jank. Perfect for developers looking to enhance their React skills with
  practical coding examples.
keywords:
  - React
  - Brian Holt
  - Frontend Masters
  - deferred values
  - image filters
  - React sliders
  - web development
---
> You will need to open the folder `deferred` from the repo in your project.
>
> - [The starter template][starter]
> - [The completed project][completed]

Let's hop into our src directory and create a Slider.jsx file.

```javascript
export default function Slider({ value, deferred, onChange, name, max }) {
  return (
    <li className="slider">
      <label htmlFor={name}>
        {name}
        {value !== deferred ? " (Updating)" : ""}
      </label>
      <input
        type="range"
        id={name}
        name={name}
        min="0"
        max={max}
        value={value}
        onChange={onChange}
      />
      <output htmlFor={name}>
        Actual Value: {value} | Deferred Value: {deferred}
      </output>
    </li>
  );
}
```

- This is what we're going to use gather user input for what values they want applied to their image.
- This is how you tell that something is in a inbetween state - deferred and value will be different. You'll see in just a sec.

Let's go create the image it will be applied to: DisplayImage.jsx

```javascript
import img from "../images/luna.jpg";

const JANK_DELAY = 100;

export default function DisplayImage({ filterStyle }) {
  const expensiveRender = () => {
    const start = performance.now();
    while (performance.now() - start < JANK_DELAY) {}
    return null;
  };

  return (
    <>
      {expensiveRender()}
      <img src={img} alt="Luna" style={{ filter: filterStyle }} />
      <p>Last render: {Date.now()}</p>
    </>
  );
}
```

- Beyond that it's just going to render of image (in my case it's my lovely asshole dog Luna, feel free to use your own!!)
- Why the date? I want you to be able to see how frequently that image gets re-rendered.
- expensiveRender is masquerading as a component with its return null.
- What it's really doing is just slowing itself down 100ms intentionally so it can simulate jank well.
- Feel free to modify `JANK_DELAY` to simulate speeding up and slowing down your rendering. 100ms was what I needed to notice big jank on my Macbook Air. Yours may be different.

Okay, now head to App.jsx

```javascript
import { useState } from "react";
import Slider from "./Slider";
import DisplayImage from "./DisplayImage";

export default function App() {
  const [blur, setBlur] = useState(0);
  const [brightness, setBrightness] = useState(100);
  const [contrast, setContrast] = useState(100);
  const [saturate, setSaturate] = useState(100);
  const [sepia, setSepia] = useState(0);

  const filterStyle = `
    blur(${blur}px)
    brightness(${brightness}%)
    contrast(${contrast}%)
    saturate(${saturate}%)
    sepia(${sepia}%)
    `;

  return (
    <div className="app">
      <h1>Deferred Value</h1>
      <DisplayImage filterStyle={filterStyle} />
      <ul>
        <Slider
          value={blur}
          deferred={blur}
          onChange={(e) => setBlur(e.target.value)}
          name="Blur"
          max="20"
        />
        <Slider
          value={brightness}
          deferred={brightness}
          onChange={(e) => setBrightness(e.target.value)}
          name="Brightness"
          max="200"
        />
        <Slider
          value={contrast}
          deferred={contrast}
          onChange={(e) => setContrast(e.target.value)}
          name="Contrast"
          max="200"
        />
        <Slider
          value={saturate}
          deferred={saturate}
          onChange={(e) => setSaturate(e.target.value)}
          name="Saturate"
          max="200"
        />
        <Slider
          value={sepia}
          deferred={sepia}
          onChange={(e) => setSepia(e.target.value)}
          name="Sepia"
          max="100"
        />
      </ul>
    </div>
  );
}
```

- Now we can see something rendered. Notice it's _super_ janky.
- You'll notice that DisplayImage is re-rendering 100% of the time. This is because we haven't memoized it and also filterStyle is changing every time a slider is. When props change, a component will always re-render, memoized or not.

[starter]: https://github.com/btholt/irv6-project/tree/main/starter/deferred
[completed]: https://github.com/btholt/irv6-project/tree/main/completed/deferred
