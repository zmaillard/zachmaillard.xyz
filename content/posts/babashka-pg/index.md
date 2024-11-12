---
date:  '2024-11-10T19:40:45-07:00'
title: 'Babashka + PostgreSQL'
category: technology
tags:
- clojure
- scripting
- database
- babashka
---
Recently I learned about the [Babashka](https://babashka.org) which is bills itself as a "Fast native Clojure scripting runtime".  It has most of the benefits of Clojure, but has the fast startup time associated with the [GraalVM](https://www.graalvm.org), on which it is based.  While, it is not necessarily a Clojure replacement, it does make Clojure more accessible to everyday tasks one might do on the shell.  In fact, since I have discovered this tool, it is the first thing I reach for when I need to write a shell script.  

One of the things that comes up from time to time, is you need to write a small script that involves a database.  I would call this kind of script a "throw-away" script.  In times past, I would reach of something like Python, or maybe C# at work.  Babashka however, comes pre-loaded with a [bunch of libraries](https://book.babashka.org/#libraries) out of the box.  It also supports loading additional programs called [Pods](https://github.com/babashka/pods).  Pods can be used as Clojure libraries, once they have been loaded into the code.  A list of [Pods are here](https://github.com/babashka/babashka/blob/master/doc/projects.md#pods).

Normally, pulling in a remote resource from a registry would involve some extra setup work - like running `pip` for Python, or `NuGet` for a .NET project.  In the case of a Pod, Babashka will automatically pull down the correct Pod from the registry, if it is not already loaded on the system.  Below is a simple Clojure script that prints out the list of tables in the database:


```clojure
#!/usr/bin/env bb


; Load PostgreSQL POD
(require '[babashka.pods :as pods]) 
(pods/load-pod 'org.babashka/postgresql "0.1.2")

; Now that Pod is available - require like a regular
; Clojure namespace
(require '[pod.babashka.postgresql :as pg]
         '[cheshire.core :as json]) ;Json Support

; Assuming database parameters are stored as environment variables
; build up conection information
(def db {:dbtype "postgresql"
         :host (System/getenv "DB_HOST")
         :dbname (System/getenv "DB_NAME")
         :user (System/getenv "DB_USER")
         :password (System/getenv "DB_PASSWORD")
         :port (System/getenv "DB_PORT")})

; Function to list tables in database
(defn print-tables
  []
  (pg/execute! db ["SELECT * FROM pg_catalog.pg_tables"]))


; Parse output as json and print tables to console
(println (json/generate-string(print-tables)))
```

Because I have specified the `bb` interpreter for the script, I can simply run the script like: `./db.clj`.   To show that it is just like a normal shell script, I can pass the Json output to `jq` to for further handling. 

`./db.clj | jq`

**Ouput**
{{< diagram src="images/output.png" width="700" >}}

