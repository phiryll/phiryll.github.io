# Painting Windows Front-to-Back

I should preface this one - the problem (and my solution) were in
1993. I hope modern hardware and windowing systems don't need this
kind of optimization any more.

Most computer windowing systems paint windows back-to-front, because
it is simple and correct. However, that technique also results in
writing multiple times to the same pixels, wherever there is overlap.
Writing to video memory should be avoided when possible because of the
overhead involved in moving data between RAM, the CPU, and video RAM.

It is possible to efficiently paint windows front-to-back, so that
every pixel is only written once. This technique works best when
window content is stored in off-screen video RAM, so that fast bit
blit operations can be performed entirely using video hardware. The
algorithm is very amenable to expression in assembly language.
