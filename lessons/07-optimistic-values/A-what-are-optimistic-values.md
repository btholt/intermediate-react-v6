Let's say you are texting your friend. You open iMessage, WhatsApp, Signal, ICQ, AIM, MSN Messenger etc. What is the actual order of operations?

1. You type your message
1. You click send
1. The message shows up in the log of messages _(this is the one we're interested in)_
1. The message is actually sent over the network
1. The recepient receives it

That #3 is interesting - you get visual feedback in your log of messages as if the message was already sent. In your mind, the message is sent, and the UI reflects that. In reality the message hasn't even left the device when it's first rendered that way, so in some ways it's a bit misleading, but this **optimistic** display of UI is more closely reflects the user's mental model.

That's what we're talking about, optimsitic UI updates - we're doing some backend work behind the scenes, but we're going to optimistically show the user that their update was done. This is possible to do without the ready-made hook `useOptimisticValue` but it was such a pain before. Now it's fairly easy to use in conjunction with useTransition (necessary to identify a low priority re-render) to show an intermediary state.
