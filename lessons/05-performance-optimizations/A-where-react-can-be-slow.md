---
description: >-
  Learn how to enhance React performance by using tools like memo, useMemo, and
  useCallback, as taught by Brian Holt in "Intermediate React v6" for Frontend
  Masters. Discover when to optimize your React components to avoid unnecessary
  re-renders and improve efficiency without preemptive adjustments. The course
  is perfect for developers seeking to refine their React skills with practical
  insights and advanced concepts.
keywords:
  - React
  - performance optimization
  - memo
  - useMemo
  - useCallback
  - Brian Holt
  - Frontend Masters
---

React generally has a very good performance profile, no matter what "thought leaders" may be tweeting about it. If you write React in a way consistent with the way I taught you in this class and the intro as well as the docs, you really don't need to concern yourself too much with "is this going to perform well" because React tends to be really good at making sure average code runs fast enough.

To be entirely honest, it was _hard_ to make non-performant code in React. We're going to have to resort to adding artificial slowdowns in our code because my MacBook could consistently churn through the code inefficient code anyway, making it hard to see performance gains we're going to get. Nonetheless, these concepts aren't important until they're _very_ important.

> 💡 I am about to show your four different ways to make React perform better in certain circumstances. My **strong** advice for is to not preemptively use these. You should wait until you have a performance problem in your UI before you try to optimize it. These tools make your code harder to read and makes bugs hard to track down (since you're getting outside of the normal way of writing React.) Have problems before you solve them.

So where does React have problems some times?

![React tree of five components: app, content, profile, profilepic, and profilename. App has children content and profile. Profile has children ProfilePic and ProfileName.](/images/profile.png)

Imagine a change happens in the Profile component and nowhere else. What happens? Profile will update, and as apart of that, ProfilePic and ProfileName _will also run_ even though nothing changed in them. Why? React isn't sure that the change Profile won't cause some cascading effect in either of those components, and re-runs them just to be sure.

Is this a problem? 99.99% no – most of the time these renders are so fast it's not even measurably different to just let them re-render, particularly if it's infrequent.

Do Content and App re-render? No. Only the children in the Profile tree do.

So what if ProfilePic is particularly expensive to render? Maybe it's an LLM-generated picture and it runs the LLM every time it re-renders. This could be bad: every change in Profile and App would cause re-renders in ProfilePic, even if it's not changing!

This is where three tools are going to come in very helpful: memo, useMemo, and useCallback. These going to give us a finer grain control to say "only re-render when this component indeed needs to re-render."
