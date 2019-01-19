---
title: "IN/Clojure 2019"
date: 2019-01-19T23:26:56+05:30
draft: false
toc: false
images:
tags: 
  - clojure
---

Recently, I attended the [((((IN/Clojure))))](https://inclojure.org/) workshop which took place in Bangalore. The workshop was designed for two levels, a beginner & an intermediate one. Since, I had some previous knowledge around clojure, I signed up for the intermediate workshop. 

The day of the workshop, we were expected to be ready with our own crazy setup, as long as it worked. Since, we are on the this, one could have a working clojure environment setup in _Vim, Visual Studio Code, Sublime Text, Emacs, Spacemacs, IntelliJ (with Cursive)_ and what not. I now use Spacemacs having tried my hands on plain vanilla Emacs with certain tweaks [(dotfile for Emacs)](https://github.com/darkdefender27/dotemacs).

Every workshop/conference one has attended knows this well - it does not start without any wifi hassles. After overcoming those, we kickstarted with solving a basic problem of reading and processing data in a _.csv_ file and then dumping the processed data in a database; and finally exposing a _GET_ endpoint which reads & omits out these records.

I got to replenish my stale knowledge on _Lazy Seq, Java Interop, REPL_ interaction and of course _Spacemacs_ shortcuts :p. The workshop further covered  concepts like Concurrency in Clojure, the motivation behind Clojure having immutable data structures, _Futures, Refs, Atoms, STMs,_ a cool `Rock-Paper-Scissor` exercise demonstrating the above mentioned concepts.

The final segment of the workshop dealt with `core-async`. The same pattern was followed here as well, concepts and then exercises which worked-up those concepts. During this segment, I learnt how, using _core-async_, one can solve or scale the earlier problem of reading and processing data from a source, when your source is emitting out data in realtime (Streams!). To end the workshop, we solved the exercise, using _core-async's_ channels; the exercise already had a base streaming solution in place with appropriate expressions to be filled in the blanks.

The content of the workshop had a good amount of variation, it went on from basics to intermediate to some advanced clojure at an appropriate pace. A well documented workshop material is made available on [Github](https://github.com/inclojure-org/intermediate-clojure-workshop/). So, just clone your way into the world of Fun Fun Functions!