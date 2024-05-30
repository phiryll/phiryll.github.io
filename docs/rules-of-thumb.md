# Jennifer’s Rules of Thumb
_These are **NOT** requirements! These are rules of thumb!_

Every rule here has caused a major project failure in my past when
broken. That's why they're here. However, some rules aren't
appropriate for some kinds of programming, like embedded systems,
games, or flight control software. You know your system better than I
do.

## Aphorisms

* There are only three numbers in computer science: 0, 1, and Many.
* Avoid edge cases by making them not edge cases.
* Favor composition over inheritance.
* Favor immutability.
* Check-then-act cannot be made thread-safe without synchronization.
* Simple is usually better than complex but slightly faster.
* "Premature optimization is the root of all evil." - Sir Tony Hoare

## Code Clearly

Entire books have been written on this topic, but the gist is always
the same - make your code easy for others to understand. Many of the
points made in this document are specific examples of this. Roughly
70% of software development costs are maintenance costs, not the cost
of the initial release. Being easier to understand means being faster
to ship, easier to debug, and less likely to have bugs in the first
place. It also helps tremendously with onboarding new engineers.

Some small examples:

* Use meaningful variable names.
* Don't reuse variables for more than one purpose.
* Document all assumptions fully, or even better, encode them as
  assertions or checks.
* Use early returns.
* Indent exceptional behavior, keep the happy path outdented.
* Keep functions focused on one job and reasonably small, for some
  definition of "reasonably".
* Include links to a tricky algorithm in the comments, or document it
  fully right there.

## Don't Optimize Prematurely

Complicating code for performance reasons is seldom a good idea unless
you're getting an algorithmic improvement out of it. Even then, it
won't be worth it if the optimization doesn't make a perceptable
difference overall. You should write simple and correct code first,
including good tests and performance instrumention. Only after
measuring performance under real world conditions should you decide
what to optimize. If you already have good tests, you won't be as
likely to break things when you experiment with different
optimizations. In particular, micro-optimizations probably aren't
going to make a difference if that thread of execution is also
performing I/O.

All of that said, there are situations where a 1% performance
difference can be huge, like software that interfaces with the
physical world, or systems that operate at Internet scale. If you have
10,000 servers, a 1% improvement is serious money. But even in these
cases, you should still get it correct first and optimize only after
measuring.

## Model Things As They Are

Don't get clever with your representations unless there's a **very**
good reason. Running 2% faster is usually not a good reason to
sacrifice maintainability.

For example, model a user with a User object, and an account with an
Account object. Don't merge the two because a user can only have one
account. That's today. Tomorrow, your boss will tell you that they
just signed a contract for delivering multiple accounts per user, and
that you have one month to deliver.

More examples:
* Don't pack 4 independent byte values into a long just because you
  can.
* Use numbers, strings, and dates to model exactly those things.
* If you have a reference to other data, model that in the natural
  way. For example, if you have foreign keys in SQL, don't store them
  as numbers or varchars. Don't laugh, I've seen it happen.

## Design For Change

This should be obvious, but for some reason it isn't. Your
requirements will change, all the time. Plan for it. Use an agile
methodology if your situation allows.

## Decide How Unique IDs Are Made Very Carefully

If possible, avoid using semantic fields in your data as unique
identifiers, **especially** if they are computed. The best unique
identifiers are opaque and permanent.

## Cleanly Separate Independent and Dependent Entities

I'm using "entity" here as a general concept, because words like
"object" and "record" are overloaded.

Generally, an entity should have either an independent or a dependent
life cycle, whether its existence is dependent on the existence of
some other entity. This should be clear either from the entity's
definition, or from comments if the definition doesn't make this
clear.

An independent entity should have identity, and a key by which it can
be retrieved or referenced. An independent entity can be shared by
using that reference. A dependent entity should not have any of these
properties. It's best to avoid entities which have some aspects and
not others, it should be all or nothing.

For example, an address can have a zip code. If the zip code is
modeled as a numerical or text field of the address, then the zip code
is dependent. If the zip code is a separate entity referenced by the
address, then it is independent. Deleting the address also deletes the
zip code in the former case, but not the latter.

## Keep All Input Data

If your software is in the business of taking input data and doing
something non-trivial with it, you will be much better off in the long
run if you keep that input around.

Don't sanitize it, or clean it up in any way. Store it exactly as you
got it. That also means you need to be capable of storing badly
formatted or otherwise invalid data. You should generally assume that
any data given to you can be dirty. This does **not** mean raw data
should be stored in your primary database. It doesn't even need to be
accessible by your main application(s), but it should at least be in a
log file or similar.

This includes requests and responses to and from third party systems.
It's very difficult to debug interactions with systems you don't own
if you don't even know what the inputs or outputs were.

Let's take the simple example of text indexing. What do you do when
your tokenizer has a bug? When you encounter a new input format (MS
updates Word again, e.g.)? When you suddenly have to deal with
unplanned-for foreign languages? If you don't have the original input,
you won't be able to correct any incorrect data derived from it.

## Use Explicit String Encodings

Always make the character set explicit when encoding or decoding
strings. Prefer encodings capable of handling Unicode. UTF-8 is a good
default choice.

## Strive For Immutability

This is often invoked in reference to Java, where it has a very
specific meaning (and requirements). But the principle is general, for
the same reasons. Immutable data requires no coordination when shared.
No coordination means no buggy code to implement the coordination, no
coordination cost, and no consistency failures because some client
failed to follow the coordination rules. It just makes everything both
simpler **and** faster.

## Don't Hide Bugs

This should be obvious, but isn't. This is a common example:
1. Code throws an NPE.
1. The programmer can't figure out why the reference is null at that point.
1. A deadline is looming.
1. The programmer adds this:
```
if( value == null ) { return null; }
```

This is very, very bad. Yes, the programmer has kept an exception from
reaching the UI, or otherwise immediately disrupting the system. But
they've done something far worse - they've allowed bad data to
_silently_ continue through the system unchallenged. Now it's
producing bad output, but there's no exception or logging to let
anyone know. A project I worked on had a lot of this, and people's
lives were at risk. There is no excuse for this. At the very least, an
error should be logged.

That segues nicely into situation #2. On that same project, we were
told to **remove** error logging, because it gave the field engineers
the impression that our product wasn't working (the actual language
was more along the lines of "It's #%^"). Rather than educate our own
employees, management decided to remove error logging. This is
exceedingly unethical. Ironically, making the project seem like it had
no errors caused it to accumulate even more errors instead, errors
which could no longer be found.
