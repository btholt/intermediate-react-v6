> You will need to open the folder `transitions` from the repo in your project.
>
> - [The starter template][starter]
> - [The completed project][completed]

We're making a little scoreboard project, let's familiar ourselves with it.

- Run `npm install`
- It's a plain ol' Vite project. No server side anything. You can do transitions with servers, we just don't need to.
- There's plain little Node.js server that just allow you to request the score of games 1 through 5. It doesn't do anything else.
- Run `npm run dev:server` in one terminal and `npm run dev:client` in another. One runs Vite and one runs the Node.js server.
- Vite will proxy the Node.js server from port 3000 to port 5173 so you can call localhost:5173/score?game=1 - all same origin
- Everything else is a pretty standard React project.

[starter]: https://github.com/btholt/irv6-project/tree/main/starter/transitions
[completed]: https://github.com/btholt/irv6-project/tree/main/completed/transitions
