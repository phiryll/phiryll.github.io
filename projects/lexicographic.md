# Lexicographic Byte Order Encoding

Techniques for encoding basic data types, arbitrary precision integral
and floating point types, ISO 8601 date-time instants, and aggregates
(structures or lists) of those types in a way that preserves their
natural ordering when sorted in lexicographic unsigned byte order. The
primary purpose of this is to make it easier to use an [ordered
key-value
store](https://en.wikipedia.org/wiki/Ordered_Key-Value_Store), or a
binary trie.

Also, a technique for encoding
[WKT](https://en.wikipedia.org/wiki/Well-known_text) as a byte string
in a way allowing geospatial containment and intersection queries to
be implemented using byte string prefix searches. In a sense, this is
also a lexicographic ordering, except that A being a prefix of B
implies B is a spatial subset of A. This is essentially a binary
variant of [geohash](https://en.wikipedia.org/wiki/Geohash). Because
of the additional dependencies, geospatial support should either be
optional in this project, or a separate project entirely.
