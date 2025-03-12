---
description: >-
  Explore Brian Holt's 'Intermediate React v6' course, providing insights on the
  use of Node.js, preferred development tools like Visual Studio Code, and a
  balanced perspective on AI integration in coding. Learn how these tools and
  technologies enhance the process of writing efficient React Server Components,
  as taught for Frontend Masters.
keywords:
  - React
  - Node.js
  - Visual Studio Code
  - AI in coding
  - Brian Holt
---

## Node.js

You'll need to have a Node.js version installed, and preferably something after v20.16. I wrote this course with 22.14 but it should be fairly future-proof.

I use [fnm][fnm] to manage my Node.js versions (similar to nvm).

I _think_ this course would work with recent versions of [bun][bun] but it's untested. Beware if you decide go down this path.

## Tools FAQ

### What tools are your using?

- Visual Studio Code – I used to work at Microsoft on VS Code so it's no surprise that I'll be using it in this course. We'll also be using a few extensions that I'll call out as we get there.
  - I frequently use Copilot and Cursor too, but I'll be disabling it for this so we can go through together, keystroke by keystroke.
- Firefox – I want more than Chromium to exist so I support Firefox where I can. Feel free to use any browser; it won't matter in this course.
- Terminal.app – I used to use iTerm2 and Hyper but in the end I appreciate how fast the default terminal is.

### What <font/theme/extension> are you using?

- Visual Studio Code
  - Dark+ Theme – It comes installed by default but it's not the default theme anymore. I'm so used to it that I can't switch.
  - [MonoLisa][monolisa] font – I like fonts and I look at it all day so I was okay paying for it. I have [ligatures][ligatures] enabled which is why you might see strange glyphs. If you want ligatures but don't want to pay, the linked ligature article has a few. I like Cascadia Code from Microsoft.
  - [vscode-icons][vscode-icons] – Lots of neat icons for VS Code and it's free.
- Terminal
  - zsh – It comes with macOS now and I'm _way_ too lazy to switch back to bash.
  - [Dracula theme][dracula] – I like the pastels. I would use it in VS Code too if Dark+ wasn't ingrained in my blood.
  - [Starship Prompt][starship] – Very cool prompt that's just pretty. Also shows you what sort of project you're in which is occasionally useful
  - [Caskaydia Cove Nerd Font][nerd] – This works with Starship prompt to give you the JS logos and all those extra glyphs. It's based on Cascadia Code.

## A note on the use of AI in this course

This course was written by me. I used AI in a few places for assistance, but every piece of code and every line of text on this site was written by me. The only places that it's actually AI written directly are the example rows of the database and the sample markdown - just filler content that I prompted Claude for. I do let OpenAI write all the SEO for these webpages (like all the OpenGraph keywords and descriptions.)

My stance on AI is two-fold. In the hands of an experienced wielder AI is incredibly powerful. When I say experienced, I mean it in two ways: 1. experienced in writing the sort of code they're prompting for and 2. experienced in the usage of how to prompt and use AI well. In our case, AI will allow you to amplify the rate you can write React code, but that's only possible if you understand React. Sure, you can "vibe" code React with a very shallow understanding, but you're going to build tall houses of cards. You'll be better served by turning off the AI and handwriting code, understanding each precept, and then using that knowledge to co-write solid React with your AI toolkit.

Do I use AI? Yes, it helps me work faster. But like a manager reviewing their interns' code, I am responsible for what I decide to ship, and so I keep a tight rein on it.

[ligatures]: https://worldofzero.com/posts/enable-font-ligatures-vscode/
[monolisa]: https://www.monolisa.dev/
[vscode-icons]: https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons
[dracula]: https://draculatheme.com/terminal
[starship]: https://starship.rs/
[nerd]: https://www.nerdfonts.com/font-downloads
[fnm]: https://github.com/Schniz/fnm
[bun]: https://bun.sh/
