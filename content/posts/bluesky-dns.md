---
date: "2024-11-05T17:37:34-07:00"
draft: true
title: Configure Bluesky Handle Using Cloudflare DNS
category: technology
tags:
- dns
- social
---
One of the neat features of Bluesky is how that platform handles account verification.  With Twitter/X, the system relied on a verification checkmark that famous people would get access to, through some process.  When Elon Musk bought Twitter, those verification checks became useless, when they could be bought for a monthly fee.  Stories of obviously fake accounts, that had a verifiation check was a predictable result.

Bluesky on the other hand ties the account verification to domain ownership.  If you own a domain, then you can link it your Bluesky account using your username.  For me, my username is `zachmaillard.xyz`, which also happens to be the name of this website.  The downside to this approach, is that it involves registering a domain name, and adding a DNS record.  The plus side is, if some oligarch takes over Bluesky - they won't be able to sell verification because your username *is* the verification.

Below is a short guide on how to configure the DNS records using [Cloudflare](https://www.cloudflare.com).  There are a bunch of different DNS providers, and for the most part the process will be similar.

1. Obtain a domain name.  For the purposes of this document, it is assumed you purchased your domain name through Cloudflare.
2. In Bluesky, go to the [Settings](https://bsky.app/settings) page and choose `Change Handle` option.  (Will have an "@" icon.)
3. Click the "I have my own domain" link in the dialog.
4. Enter your domain name as the handle.  Choose the "DNS Panel" option.  Note the `Value:` field.  You will need this for step 5.  Leave the window open.
5. Open up the Cloudflare DNS Dashboard for your domain name.
6. Click `Add Record`
7. Enter the following values:
   a. Type: TXT
   b. Name: _atproto
   c. TTL: auto
   d. Content: Value from step 4.  Should start with something like `did=did:....`
8. Click Save
9. Go back to the Bluesky Settings and click "Verify DNS Record".  You may need to wait a few minutes between step 8 and 9.  
10. Success - you are new verified!  As long as you maintain ownership of that DNS name, you will always have a verified Bluesky account.

Official documentation [here](https://bsky.social/about/blog/4-28-2023-domain-handle-tutorial)