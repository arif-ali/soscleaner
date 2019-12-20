.. sectionauthor:: Jamie Duncan <jamie.e.duncan@gmail.com>

==============================
Proposals and Roadmap
==============================

Python 3
---------

As comfortable as Python 2 is, it is EOL in early 2020. Python 0.4.x will maintain its Python 2 codebase. Python 0.5.0 will be released as early in 2020 as possible. This release will be 100% Python 3. 

The current plan is maintain these as separate branches. There was talk of using tools like six. But the changes that come from moving to Py 3 involve a major refactoring of the network obfuscation module. Maintaining that in the same file as the Py2 code is seen as too cumbersome right now.

API
-----

Currently each `soscleaner` run is an atomic process. If you run soscleaner against the same dataset twice, there's no assurance the same obfuscations will occur. It's probable, but not guaranteed. Additionally, if one dataset is obfuscated, there's no way to add to the dataset at a later time and have the same obfuscation database be used. This use case has been identified by multiple users. 

In the 0.5 branch, we're planning work to add this capability. The most obvious path would be to add the capability to store the obfuscation database in something more useful than individual CSV files (a non-SQL database springs to mind). This would allow for querying and appending to existing datasets. 

It would also allow to more easily front `soscleaner` workflows with a RESTful API. This feature would allow for a centralized, queryable database of obfuscated data for an enterprise.

Interacting with Kubernetes
----------------------------

If `soscleaner` was able to be accessed on-demand via an API, interacting with Kubernetes becomes a possibility. An initial thought is to extend `kubectl` with a helper script that would take the output of a supplied command and obfuscate it programmatically. For example:

```
$ kubectl obfuscate get pods -n default
```

The helper script would:

* get the output of `kubectl get pods -n default`
* pass it to `soscleaner` through its API. 
* return the obfuscated `kubectl` output.

There's still some work to do here around session data, and how to know if it's a new obfuscation session or to append to an existing database. A sane default could be to obfuscate everything in a given namespace using the same obfuscation database. This is easily inferred from most `kubectl` commands or the kubernetes context.

The primary value for this enhancement would be in allowing output of `kubectl` (and variants like `oc`) to be consistently obfuscated during troubleshooting with outside parties. Currently there's not an automated tool to obfuscate command output from `kubectl` and other CLI based tools that provide interactive output to the user. Additionally, these tools are increasingly common. This would provide a good blueprint for `soscleaner` to fill this need. 

