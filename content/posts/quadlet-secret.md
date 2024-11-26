---
date: '2024-11-25T20:18:16-07:00'
title: 'Storing Secrets in Podman Quadlet'
category: technology
tags:
- container
- security
- bot
---
My [earlier post on Podman Quadlet]({{< ref "/posts/quadlet.md" >}}) showed how to install [Podman Quadlet](https://podman.io), but included secrets as plain text in the container file.  This isn't exactly the best practice for handling secrets.  Podman has a built in secrets management utility that integrates with the Quadlet containers.

**Note:** The steps below assume that the secret has been loaded into an enviornment variable.

### Create Secrets
Use the [Podman secret](https://docs.podman.io/en/latest/markdown/podman-secret.1.html) command to create a named secret.  The secret is passed as *STDIN* and given a name that will be used to access the secret later.  The syntax looks like:

*echo -n "${YOUR_SECRET_ENV_VARIABLE}" | podman secret create your_secret_name -*

The above example takes the value of the environment variable *YOUR_SECRET_ENV_VARIABLE* and creates a Podman secret called *your_secret_name*

For my *rsp.bot* - the Podman secret command looks like:

```
echo -n "${BSKY_PASSWORD}" | podman secret create bluesky-password-container -
```

### Use Secrets
In order to use the reference in your container file, you will need to map the Podman secret name with the environment variable that your container is expecting.
 
Add a *Secret* line for each instance of a secret.  The example below shows how to map the *bluesky-password-container* secret to the *BSKY_PASSWORD* environment variable.
 
```
Secret=bluesky-password-container,type=env,target=BSKY_PASSWORD
``` 

My updated container file looks like:
```
[Unit]
Description=BlueSky Roadsign Pictures Bot

[Container]
ContainerName=rsp.bot
Image=ghcr.io/zmaillard/rsp.bot:v1.1.0
Environment=BSKY_USERNAME=roadsign.pictures
Environment=AWS_REGION=auto
Environment=AWS_ENDPOINT_URL=https://328471a82e8e2e18d0bcc66d0b048db7.r2.cloudflarestorage.com
Environment=AUTO_POST_CRON_SCHEDULE="0 0 * * *"
Secret=bluesky-password-container,type=env,target=BSKY_PASSWORD
Secret=aws-access-key-container,type=env,target=AWS_ACCESS_KEY_ID
Secret=aws-secret-key-container,type=env,target=AWS_SECRET_ACCESS_KEY

[Install]
#Start by default on boot
WantedBy=multi-user.target default.target
```
### References
- [Storing sensitive data using Podman secrets: Which method should you use?](https://www.redhat.com/en/blog/podman-kubernetes-secrets)
- [Multi Container Application With Podman Quadlet](https://www.redhat.com/en/blog/multi-container-application-podman-quadlet)
