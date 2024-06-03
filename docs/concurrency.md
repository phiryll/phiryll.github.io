# Notes on Concurrency and Distributed Systems

Everything a computer does, a human can do. The only difference
between a distributed system of computers and a distributed system of
people is the time scale. With respect to correctness, atomicity,
consistency, availability, and partition tolerance, a network of
humans is no different. For computers, this is true not only for
distances from the next rack to the other side of the world, but even
within a single machine between cores, L* caches, RAM, and disk.

To reason about whether some approach is correct/atomic/consistent/…,
I’ve found it can be helpful to translate the problem to the domain of
people, specifically to before the invention of the telegraph. The
content and format of data is mostly irrelevant, except for
information designed to deal with being distributed (vector clocks,
e.g.). Clocks are not synchronized, probably not even within some
tolerance; all times should be treated as local only. All actors are
geographically remote from each other, and the only way to communicate
is by physical letters, sent by pony express. Some of the things that
can happen:

* It can take months to send a message. Message delivery times are not
consistent, something you sent later could arrive first.
* The messenger may not deliver your message, or may deliver the wrong
message.
* The recipient may die before your message arrives.
* The recipient may misinterpret your message.
* The messenger may not deliver the reply, or may deliver the wrong
reply.
* You may die before getting a reply.
* You may misinterpret the reply.
* ...

It may be the case that messengers send some kind of ack/nack without
waiting for the recipient to answer (which may also fail to get
delivered). Protocols like TCP and devices like ECC RAM are designed
to handle some of these issues, so we don’t normally worry about
getting a mangled message. Some message delivery systems also have the
possibility of delivering duplicate messages.

Let’s say you send a command to Alice to “Do X and return an
ack/nack”. If you don’t get a reply, you will have no idea whether or
not X has actually been done. Similarly, after Alice sends a reply,
she may have no idea whether or not you have acted on it. Even if you
get an ack, it may be the case that X was undone by someone else after
Alice sent the ack.

Because of the long delivery time, it is more obvious that any
information can be outdated by the time it is received. That’s also
true of memory reads in a modern CPU, we just don’t think about it
most of the time. You’ll find that protocols like Paxos still work in
this human problem domain, because they were designed with exactly
these kinds of limitations in mind.
