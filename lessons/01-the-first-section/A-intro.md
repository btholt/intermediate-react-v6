> 🚨 You do not need to watch/read previous versions of this course, Intermediate React. This is just the sixth revision of the course. You **do** need to have taken [the Complete Intro to React v9][v9]

Hello! And welcome to the sixth revision of Intermediate React as taught for Frontend Masters! This is a total rewrite of the Intermdiate curriculum and I am so excited for you to take it. Previous versions of this course focused on the ecosystem around React and [v5][v5] of this course is still valid for that: Tailwind, TypeScript, Redux, and other things. Beyond that, Frontend Masters has individual courses on all of those things as taught by other instructions. [Check out the learning path][path] to find all of those.

This course logically be divided into two halves: server-side React and performance with React. Both halves are going to focus on the capabilities of React itself rather than tools around it. At the end of the course, you are going to understand all of the tools available to you to write React apps. There are some more APIs that we don't cover but they're so niche that they're not even worth coverings - mostly for library authors.

## Who is this course for?

You! This course is for anyone looking to get more experience in React or deepen their expertise in it. No experience is necessary beyond the intro course. However, I guarantee anyone who is interested in React will learn something in here. This probably shouldn't be your first programming or JavaScript course, but beyond that, it should be comfortable for anyone. We do use some Node.js to run our API and for the build tools but extensive Node.js experience is not necessary, I'll give you the commands necessary to run it and cover the Node.js we do use.

## Who am I?

![Brian teaching](/images/social-share-cover.jpg)

My name is Brian Holt and I've been writing React for a long time. I shipped Reddit's first React code in 2014.

<blockquote class="twitter-tweet" data-dnt="true" data-theme="light"><p lang="en" dir="ltr">We shipped reddit&#39;s first production <a href="https://twitter.com/reactjs?ref_src=twsrc%5Etfw">@reactjs</a> code last week, our checkout process.<a href="https://t.co/KUInwsCmAF">https://t.co/KUInwsCmAF</a></p>&mdash; Brian Holt (@holtbt) <a href="https://twitter.com/holtbt/status/493852312604254208?ref_src=twsrc%5Etfw">July 28, 2014</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I went on to write React at a variety of large companies and it's been my goto tool for over a decade at this point. It's safe to say it's been a defining feature in my career.

I currently work as a staff product manager at [Neon][neon] working on developer tools and developer experience. I loved working on dev tools and dev experiences so much that I ended up working in tools and strategy. Previous to Neon, I've worked at Snowflake, Microsoft, LinkedIn, Netflix, Reddit, and some other startups. I've done everything from VP of product to dev rel to staff engineering tech lead.

When I'm not working or teaching, you'll find me hanging out with my wife, son, and my soon-to-arrive daughter! I've lived the past six years in Seattle but I've just moved to Sacramento to get a bit more sun and to live close to amazing snowboarding in Tahoe! 🏂 I also enjoy hazy IPAs, Islay Scotches, road cycling, and playing Marvel Rivals poorly.

Please catch up with me on social media! Be aware that I'm awful at responding to DMs!!

- [𝕏][x]
- [Bluesky][bs]
- [LinkedIn][li]
- [GitHub][gh]

## Where to File Issues

I write these courses and take care to not make mistakes. However when teaching over ten hours of material, mistakes are inevitable, both here in the grammar and in the course with the material. However I (and the wonderful team at Frontend Masters) are constantly correcting the mistakes so that those of you that come later get the best product possible. If you find a mistake we'd love to fix it. The best way to do this is to [open a pull request or file an issue on the GitHub repo][issues]. While I'm always happy to chat and give advice on social media, I can't be tech support for everyone. And if you file it on GitHub, those who come later can Google the same answer you got.

## How the repo works

There are two repos for this class: [the website you're currently on][site] and [the example projects][projects]. To get set up, clone or [download][zip] the projects repo:

```bash
git clone https://github.com/btholt/irv6-project.git
```

We are going to work on a few separate projects. The first few are going to be written from scratch (except a little SQLite database and a CSS file, we'll talk about those when we get there.) So those don't have incomplete projects to start with, we'll just start from nothing.

For the following ones, they have empty starter projects because I don't want to set up Vite a million times. We'll have you just open the repo to the right folder and start from there. Again, we'll have instructions on each lesson as we go.

> And one last request! [Please star this repo][site]. It helps the course be more discoverable and with my fragile ego.

[x]: https://twitter.com/holtbt
[bs]: https://bsky.app/profile/brianholt.me
[li]: https://www.linkedin.com/in/btholt/
[gh]: https://github.com/btholt
[site]: https://github.com/btholt/intermediate-react-v6
[projects]: https://github.com/btholt/irv6-project
[issues]: https://github.com/btholt/irv6-project/issues
[neon]: https://neon.tech/
[zip]: https://github.com/btholt/irv6-project/archive/refs/heads/main.zip
[v9]: https://frontendmasters.com/courses/complete-react-v9/
[v5]: https://frontendmasters.com/courses/intermediate-react-v5/
[path]: https://frontendmasters.com/learn/react/
