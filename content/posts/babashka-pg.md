---
date:  '2024-11-10T19:40:45-07:00'
draft: true
title: 'Babashka + PostgreSQL'
---
Recently I learned about the [Babashka](https://babashka.org) which is bills itself as a "Fast native Clojure scripting runtime".  It has most of the benefits of Clojure, but has the fast startup time associated with the [GraalVM](https://www.graalvm.org), on which it is based.  While, it is not necessarily a Clojure replacement, it does make Clojure more accessible to everyday tasks one might do on the shell.  In fact, since I have discovered this tool, it is the first thing I reach for when I need to write a shell script.  

One of the things that comes up from time to time, is you need to write a small script that involves a database.  I would call this kind of script a "throw-away" script.  In times past, I would reach of something like Python, or maybe C# at work. 


```
#!/usr/bin/env bb

(require '[babashka.pods :as pods])
(pods/load-pod )
```
