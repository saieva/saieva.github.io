---
layout: post
title:  "Reimagining the Implementation of TeX (and the Luxury of Modern Programming Methods)"
date:   2015-01-24 06:00:00 -0400
categories: Programming
---
Glenn Vanderburg, Engineering Director for Architecture at LivingSocial
([@glv][GLVTwitterURL]; [https://vanderburg.org][GLVWebsiteURL]), has a personal project underway to
implement TeX’s algorithms in the Clojure programming language. His work was recently described in an
article [published on InfoQ][InfoQArticleURL]. Glenn also presented his work at the Clojure/conj
conference last year in 2014, his excellent talk is [posted on YouTube][ClojureTVVideoURL].

The InfoQ article and YouTube presentation provide a high-level summary of the state of technology and
software development capabilities available in the early 1980’s which influenced (really restricted and
confined) the creativity and results of a programmer at the time. By implementing TeX in [Clojure][ClojureWebsiteURL],
a functional programming language with capabilities for multi-threaded processing, Glenn applies modern methods,
and makes several now-and-then observations along the way, to the implementation of a large-scale programming
masterwork created by Professor Knuth more than 30 years ago.

> “The ways we can program today are luxuries made possible by decades of small advances.”

Glenn also describes the potential benefits for reuse that would be available with a modern implementation
giving several examples of how Web publishing and eBook readers might take advantage of using TeX’s excellent
typesetting algorithms creating beautiful page layouts and more natural reading experiences.

**References**

- [Literate Programming][LiterateProgrammingURL] by Donald E. Knuth
- [A Commentary on the Unix Operating System][CommentaryOnUnixURL] by John Lions
- [Computers & Typesetting][ComputersAndTypesettingURL] by Donald E. Knuth
- [The Art of Computer Programming][TheArtOfComputerProgrammingURL] by Donald E. Knuth

[GLVTwitterURL]: https://twitter.com/glv
[GLVWebSiteURL]: https://vanderburg.org/
[InfoQArticleURL]: https://www.infoq.com/news/2015/01/implementing-tex-in-clojure
[ClojureTVVideoURL]: https://www.youtube.com/watch?v=824yVKUPFjU
[ClojureWebsiteURL]: https://clojure.org/
[LiterateProgrammingURL]: http://www.literateprogramming.com/knuthweb.pdf
[CommentaryOnUnixURL]: http://www.lemis.com/grog/Documentation/Lions/
[ComputersAndTypesettingURL]: https://www.amazon.com/Computers-Typesetting-Volumes-A-E-Boxed/dp/0201734168
[TheArtOfComputerProgrammingURL]: https://www.amazon.com/Computer-Programming-Volumes-1-4A-Boxed/dp/0321751043

