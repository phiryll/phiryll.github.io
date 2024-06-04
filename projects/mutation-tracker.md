# Mutation Tracker

The use case for this project is very niche, and requires a
programming language that allows overriding operators on the fly,
including assignment. I started prototyping this in python, but had to
put the project on the back burner and never got back to it. I'm going
to describe the use case, which should pretty much explain what the
project would need to do.

There is an algorithm to price items (hereafter, just the
"algorithm"). Each item is unique, because they're used products with
tons of options, differing wear and tear, etc. The algorithm has
become spaghetti over time, and all the original authors have left.
There are thousands of edge cases in the code that don't make sense to
anyone currently employed there, and don't have documentation or a
helpful git commit message. It was standard practice in the past to
add an edge case to the code whenever a stakeholder wasn't happy with
a price. The algorithm uses data from third parties, data that can
change every time you request it (because the third parties respond to
market changes).

Just about every day, someone interrupts the engineering team
demanding to be told why a price is what it is. Some engineer then has
to go get that version of the code, find all the third party requests
and responses, and trace through it by hand. This takes hours.

This process needs to be self-service for non-technical stakeholders.
There's also the problem of how to present the data in a way that
makes sense, but this project is only for capturing the data.
Essentially, for a subset of variables, it needs to track every
arithmetic operation, assignment, request/response, or other mutating
operation. This should include code in functions that are unaware of
the tracking.

A little ambitious, but the time and aggravation saved would be huge
if it could be made to work.
