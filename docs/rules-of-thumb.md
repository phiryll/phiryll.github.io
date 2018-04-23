# Jennifer’s Rules of Thumb
_These are **NOT** requirements! These are rules of thumb!_

## Aphorisms

* There are only three numbers in computer science: 0, 1, and Many.
* Avoid edge cases by making them not edge cases.
* Favor composition over inheritance.
* Favor immutability.
* Check-then-act cannot be made thread-safe without synchronization.
* Simple is usually better than complex but slightly faster.
* "Premature optimization is the root of all evil." - Donald Knuth

## Code Clearly

Your code should be understandable without having to spend hours
figuring it out. If you are coding a tricky algorithm, include links
to comprehensive descriptions in the comments. If it's original,
document it fully right there in the code. Use meaningful variable
names. Don't reuse variables for more than one purpose. Document all
assumptions fully, or (even better) encode them as assertions or
checks.

## Model Things As They Are

Don't get clever with your representations unless there's a **very**
good reason. Running 5% faster is not a good reason, unless lives
depend on it.

Model a user with a User object, and an account with an Account
object, and don't merge the two because a user can only have one
account. That's today. Tomorrow, your B&P team will tell you that they
just signed a contract for delivering the new capability of multiple
accounts per user, and that you have one month to deliver.

This principle goes further than that. Don't pack 4 independent byte
values into a long just because you can. Use numbers, strings, and
dates to model exactly those things. If you have a reference to other
data, model that in the natural way. For example, if you have foreign
keys in SQL, don't store them as numbers or varchars. Don't laugh,
I've seen it happen.

## Design For Change

This should be obvious, but for some reason it's not. Your
requirements will change, all the time. Plan for it. Use an agile
methodology if your situation allows.

## Decide What Constitutes A First-Class Entity

I've found it best to separate your model objects into exactly two
kinds of things, what I call first-class entities, and everything
else. My usage of that term differs from the common usage, so I really
need to rename the concept. A first-class entity:
* Has identity, and a name by which it can be retrieved or referenced.
* Has a generally independent life cycle. Its existence is usually not
dependent on the existence of some other entity.
* Can be shared, by reference.

Entities that are not first-class should probably not possess any of
these properties. For example, an address can have a zip code. If that
zip code is modeled as a number or text, then the zip code is not
first-class. If that zip code is a separate entity referenced by the
address, then it is first class. Deleting the address also deletes the
zip code in the former case, but not the latter.

## Keep All Input Data

If your software is in the business of taking input data and doing
something non-trivial with it, you will be much better off in the long
run if you keep that input around.

Don't sanitize it, or clean it up in any way. Store it exactly as you
got it. That also means you need to be capable of storing badly
formatted or otherwise invalid data. You should generally assume that
you have dirty data.

Let's take the simple example of text indexing. What do you do when
your tokenizer has a bug? When you encounter a new input format (MS
updates Word again, e.g.)? When you suddenly have to deal with
unplanned-for foreign languages? A customer in a previous job
encountered exactly those issues. Unfortunately, they decided that
they didn't have enough storage to keep those word docs around (don't
get me started on the relative size of the indices involved). So
they're stuck with bad output, permanently.

## Use Explicit String Encodings

Always make the character set explicit when encoding or decoding
strings. Prefer encodings capable of handling Unicode. UTF-8 is a good
default choice.

## Strive For Immutability

This is often invoked in reference to Java, and has a very specific
meaning (and requirements) there related to the JVM memory model. But
the principle is definitely general, for the same exact reasons.
Immutable data requires no coordination when shared. No coordination
means no buggy code to implement the coordination, no coordination
cost, and no consistency failures because some client failed to follow
the coordination rules. It just makes everything both simpler **and**
faster.

## Don't Hide Bugs

Another thing that should be obvious, but it isn't. This is a common example:
1. Code throws an NPE.
1. The programmer can't figure out why the reference is null at that point.
1. A deadline is looming.
1. The programmer adds this:
```
if( value == null ) { return null; }
```

I can't begin to describe how much this bothers me. Yes, the
programmer has kept an exception from reaching the UI, or otherwise
immediately disrupting the system. But they've done something far
worse - they've allowed it to continue through the system
unchallenged. Now the system is producing bad output, but there's no
exception or logging to let anyone know. The last major system I
worked on had a lot of this, and people's lives were potentially at
risk. There is no excuse for this. At the very least, an error should
be logged.

That segues nicely into situation #2. On that same project, we were
actually instructed to **remove** the logging of errors, because it
gave the field engineers the impression that our product wasn't
working (the actual language was more along the lines of "It's #%^").
Rather than educate our own employees at customer sites what a logged
error might mean, we decided to hide the bugs. At the very least, this
is unethical. And, you can't fix bugs of which you are unaware.

