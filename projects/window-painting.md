# Painting Windows Front-to-Back

This problem (and my solution) were for a tiny embedded system many
years ago. Modern hardware and windowing systems don't need this kind
of optimization any more.

This project will probably end up being just a description of the
algorithm without any code, or perhaps a reference implementation. Any
real implementation should probably be in assembly language.

_Entering the Wayback Machine_

Most computer windowing systems paint windows back-to-front, because
it is simple and correct. However, that technique also results in
writing multiple times to the same pixels, wherever there is overlap.
Writing to video memory should be avoided when possible because of the
overhead involved in moving data between RAM, the CPU, and video RAM.

It is possible to efficiently paint windows front-to-back, so that
every pixel is only written once. This technique works best when
window content is stored in off-screen video RAM, so that fast bit
blit operations can be performed entirely using video hardware. The
algorithm is very amenable to expression in assembly language, because
that allows you to test the sign and carry bits directly without
performing a second operation.
