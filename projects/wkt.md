# WKT Encoding

Encode [WKT](https://en.wikipedia.org/wiki/Well-known_text) in a way
allowing geospatial containment and intersection queries to be
implemented using byte string prefix searches. If A is a prefix of B,
then B is a spatial subset of A. This is essentially a binary variant
of [geohash](https://en.wikipedia.org/wiki/Geohash).
[This](https://pkg.go.dev/github.com/go-spatial/geom) might be an
option for a library to use for this project. This could be used with
an [ordered key-value
store](https://en.wikipedia.org/wiki/Ordered_Key-Value_Store)

The gist is that a polygon is subdivided into a set of rectangles
which are then encoded, likely a very large set of rectangles. The
encoding is tunable to trade space for precision.

The mechanism isn't specific to WKT or geospatial contexts. The only
requirements are that the space has a Cartesian coordinate system and
is bounded by a finite rectangle in that coordinate system.

Pros:
* fast containment searches, unsure about intersection performance
* can be used to cull a query result set before using a more precise
  test
* can be extended to three or more dimensions in principle
* can take the true shape of the earth into account, although that may
  be more trouble than it's worth

Cons:
* a point is encoded as a very small rectangle
* a line is encoded as many very small rectangles
* the encoding is verbose, lossy, imprecise, and one-way
