Let's talk about [React 19][19].

It came out at the tail end of 2024 officially but really had publicly been worked on [since late 2022][rfc]. So it's been worked nearly 3 years and been stable and in production (via Next.js as well as internally at Facebook) since [late 2023][next].

All of this to say: it's fairly new to _stable_ React but it's been worked on and shipped via "unstable" channels for nearly 2+ years. We've had a lot of time to learn some good and bad patterns, and it's going to be fun to learn them together with you.

Primarily we are going to concern ourselves with React Server Components. This is a new way of writing React components that allows your React components to render partially on the server and then to be served in a client side app in React. It's very cool; it allows you to do things like safely directly query your database from within React, something we couldn't (or at least shouldn't) do previously.

The Complete Intro course does some minor cover of version 19 features and the good news is nothing changed in what I taught - everything I taught stayed stable so no corrections since version was released after v9 of my intro course!

[19]: https://react.dev/blog/2024/12/05/react-19
[rfc]: https://github.com/reactjs/rfcs/pull/188
[next]: https://nextjs.org/blog/next-14
