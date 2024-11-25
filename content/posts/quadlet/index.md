---
date: '2024-11-24T08:45:11-07:00'
draft: true
title: 'Hosting Application on Raspberry Pi Using Quadlet'
category: technology
tags:
- container
- raspberrypi
- bot
---
I have been working on a bot for the social network [Bluesky](https://bsky.app).  My initial effort was non-interactive.  I used a Cloudflare Worker with a Cron trigger to post a [random road sign picture every day](http://github.com/zmaillard/rspodt).  I wanted to learn how to make the bot respond to "@mentions" with various commands.  This would mean having to host the bot in a slightly different way.  Instead of it being a serverless application, I would want to either listen to a stream of events, or poll on a fairly regular caedance to check for updates.  I will post details about the [bot](http://github.com/zmaillard/rsp.bot) at a later date.


The reqiurements for the bot are pretty minimal.  I need something that can be online, but it does not need to be publically accessible.  The bot polls for updates, but is not directly triggered via a web hook.  There is no need to poke holes through my firewall, or setup a seperate VLAN to host this on.  My first thought was to use a lightweight version of Kubernetes, like [k3s](http://k3s.io), or [k0s](http://k0sproject.io).  But I really am not intending to use this on more than one Node - so the whole overhead of Kubernetes seems excessive.  As I was researching this solution, the answer showed up as a post on [lobste.rs](https://lobste.rs/s/vajg5m/what_is_boring_technology_solution_for).


[Quadlet](https://docs.podman.io/en/latest/markdown/podman-systemd.unit.5.html) is part of the [Podman](https://podman.io) application (version 4.4 and above), that allows containers to run as [systemd](https://systemd.io) processes.  Podman bills itself as "...an open source container, pod, and container image management engine.", and is essentially a Docker alternative.

### Install
Note: Latest Raspberry Pi OS at the time of this post is based on Debian 12 (Bookworm).  The version of Podman installed via the stable distribution does not support Quadlet.  I could have build Podman from source or pinned Podman to the testing distribution for Bookworm.  Both have their drawbacks, and I do not have any compelling reason to use RaspberryPi OS in the first place.  The easier route was just to go with Ubuntu Server 24.10 for Raspberry Pi and be done with it.  There is nothing special that needs to be configured with the OS installation.  To install Podman on Ubuntu Server run:

```
sudo apt-get install podman
```

Again, this will not work with versions of Podman previous to 4.4.  At the time of this writing, the version available in Ubuntu Server 24.10 is 5.0.3.

### Create Service File
For this application I am going to host my container files in my user directory
```
mkdir -p ~/.config/containers/systemd
```

Within that directory, I create the file *rspbot.container*:
```
#File rspbot.container
[Unit]
Description=BlueSky Roadsign Pictures Bot

[Container]
ContainerName=rsp.bot

# Docker Image
Image=ghcr.io/zmaillard/rsp.bot:v1.1.0

# Environment Variables
# Currently Secrets Are In Plain Text
Environment=BSKY_USERNAME=<bskyusername>
Environment=BSKY_PASSWORD=<bskypassword>
Environment=AWS_ACCESS_KEY_ID=<aws access key id>
Environment=AWS_SECRET_ACCESS_KEY=<aws secret id>
Environment=AWS_REGION=<aws region>
Environment=AWS_ENDPOINT_URL=<aws endpont url>
Environment=AUTO_POST_CRON_SCHEDULE="0 0 * * *"

[Install]
#Start by default on boot
WantedBy=multi-user.target default.target
```


### Authenticate To GitHub Packages
The *rsp.bot* is hosted in GitHub Packages, which requires a token to pull packages.  In order for *podman* to be able to load the images, you will first need to login to GitHub Packages.  This step is only necessary if your container registry reqires credentials.

The **--authfile=~/.config/containers/auth.json** flag persists the authentication information, between system reboots.

```
podman login --authfile ~/.config/containers/auth.json ghcr.io
```

### Create Service
Now we are ready to install the service.  First, we need refresh the services available on the system.  The **--user** flag will cause the *systemctl* tool to look in the user directory.

```
systemctl --user daemon-reload`
```

Next, start the service.  The service name will be the name of the container file, with the *.service* instead of *.container*.  So in this example, my file is called *rspbot.container*, and my service is called *rspbot.service*.

```
systemctl --user start rspbot.service
```

This command may take some time to complete, as it will need to pull down the new Docker image from the registry.  Starting a service with an image that already exists on the system is much faster.  After the command completes, check the status of the service to verify it started successfully.  Any startup errors, or container crashes will be visible with this command.

```
systemctl --user status rspbot
```

### Updating Image
To update the Docker image, or to make changes to environment variables, simply edit the container file and restart the service with the command:

```
systemctl --user restart rspbot
```

### References
- [Quadlet: Running Podman containers under systemd](https://mo8it.com/blog/quadlet/)
- [Multi Container Application With Podman Quadlet](https://www.redhat.com/en/blog/multi-container-application-podman-quadlet)
