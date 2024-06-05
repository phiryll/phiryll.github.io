# Collision Calculator

A simple tool for calculations related to collisions among a set of
uniformly random sequences of fixed length ("words"). Calculate any
one of the following given the others:
- alphabet size: The number of possible character values.
- word length: The number of characters in a word.
- set size: The number of words in the set.
- collision probability: The probability that at least two items in
  the set will have the same value.

The probability calculation only needs the number of possible words
and the number of words in the set, but no one wants to calculate
2<sup>128</sup> for the number of possible words (in this case,
the number of possible 128-bit sequences).

This is the same problem as the birthday paradox, where the alphabet
has 365 characters and words have length 1.

The tool needs a simple UI that allows the user to set one of the 4
values as output, with the others editable. For example:
- What is the probability of a collision among 100 billion uniformly
  random 64-bit hashes?
- How big must a hash be to have less than a 0.01% chance of a
  collision among 100 billion uniformly random hashes?

This should be doable in javascript, which would allow the tool to be
hosted on GitHub Pages.
