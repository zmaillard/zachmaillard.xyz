---
date: "2024-11-05T17:38:02-07:00"
draft: true
title: Using 1Password as an SSH Agent
category: technology
tags:
- security
- ssh
---
1Password is a password manager, that in addition to normal management functions, has some nice features that are geared towards the development workflow.  One of these features is the ability to use 1Password as an SSH agent.  This greatly simplfies management of SSH ssh keys.  Not only does this work in macOS and Windows, it also integrates with commands run in Windows Subsystem for Linux (WSL).

To enable the SSH agent:
1. Open up the `Settings` in 1Password and click the `Developer` menu.
2. Check the `Use SSH Agent` checkbox.

https://developer.1password.com/docs/ssh/agent
