# A Recursive Variant of Bresenham's Line Algorithm

This project won't actually have anything that draw lines, but
Bresenham's algorithm has inspired it.

Bresenham's line algorithm is a well-known algorithm for painting
unaliased lines on a computer screen using only integer arithmetic.
Bresenham's run-slice algorithm is an improvement of the original, and
it can be extended to a recursive algorithm.

Once written, it becomes obvious that the recursive Bresenham's line
algorithm and Euclid's algorithm (for computing the greatest common
divisor) are essentially the same, except the line algorithm does
something with the intermediate results. Successive Fibonacci numbers
are the worst case input for Euclid's algorithm, which directly
corresponds to how non-repetitive the sequence of steps of a drawn
line would be.

The algorithm (any variant) can also be shown to solve a more general
problem of evenly distributing items among a sequence of bins, and can
be applied to (among other things) image scaling.
