---
layout: post
title:  "Smarter Honeypots: Using GenAI to customize"
date:   2024-12-07 22:14:15 +0000
categories: security honeypot GenAI
---
Honeypots are not new and lot of organizations use it as an early warning indicator of a potential compromise. You can build honeypots for any service you want, and there are VM images available which actually bundle multiple honeypot services together. 

While starting up generic honeypots are relatively easy, there is a bigger opportunity to customize these services in the new age of GenAI.

### Making it believable
A good, believable, honeypot could effectively keep a determined attacker busy long enough for detection to catch up. For this not only do you have to start up multiple honeypots across different ports, you also have to make sure its config files look believable for the organization its in. For example, if the organization is called "Blogofy Inc", you cannot have a generic httpd config file which talks about "ACME Corp". An even better honeypot is the one which understands the size of the company, the business its in, the architecture of the network and website, knows which part of the world its located in, and even knows who the primary Admins are.

Before the advent of GenAI, all of these customizations had to be done by hand. But with tools like Gemini, most of these can now be done on the fly by designing a prompt which tell the story. Gemini can do the rest.

### Examples
I have a server [blogofy.com](https://www.blogofy.com/) which hosts some of the javascript based games my kids created over the last few months. But what is not easily visible is that this server also hosts a Gemini based honeypot which will happily give you any file you are looking for. Its very hacky at the moment, but as a proof of concept this is decent enough. Here are some concrete examples.

* [https://www.blogofy.com/export/2024-financial-report.txt](https://www.blogofy.com/confidential/2024-financial-report.txt) -  /confidential//2024-financial-report.txt
* [https://www.blogofy.com/export/2024-financial-report.txt](https://www.blogofy.com/confidential/corporate-background.txt) -  /confidential/corporate-background.txt
* [https://www.blogofy.com/../../../etc/passwd](https://www.blogofy.com/../../../etc/passwd) - /etc/passwd
* [https://www.blogofy.com/../../../etc/shawdow](https://www.blogofy.com/../../../etc/shawdow) -  /etc/shadow
* [https://www.blogofy.com/../../home/raj/.profile](https://www.blogofy.com/../../home/raj/.profile) - ~/.profile
* [https://www.blogofy.com/../etc/sshd/sshd.conf](https://www.blogofy.com/../etc/sshd/sshd.conf) -  /etc/sshd/sshd.conf

