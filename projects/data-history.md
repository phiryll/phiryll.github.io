# Data History and Provenance

This doc describes a database I've wanted to create for a long time.
In a sound bite, think "version control for data." Some use cases are
then presented demonstrating the kinds of problems it could solve.
Building this would be non-trivial, and might not even be possible at
scale. This doc merely describes the end goal, without any details of
how to get there.

## All History is Maintained

Every data change is recorded separately. Historical data can be
retrieved from any point in the past, and time-based queries and
analyses are supported. When data is deleted, it is only marked as
having been deleted. In addition to the normal metadata, every change
must include who did it and a message describing the reason for the
change. That requirement also applies to any automated processes, not
just end users. A side effect of this is that all actors must have a
representation in the system. The identity of an automated process
must include which part of the process (if it does multiple things
and/or uses multiple algorithms) and its version.

All queries are at a specific point in time. A side effect of this is
that all read data is effectively immutable, greatly reducing the
overhead for managing concurrent reads.

The database state can be branched, and those branches can be private
to users. In other words, a user can run a number of what-if scenarios
on the data, or even a what-if on top of a what-if, without impacting
the authoritative database.

## Relationships are Represented

Data can have relationships to other data, and those relationships are
understood by the system. Relationships do not need to have a valid,
existing target. Just like in the world wide web, dead links are
allowed. This reduces the resources needed for maintaining
consistency.

Relationships are not constrained in multiplicity. The system should
never have to check whether some other relationship exists when adding
a new relationship.

## Uncertainty is Represented

It must be possible to represent not just actual facts, but also
assertions. In this usage, an assertion is a relationship between the
data doing the asserting and the data being asserted, including the
uncertainty as measured by the asserter. Uncertainty is not a
probability, but a certainty factor (the rules for combining them are
different). For example, "Alice is 70% certain that we will win the
XYZ contract", or "Algorithm ABC version 3.5.2 is 93.45% certain that
stock XYZ will fall 10% in the next week."

Contradictory information can be represented. Two algorithms or users
can arrive at conflicting conclusions.

## Data has Provenance

Where data comes from is also in the database, with a relationship
between the data and its source. This is not typically the same thing
as the user who changed the data, although it might be. The source
might be a newspaper or a sensor. Data might have multiple sources (a
conclusion based upon many inputs, e.g.).

## General Principles

Some of these are not required to support the above features, but are
just generally good ideas.

Support a binary data type allowing any possible value (e.g., not
null-terminated).

Use an explicit string encoding capable of handling Unicode.

Store all externally supplied data in its raw form, in the database.
All data sanitization, normalization, etc. is something you do
afterwards, and should be treated no differently than producing any
other kind of derived data. Fundamentally, normalizing line endings or
removing bad characters is no different than entity extraction. Some
data goes in and new data computed from that comes out. One reason to
do this is to avoid dropping data on the floor because it has failed
some sanity check. Another reason will be covered by the use cases
below.

Divide your entities into first-class things (those that have
identity, an independent life cycle, and can be shared by reference),
and everything else (none of those properties).

Support access control at the finest possible level of detail. For
example, two users see different properties or relationships on the
same object, because they have different authorizations. This probably
isn't feasible at the level of properties, but there are use cases for
it.

## Use Cases

The most significant thing that this system provides is the ability to
track down why your data says what it does. The use cases below are
essentially different ways of using this.

An automated process is found to have a bug. This could be anything
from a data ingestion process (parsing text, e.g.) to a complicated
socio-economic link analysis algorithm. Keeping track of who or what
made every change allows you to find all changes made by the specific
buggy version(s) of the process. Keeping track of data provenance
allows you to find all the data which used the false data as a source
of information, transitively. Even if the false data is 20 steps
removed from a conclusion, the system can follow that chain of
provenance.

A sensor is found to be faulty. This is essentially the same use case.

A user or other source is found to be malicious. Again, this is
essentially the same use case.

Conversely, if a decision maker wants to understand all the data that
went into the summary on their desk, the system can answer that as
well by following the data provenance backwards to its sources.
