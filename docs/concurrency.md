# Notes on Concurrency and Distributed Systems

With respect to correctness, atomicity, consistency, availability, and
partition tolerance, a network of humans is no different than a
network of computers. For computers, this is true even within a single
machine between cores, L* caches, RAM, and disk.

To reason about whether some approach is
correct/atomic/consistent/..., I’ve found it can be helpful to
translate the problem to the domain of people before the invention of
the telegraph. The content and format of data is mostly irrelevant,
except for information designed to deal with being distributed (vector
clocks, e.g.). Clocks are not synchronized, probably not even within
some tolerance; all times are local only. All actors are
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

Protocols like TCP and devices like ECC RAM are designed to handle
some of these issues, so we don’t normally worry about getting a
mangled message. Some message delivery systems may deliver duplicate
messages.

Let’s say you send a command to Alice to “Do X and return an
ack/nack”. If you don’t get a reply, you will have no idea whether or
not X has actually been done. Similarly, after Alice sends a reply,
she may have no idea whether or not you received it. Even if you get
an ack, X might have been undone by someone else after Alice sent the
ack but before you received it.

Because of the long delivery time, it's much more obvious that any
information can be outdated by the time it is received. That’s also
true of memory and database reads in a computer, we just don’t think
about it most of the time. Protocols like Paxos still work in this
human problem domain, because they were designed with exactly these
kinds of limitations in mind.
