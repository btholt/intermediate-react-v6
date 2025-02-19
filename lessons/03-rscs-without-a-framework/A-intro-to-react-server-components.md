We've talked about client-side React apps (no server), static site generation apps (server runs only at compile time), and server-side rendered app (server renders initial page but then client does the rest). Now we're going to talk about react server components which involves the server even more.

## What are RSCs?

RSCs are not SSR 2.0.

This is a common misconception that I just want to dispel upfront: these things are relatively unrelated in the sense you can SSR without RSCs, RSCs without SSR, both, or neither!

RSCs introduce the notion of a server to an ongoing React app. Whereas SSR renders the page once and then is no longer involved in the ongoing React app, RSCs introduce the notion that the server continues to render parts of the app. An RSC is a component that only renders on the server and sends its complete markup to the app – the client-side app doesn't even have the code to render the component client-side - it can only receive markup from a server and then insert that into the app.

> A React server component is a component that _only_ renders on the server.

Why is this useful? For a couple of reasons. No matter how much of an interactive app you have, large swaths of it are inert markup, text, and content. You don't need React to generate HTML that shows a few headers and paragraphs. We do it because it's simpler to keep all the code together, but the truth of it is just that: in the grand scheme of things, React is really only needed client-side to add interactivity to an app. Everything else is developer experience and code organization.

Enter RSCs. For one, they're just a simple rendering of a page's content, rendered once, on the server. This means that it doesn't matter how powerful a user's device is, the rendering is happening on the server. It also means users aren't receiving the code for pages they aren't going to visit, so the bundle can be split pretty well.

But the biggest, most powerful feature (in my opinion) is that RSCs can use modules that can only be used on the server. Let's say you're making a messaging app (like we're about to): that data all has to be stored in a database somewhere. Normally you'd need a React app fetch that data with a get request, but with RSCs you can just make the request directly in the React component 🤯

All the secret connection strings you need to have to access your database? Never leave your server! It makes writing React components that read & write from databases, private APIs (like OpenAI, etc.), or need other server-side components very fluent to write. We're about to see how.

## Okay so don't write RSCs by hand

But we're going to lol.

RSCs are really meant to be written by frameworks and _not_ be written by the masses. It's a pretty deep integration to build with your bundler (and they only ship Webpack at the moment) and it's very hard to get right and not get a net-negative on performance.

It's also just a lot of code to write, and there already a few frameworks out there that will do it better than you likely have time to.

- [Next.js][next]
- [React Router v7 / Remix][remix] (soon)
- [TanStack Start][tanstack] (soon)

Next.js works with RSCs today, as of the writing of this course, and embraces the full surface capabilities of RSCs. React Router v7 and TanStack Start both have expressed that at least initially they will be selective of which features that they embrace that make sense with their frameworks (and I support that! No reason to shoehorn it in.)

We'll end up writing a whole app in Next.js by the end of the course, but we're going to start with doing it by hand!

## NotePasser

Remember in school when you would pass notes by hand? No? Well, back in my day (_shakes fist at passing cloud_) we didn't have cell phones and had to rely penmanship and steathily trying to pass a paper note to our friend in class.

We're going to build an app inspired by that today, where we can pass notes from one user to another.

One note - I had originally built this app to have a full on auth provider, but realized it was adding a lot of complexity to the app that wasn't teaching you how to do React. Instead this app is going to sort of fake auth. We're just going to assume whoever is using the app is always user ID `1`. This is done in the name of simplicity so we can just focus on building React apps, but feel free to later to add a cool auth provider like [Neon Auth][neon] (I helped build this one!!), [Clerk][clerk], [Descope][descope], or any other of your favorites.

[next]: https://nextjs.org/
[remix]: https://reactrouter.com/
[tanstack]: https://tanstack.com/start/latest
[neon]: https://neon.tech/
[clerk]: https://clerk.com/
[descope]: https://www.descope.com/
