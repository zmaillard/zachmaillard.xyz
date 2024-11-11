---
date: "2024-11-05T17:38:02-07:00"
draft: true
title: 1Password Developer Tools
category: technology
tags:
- security
- ssh
---
[1Password](https://1password.com) is a password manager, that in addition to normal management functions, has some nice features that are geared towards the development workflow.  Two of the features that are worth calling out are SSH Agent, and the 1Password `op` CLI.

### SSH Agent
One of these features is the ability to use 1Password as an SSH agent.  This greatly simplfies management of SSH ssh keys.  Not only does this work in macOS and Windows, it also integrates with commands run in Windows Subsystem for Linux (WSL).  Follow [these instructions](https://developer.1password.com/docs/ssh/agent) for configuring the agent.  I also have my agent settings as part of my [dotfiles](https://github.com/zmaillard/dotfiles/blob/main/home/dot_ssh/config.tmpl), so when I setup a new computer I do not need to remember the correct agent configuration settings.

In order for the SSH agent to recognize the keys, they need to be stored as a type of `SSH Key`.The SSH key category has some useful features like storing both the public and private key, as well as the fingerprint of the key.  There are also features built in to export keys to the filesystem.  Once the agent is setup, whenever a request for an SSH key matching the fingerprint occurs, a 1Password prompt will appear asking for your biometric information or password.  This can be an operation like logging into a new server using `ssh`, or accessing a repository in Git.  Existing keys can be imported into 1Password, or new ones can be generated from within the tool itself.
{{< diagram width="400" src="images/1password-new-ssh-key.png" caption="New SSH Key"  >}}
{{< diagram width="400" src="images/1password-existing-ssh-key.png" caption="Existing SSH Key" >}}

This prevents needing to have to ensure to copy private keys from workstation to workstation.  Also this is useful when accessing Git from multiple workstations.  There is no need to configure a new SSH key for each workstation.  {{< diagram width="400" src="images/1password-git-prompt.png"  >}}


### OP CLI
The [1Password CLI](https://developer.1password.com/docs/cli/), `op`, allows you to access your 1Password vaults from the command line.  Depending on which operating system you are using, it may even integrate with the 1Password desktop application.  One of the features I use the CLI most for, is to integrate within my [dotfiles](https://github.com/zmaillard/dotfiles).  I can inject vault references into my dotfiles and configuration scripts.  Then went I apply the settings on my computer, I am prompted for a password on my 1Password desktop application, or if I am running within WSL, I am prompted for my password on the commandline itself.  If I am using the biometric features, I can use my fingerprint, or face to login. {{< diagram width="400" src="images/1password-cli.png"  >}}


