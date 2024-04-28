---
layout: post
title:  "Anyone can get hacked"
date:   2024-03-24 03:53:19 +0000
categories: security 0trust  
---
# Anyone can get hacked

A long time ago (before Chrome/Chromebooks) I used to own a cutting-edge Dell Windows laptop (with all the bells and whistles), with all the monthly security patches, the best antivirus and a good software firewall. I even had a SANS GIAC security certification, so knew a bit about security.
I still got hacked. Not once, but twice in a single month.

## The Mystery
The first time I got hacked, I was browsing news websites with my morning coffee. I knew something happened to my device by the screen flicker I saw (as if some process started and then closed the window). I immediately stopped what I was doing and scanned it using the Antivirus. It didn't find anything, but I didn't want to take risks and rebuilt the device anyway. Please note that rebuilding a Windows laptop from scratch is a fun week-long project :)
The second time it happened, it felt like Deja-vu. Interestingly I was on the same exact Indian news website as the previous time. This time I decided to clone the disk (made a read-only copy for forensics) and spent the next week looking for any evidence of the hack.
## The Evidence
After few hours of forensics, I found out that two things happened before I closed my laptop 
* I was served a Java applet as part of an advertisement 
* And that downloaded and installed something else (windows executable) on my device.
In an order to recreate this issue, I tried visiting that news website from a Virtual machine. I hoped to learn more about how the payload is executing, but I couldn't get the advertisement to give me the java applet. I suspected its because it was programmed to show that advertisement at very low frequency to specifically avoid people like me from dissecting it.

[Java applets](https://www.java.com/en/download/help/enable_browser.html) were very popular at that time. They were enabled by default on all browsers and allowed compiled java code to be downloaded and executed without any user intervention in a secure+safe way inside a sandbox within your browser. But based on the evidence I had, it was clear it had managed to escape the sandbox some how.
And a few days later the news did come out that there was a 0-day Java bug going around and a patch was made available. 
The funny part is that, I was never a big fan of Java applets and after this incident I did turn it off completely to avoid a repeat. I wish it was off by default.

## The Education
While that was a very frustrating month for me, I did learn few important lessons which can never be taught using textbooks :)
* "Bells and whistles" won't protect you.
* Running the latest OS or having all security patches is never enough
* As you add more components/features on your device, you will create more attack surfaces waiting to be exploited. [ I had no use for Java Applets and should have disabled it instead of assuming that it was secure]
* Rebuilding a Windows laptop was a huge pain. [ Those were the days of installing software from CDs ]
* Backing up personal data regularly was important and not easy
* I was very lucky that I knew the moment I was compromised. [Others may not be as lucky]
* And I was lucky that I knew what to do after compromise. [Others may not know].

## You can control the outcome
Everyone will get hacked at some point in some way. But you have two things in your control
* You can control how easy you want to make it for your attacker
* And you can control how you respond after you get compromised. 
The 100K users mentioned in these reports below got hacked and that their credentials are now up for sale. I suspect some of them could have avoided it if they had reduced their attack surface area.

[Over 225000 compromized ChatGPT clients](https://thehackernews.com/2024/03/over-225000-compromised-chatgpt.html)
[Over 100000 stolen ChatGPT accounts](https://thehackernews.com/2023/06/over-100000-stolen-chatgpt-account.html)

## Chromebooks
I have been fascinated with Chromebooks since they came out. But its my past experience with malware which makes me still use it as my default laptop today. 
Here are some of the noteworthy security features you should be proud of when you use a Chromebook. 
* This device was built from the ground-up with security in mind. 
* It has a completely silent auto-update system which patches 0-day bugs automatically - you never have to worry about it.
* All updates are from a single provider - you don't have to worry about auto-updates for each of the separate components installed.
* The core operating system is on a read-only disk. Which means that even if the device gets compromised the operating system cannot be modified.
* The device intentionally limits what extra features are shipped with it to reduce the attack surface.
* Its easy to switch between devices, because all data is in the cloud anyway
* Its next to impossible to execute code inside a Chromebook outside of the browser.
Folks often ask me why I get so excited when I hear Chromebook. I hope this post addresses that question :)
