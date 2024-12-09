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

### GenAI can help
Before the advent of GenAI, all of these customizations had to be done by hand. But with tools like Gemini, most of these can now be done on the fly by designing a prompt which tell the story. Gemini can do the rest. 

In the following examples which I've implemented on my test server, you will quickly notice a pattern coming through the files. All of these files were created using the same exact prompt which provided context to Gemini to understand how to subtly modify the responses to align them into something which looks like its coming from a real server. The prompts do not have to be very long, but you could choose to give it longer prompts to get it closer to reality. I think Gemini can take upto 2M tokens as input, which is a LOT.

#### Telling the story

Here is how my prompt starts
> You are a Honeypot running on an IT server (blogofy.com) managed by Dopey, the IT Administrator for Blogofy Toy Corporation, located at the North Pole. You are a renowned toy manufacturing company owned and operated by CEO and President, Santa Claus (also known as St. Nicholas). Your CTO is Grumpy. Currently, you are in peak season, rushing to manufacture and deliver toys by December 25th. Everyone is extremely busy and focused on meeting this deadline. Dopey is responsible for maintaining our network and systems, troubleshooting any issues, and ensuring smooth operations during this critical time. Remember, time is of the essence, and any disruptions can significantly impact our toy production and delivery schedule. Your goal is to make sure the honeypot is very very believable, such that attackers spend time trying to figure out whats going on and our detection modules are able to find them before they disrupt anything.

#### Examples
I have a server [blogofy.com](https://www.blogofy.com/) which hosts some of the javascript based games my kids created over the last few months. But what is not easily visible is that this server also hosts a Gemini based honeypot which will happily give you any file you are looking for. Its very hacky at the moment, but as a proof of concept this is decent enough. Here are some concrete examples.

* [https://www.blogofy.com/export/2024-financial-report.txt](https://www.blogofy.com/confidential/2024-financial-report.txt) -  /confidential/2024-financial-report.txt
* [https://www.blogofy.com/export/corporate-background.txt](https://www.blogofy.com/confidential/corporate-background.txt) -  /confidential/corporate-background.txt
* [https://www.blogofy.com/../../../etc/passwd](https://www.blogofy.com/../../../etc/passwd) - /etc/passwd
* [https://www.blogofy.com/../../../etc/shadow](https://www.blogofy.com/../../../etc/shadow) -  /etc/shadow
* [https://www.blogofy.com/../../home/raj/.profile](https://www.blogofy.com/../../home/raj/.profile) - ~/.profile
* [https://www.blogofy.com/../etc/sshd/sshd.conf](https://www.blogofy.com/../etc/sshd/sshd.conf) -  /etc/sshd/sshd.conf
* [https://www.blogofy.com/../etc/apache/httpd.conf](https://www.blogofy.com/../etc/apache/httpd.conf) -  /etc/apache/httpd.conf

### What are the probes looking for
Beyond just targetted attacks, for which this will do a good job, honeypots can also be an interesting tool to study what malware probes are looking for. The following log fragment was captured on Dec 8th. After the probe got a 200 on xleet.php (which I suspect is a malware by itself), it quickly came back to look for what else is on the system. You can see the entire list below to see that this probe already knew what tools have security holes (or are malware themselves) and was specifically looking for their presense.

None of this may have happened if the first request was not responded with a 200 :)

```
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:03 -0800] 10192092 "GET /xleet.php HTTP/1.1" 200 6815 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:14 -0800] 2261 "GET /autoload_classmap.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:14 -0800] 2484 "GET /classsmtps.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:15 -0800] 2564 "GET /cljntmcz.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:15 -0800] 2790 "GET /cloud.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:16 -0800] 2483 "GET /cnzcsfwm.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:16 -0800] 2191 "GET /colors.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:16 -0800] 2272 "GET /colour.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:17 -0800] 2267 "GET /conf_upload.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:17 -0800] 2245 "GET /config.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:18 -0800] 2697 "GET /contact_tpl.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:18 -0800] 2416 "GET /content.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:19 -0800] 2450 "GET /content.php888 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:19 -0800] 2505 "GET /contentloader1.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:19 -0800] 2264 "GET /cookie.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:20 -0800] 2442 "GET /cron.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:20 -0800] 2398 "GET /css.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:21 -0800] 2364 "GET /csv.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:21 -0800] 2468 "GET /curl.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:22 -0800] 2484 "GET /delpaths.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:22 -0800] 2242 "GET /depotcv.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:23 -0800] 2536 "GET /disagraeed.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:23 -0800] 2253 "GET /disagraeosc.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:23 -0800] 3283 "GET /disagraep.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:24 -0800] 2452 "GET /disagreed.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:24 -0800] 2550 "GET /disagrsod.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:25 -0800] 2731 "GET /dropdown.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:25 -0800] 2352 "GET /ds.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:26 -0800] 2554 "GET /dxc.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:26 -0800] 2723 "GET /e69ovfsr.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:26 -0800] 2512 "GET /eNtnKM.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:27 -0800] 2737 "GET /edit.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:27 -0800] 2410 "GET /embed.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:28 -0800] 2327 "GET /eq2hbpgs.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:28 -0800] 2291 "GET /error.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:29 -0800] 2339 "GET /essexec.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:29 -0800] 2536 "GET /ewywe1dg.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:30 -0800] 2414 "GET /exif.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:30 -0800] 2543 "GET /extractable-loader-head.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:30 -0800] 2360 "GET /f.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:31 -0800] 2271 "GET /f35.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:31 -0800] 2455 "GET /favicon.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:32 -0800] 2415 "GET /feed-rss2-queue.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:32 -0800] 2505 "GET /feeds.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:33 -0800] 2402 "GET /fi2.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:33 -0800] 2961 "GET /fied.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:33 -0800] 2090 "GET /files.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:34 -0800] 2321 "GET /flower.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:34 -0800] 2527 "GET /fm.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:35 -0800] 2493 "GET /fm2.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:35 -0800] 2473 "GET /fox.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:36 -0800] 2501 "GET /fucixwya.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:36 -0800] 2851 "GET /functions.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:37 -0800] 2217 "GET /fw.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:37 -0800] 2899 "GET /fxcexgle.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:37 -0800] 2229 "GET /gebase.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:38 -0800] 2574 "GET /gebase.php69 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:38 -0800] 2183 "GET /gecko-new.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:39 -0800] 2271 "GET /getid3-core.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:39 -0800] 2426 "GET /global.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:40 -0800] 2127 "GET /go.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:40 -0800] 2584 "GET /haiterus.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:41 -0800] 2666 "GET /headerg.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:41 -0800] 2625 "GET /hello.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:41 -0800] 2479 "GET /help.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:42 -0800] 2646 "GET /hkvkjguw.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:42 -0800] 2500 "GET /hoot.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:43 -0800] 2511 "GET /hyIPpxWDQ.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:43 -0800] 2084 "GET /iR7SzrsOUEP.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:44 -0800] 2190 "GET /inc.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:44 -0800] 2573 "GET /0.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:44 -0800] 2180 "GET /02.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:45 -0800] 2187 "GET /1.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:45 -0800] 2207 "GET /12.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:46 -0800] 2329 "GET /123.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:46 -0800] 2035 "GET /1index.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:47 -0800] 2409 "GET /1p.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:47 -0800] 2632 "GET /22.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:48 -0800] 2591 "GET /24.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:48 -0800] 2183 "GET /404.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:48 -0800] 2241 "GET /404.php123123 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:49 -0800] 2284 "GET /4price.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:49 -0800] 2275 "GET /5173e.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:50 -0800] 2737 "GET /83064.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:50 -0800] 2959 "GET /Alfa.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:51 -0800] 2588 "GET /Auth.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:51 -0800] 2658 "GET /BIBIL0DAY.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:51 -0800] 2136 "GET /BIBIL_0DAY.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:52 -0800] 2476 "GET /Casper.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:52 -0800] 2198 "GET /DxHhVcy2bmJ.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:53 -0800] 2492 "GET /GOD.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:53 -0800] 2169 "GET /IDhrIlrLb.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:54 -0800] 2168 "GET /Js.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:54 -0800] 2650 "GET /M1.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:55 -0800] 2134 "GET /MYK4TJEfFvO.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:55 -0800] 2377 "GET /NFXxUAA.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:55 -0800] 2182 "GET /NewFile.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:56 -0800] 2295 "GET /Njima.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:56 -0800] 2222 "GET /OK.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:57 -0800] 2231 "GET /OthioNDwMEK.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:57 -0800] 2903 "GET /aQzODIgoBr.php HTTP/1.1" 404 314 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:58 -0800] 2957 "GET /aaa.php HTTP/1.1" 404 436 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:59 -0800] 2624 "GET /ab1ux1ft.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:13:59 -0800] 2499 "GET /about.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:00 -0800] 4604 "GET /about.php525 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:00 -0800] 2798 "GET /about.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:00 -0800] 4437 "GET /access.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:01 -0800] 5036 "GET /add_actualites.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:01 -0800] 2466 "GET /addslashes.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:02 -0800] 2585 "GET /admin.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:02 -0800] 4600 "GET /admin.php1 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:03 -0800] 7542490 "GET /admin.php7 HTTP/1.1" 200 2820 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:11 -0800] 2674 "GET /ae.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:11 -0800] 4312 "GET /aksinet.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:12 -0800] 4195 "GET /al.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:12 -0800] 4164 "GET /aleXus.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:13 -0800] 3019 "GET /alfa-rex.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:13 -0800] 6181 "GET /alfa-rex.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:13 -0800] 4289 "GET /alfanew.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:14 -0800] 3741 "GET /alfanew.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:14 -0800] 2471 "GET /alumni_reg.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:15 -0800] 2463 "GET /amaxx.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:15 -0800] 2684 "GET /as.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:16 -0800] 4347 "GET /asasx.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:16 -0800] 2497 "GET /backup.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:17 -0800] 2645 "GET /bak.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:17 -0800] 2633 "GET /beence.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:17 -0800] 2673 "GET /bihnmimh.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:18 -0800] 2490 "GET /block-bindings.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:18 -0800] 2162 "GET /blog.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:19 -0800] 2342 "GET /blog.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:19 -0800] 2150 "GET /browse.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:20 -0800] 2470 "GET /bypass.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:20 -0800] 2409 "GET /bypass.php7 HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:21 -0800] 2139 "GET /c.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:21 -0800] 2192 "GET /c99.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:21 -0800] 2372 "GET /cJLGqzB.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:22 -0800] 2183 "GET /cache-base.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:22 -0800] 2175 "GET /cadastro-2.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:23 -0800] 2753 "GET /catuploadcsv.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:23 -0800] 2621 "GET /chosen.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:24 -0800] 2472 "GET /class-IXR-base64-view.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:24 -0800] 2424 "GET /class-IXR-encryption.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:24 -0800] 2376 "GET /class-php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:25 -0800] 2541 "GET /class-walker-category-dropdown-class.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:25 -0800] 2226 "GET /class-walker-comment-beta.php HTTP/1.1" 404 295 blogofy.com "-" "-"
blogofy.com:443 52.187.42.170 - - [08/Dec/2024:17:14:26 -0800] 2351 "GET /class-wp-cmd.php HTTP/1.1" 404 295 blogofy.com "-" "-"
```
