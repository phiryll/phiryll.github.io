# Data History and Provenance

Described here are the essential capabilities of a data storage
system. Some use cases are then presented demonstrating the kinds of
problems that such a system could solve. Building the described system
would be a non-trivial endeavor; this text merely describes the end
goal, without any details of how one might get there.

## All History is Maintained

Every data change is recorded. Historical data can be retrieved from
any point in the past, and time-based queries and analyses are
supported. When data is deleted, it is only marked as having been
deleted. Every change must include information about it, in particular
who did it and a message describing the reason for the change. That
requirement is also extended to any automated processes, not just end
users. A side effect of this is that these actors must have a
representation in the system. The identity of an automated process
must include which part of the process (if it does multiple things
and/or uses multiple algorithms) and its version information.

The history of data can be branched, and those branches can be private
to users. In other words, a user can run a number of what-if scenarios
on the data, or even a what-if on top of a what-if, without impacting
the authoritative data store.

Note that this is exactly the same practice that software developers
have been using for decades to track changes to program source code.

A beneficial side effect of this feature is that all data is
effectively immutable, greatly reducing the need to manage concurrent
access.

## Relationships are Represented

Data can have relationships to other data, and those relationships are
understood as such by the system. However, it should not be the case
that relationships must have a valid, existing target. Just like in
the world wide web, dead links should be allowed. Allowing dead
relationship targets means the system doesn't have to expend resources
checking or maintaining their consistency.

Relationships should not be constrained in multiplicity. The system
should never have to check whether some other relationship exists when
adding a new relationship.

## Uncertainty is Represented

It must be possible to represent not just actual facts, but
assertions. In this usage, an assertion is a relationship between the
data doing the asserting and the data being asserted, including the
uncertainty as measured by the asserter. Uncertainty in this case is
not a probability, but a certainty factor. For example, "Alice is 70%
certain that we will win the XYZ contract", or "Link-analysis
algorithm ABC version 3.5.2 is 93.45% certain that Osama bin Laden is
holed up at X."

In particular, contradictory information can be represented. It should
be possible for two algorithms, or end-users, to arrive at different
conclusions given the same inputs.

## Data has Provenance

Stated another way, most data comes from somewhere. That somewhere
should also be in the data store, with a relationship between the data
and its source. This is not typically the same thing as the user who
changed the data, although it might be. The source might be a
newspaper, a sensor for a measurement, or an original intelligence
report for a derived intelligence report. Generally, data might have
multiple sources (a conclusion based upon many inputs, e.g.).

A single data item may have many different computed data items derived
from it, even when those computed items represent the same thing. For
example, different algorithms for performing OCR upon an image may be
run, each producing different text.

## General Principles

Some of these are not required to support the above features, but are
just generally good ideas.

Support a binary data type that allows any possible values (i.e., not
null-terminated, etc.).

Store all externally supplied data in its raw form, in the data store.
All data sanitization, normalization, etc. is something you do
afterwards, and should be treated no differently than producing any
other kind of derived data. Fundamentally, normalizing line endings or
removing bad characters is no different than entity extraction; some
data goes in and new data computed from that comes out. One reason to
do this is to avoid dropping data on the floor because it has failed
some sanity check. Another reason will be covered by the use cases
below.

Use explicit string encodings, preferably UTF-8. If not that, then
another encoding capable of handling Unicode.

Divide your objects into first-class things (those that have identity,
an independent life cycle, and can be shared by reference), and
everything else (none of those properties). Allowing an object to have
some of those properties but not all of them invariably leads to
problems.

Support access control at the finest possible level of detail. For
example, two users see different properties or relationships on the
same object, because they have different authorizations. If possible,
this should be more robust than simple role-based security, and
capable of handling the dissemination rules associated with
classification and control markings.

Design the system to have an (external) API not tied to the internal
data representation. For example, SQL is a horrible external API for
an app with a RDBMS back end, because other people's code depends on
your schema, and you will eventually be forbidden from changing that
schema for any reason, even to fix a bug.

## Use Cases

The most significant thing that this system provides is the ability to
track down why your data says what it does. The use cases below are
essentially different ways of using this.

An automated process is found to have a bug. This could be anything
from a data ingestion process (parsing text, e.g.) to a complicated
socio-economic link analysis algorithm. Keeping track of who/what made
every change allows you to find all changes made by the specific buggy
version(s) of the process. Keeping track of data provenance allows you
to find all the data which used the false data as a source of
information, transitively. Even if the false data is 20 steps removed
from a conclusion, the system can follow that chain of provenance.

A sensor is found to be faulty. This is essentially the same use case.

A user or other source is found to be malicious. Again, this is
essentially the same use case.

Conversely, if a decision maker wants to understand all the data that
went into the summary on their desk, the system can answer that as
well by following the data provenance backwards to its sources.
