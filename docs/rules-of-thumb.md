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
* Simple is usually better than complex but slightly faster, or  
  "Premature optimization is the root of all evil." - Sir Tony Hoare

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
* Keep functions reasonably small and focused on one job, for some
  definition of "reasonably".
* Include links to a tricky algorithm in the comments, or document it
  fully in the code.

## Don't Optimize Prematurely

Complicating code for performance reasons is seldom a good idea unless
you're getting an algorithmic improvement out of it. Even then, it
won't be worth it if the optimization doesn't make a perceptible
difference overall. You should write simple and correct code first,
including good tests and performance instrumentation. Only after
measuring performance should you decide what to optimize, preferably
under real world conditions. If you already have good tests, you won't
be as likely to break things when you experiment with different
optimizations. Micro-optimizations probably aren't going to make a
difference if that thread of execution is also performing I/O.

All of that said, there are situations where a 1% performance
difference can be huge, like software that interfaces with the
physical world, or systems that operate at Internet scale. If you have
10,000 servers, a 1% improvement is serious money. But even in these
cases, you should still get it correct first and optimize only after
measuring.

## Model Things As They Are

Don't get clever with your representations unless there's a **very**
good reason. See the previous point.

For example, model a user with a User object, and an account with an
Account object. Don't merge the two because a user can only have one
account. That's today. Tomorrow, your boss will tell you that you have
one week to deliver multiple accounts per user.

More examples:
* Don't pack 4 independent byte values into a long just because you
  can.
* Use numbers, strings, and dates to model exactly those things.
* If you have a reference to other data, model that in the natural
  way. For example, if you have foreign keys in SQL, don't store them
  as basic numbers or varchars. Don't laugh, I've seen it happen.

## Design For Change

This should be obvious, but for some reason it isn't. Your
requirements will change, all the time. Plan for it.

Use an agile methodology if your situation allows. I very much mean
this in the sense of the [Agile
Manifesto](https://agilemanifesto.org/), and **absolutely not** in the
sense of whatever
[this](https://www.researchgate.net/figure/SAFe-Big-Picture-In-SAFe-R-all-teams-are-part-of-the-Agile-Release-Train-ART-and-ARTs_fig3_323994688)
is. This is for the engineering team and their immediate product
manager to use, not the rest of the business. If a VP is involved,
you're doing it wrong.

Don't hard code constants that are likely to change, things like
customer discounts, tax rates, time zones, countries, AWS regions,
etc. Almost everything defined by people, and especially governments,
is likely to change.

If you can handle a probable future use case quickly and without
adding much complexity, just do it. If you can't, create a low
priority issue to save time if it ever becomes a problem. Some things
are worthwhile to get right early, because they're usually expensive
to change later. At least have a plan if you can't deal with it in the
short term. Here are a few:
* user authentication and authorization
* security  
  This includes encryption, log file security, firewalls,
  service-to-service comms, etc. It's not just user auth.
* logging  
  Be careful, many logging services will either prohibit or truncate
  long entries.
* monitoring
* relationship cardinality (one-to-one, one-to-many, many-to-many)  
  In SQL, existing queries might not fail hard when you change the
  cardinality, and will instead just silently return incomplete data.
  This can be very difficult to track down, especially if the queries
  are constructed by a library.
* accounting integration, if relevant  
  Engineers tend to only store what they need to solve their immediate
  problem, but accounting usually needs a lot more than that. You
  absolutely must partner with the accounting team when adding any new
  accountable events. Do it early, because their requirements will
  probably change your design. They'll be very, very angry if they
  have to make a manual entry for every transaction, and you'll need
  to hire a lot more of them. Accountable events should never be
  editable after they have been created, new correcting entries are
  made instead. There are also retention requirements.

## Don't Expose Your Database Schema

If you do, clients will come to depend on it and you'll eventually be
forbidden from changing it for any reason, even to fix a bug.

This should be followed at the service level, not just at the
application level. Every database should be wholly owned by a single
service, which should provide an API for the service, not the
database.

## Decide How Unique IDs Are Made Very Carefully

If possible, avoid using semantic fields in your data as unique
identifiers, **especially** if they are computed. The best unique
identifiers are opaque and permanent.

For example, if you use someone's SSN as a primary key, what happens
if you later discover that there was a typo?

## Cleanly Separate Independent and Dependent Entities

I'm using "entity" here as a general concept, because words like
"object" and "record" are overloaded.

Generally, an entity should have either an independent or a dependent
life cycle. This should be clear from the entity's definition, or from
comments if necessary.

An independent entity should have identity, and a key by which it can
be retrieved or referenced. An independent entity can be shared by
using that reference. A dependent entity should not have any of these
properties. It's best to avoid entities which have some aspects and
not others, it should be all or nothing.

For example, an address can have a zip code. If the zip code is
implemented as a numerical or text field of the address, then the zip
code is dependent. If the zip code is a separate entity referenced by
the address, then it is independent. Deleting the address also deletes
the zip code in the former case, but not the latter (ignoring
cascade-delete semantics).

## Keep All Input Data

If your software is in the business of taking input data and doing
something non-trivial with it, you will be much better off in the long
run if you keep that input around.

Don't sanitize it, or clean it up in any way. Store it exactly as you
got it. That also means you need to be capable of storing badly
formatted or otherwise invalid data. This does **not** mean raw data
should be stored in your primary database. It doesn't even need to be
accessible by your main application(s), but it should at least be in a
log file or similar.

This includes communication to and from third party systems. It's very
difficult to debug interactions with systems you don't own if you
don't even know what the inputs or outputs were.

For example, let's say you have a text indexer. What do you do with
existing derived data when your tokenizer has a bug? When you
encounter a new or updated input format? When you suddenly have to
deal with unplanned-for foreign languages? If you don't have the
original input, you won't be able to correct any data derived from it.

## Use Explicit String Encodings

Always make the character set explicit when encoding or decoding
strings. Prefer encodings capable of handling Unicode. UTF-8 is a good
default choice.

## Strive For Immutability

This is often invoked in reference to Java, where it has a very
specific meaning. But the principle is general, for the same reasons.
Immutable data requires no coordination when shared. No coordination
means no buggy code to implement the coordination, no coordination
cost, and no consistency failures because some client failed to follow
the coordination rules. It just makes everything both simpler **and**
faster.

## Don't Hide Bugs

This should be obvious, but isn't. This is an example I've seen too
often:
1. Code throws an NPE.
1. The programmer can't figure out why the reference is null at that
   point.
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
anyone know. At the very least, an error should be logged.

That segues nicely into situation #2. On that same project, we were
told to **remove** error logging, because it gave the field engineers
the impression that our product wasn't working. Rather than educate
our own employees, management decided to remove error logging. Given
the nature of the project, this was exceedingly unethical. Ironically,
making the project seem like it had no errors caused it to accumulate
even more errors instead, errors which could no longer be found or
fixed.
