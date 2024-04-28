---
layout: post
title:  "The misconceptions around VPN"
date:   2024-02-25 21:53:19 +0000
categories: vpn security
---
# The misconceptions around VPN
There is a misconception that VPNs have magical powers to protect you. Read on to understand what VPNs actually do, what they don't tell you and the risks you may be taking when you use them.

## What are VPNs good for
One of the most important things VPNs can do is mask your IP address. To do this VPN providers have servers in different parts of the world and allow you to tunnel your traffic over those servers. A good VPN client can not only route your HTTP/HTTPS traffic, but all traffic from your device including DNS. This makes VPN providers different from Proxy servers because the latter doesn't handle DNS traffic.

Masking IP Addresses have some interesting use cases. One of the most common use case is to hide what services you are using from your service provider. And it works with your local Starbucks cafe's network, the network at the Airport and AT&T/Comcast.

While most of the traffic today already defaults to HTTPS, which limits how much a service provider can snoop in, HTTPS by itself cannot hide the end point the user is using.

Masking IP addresses allows some users to get access to services which may not be possible. For example Netflix limits what content is available in different parts of the world. If I were on vacation and want to watch programs only in US, a VPN using a server in US would make that possible

This also allows folks to access services which may be banned in certain parts of the world.  For example, Chinese citizens who may have challenges using YouTube, may have better luck with a VPN service.

## What they don’t tell you
* The biggest fact the VPN providers fail to tell its users is that most of the web traffic is already using HTTPS which cryptographically secures all communication between the client and the server.  Last year [Chrome blogged](https://blog.chromium.org/2023/08/towards-https-by-default.html) about the fact that over 90% of the traffic is already in HTTPS. In fact most modern browsers, including Chrome, will show you a warning today when a website doesn't use HTTPS. Additionally, Chrome now allows you to completely disable access to HTTP sites if you choose to do so.
* Most of your devices already have many advanced security features which may not yet be turned on. Before you start trusting a third-party service provider, you should explore all the capabilities you already have. 

## The risks
A VPN cannot magically protect a user from a harmful service, and neither can it guarantee anonymization. Let me explain why:

* **Cookies tracking is still there:** As long as you are using your regular browser, with all of its cookies in it, your service providers can very easily track who is using the service. It's possible that they may not be able to tell where you are, because your IP is masked, but it's not impossible. 
* **GeoLocation APIs:**  If your browser supports [GeoLocation APIs](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API) and you have enabled it for the websites, then the service provider could still get precise location from your browser regardless of your VPN. Don’t assume that you are invincible.
* **Trusting VPN client on your device:** A VPN service by itself cannot block ads or protect you against malware without installing something on your device. Remember that most web transactions now happen on HTTPS, which means even the VPN service providers cannot see what you are doing. To be able to block Malware or remove Ads, the service would have to do deep packet inspection, and have the ability to modify the contents. This comes at great risk, and you shouldn't trust it unless you are willing to share your banking credentials with this service provider.
* **Additional latency on all traffic:** One of the hidden side effects which not many VPN providers talk about is the fact that your browsing could slow down when VPNs are used. If you use a VPN server halfway across the world, you are adding almost about 200ms to every request being sent... that may look small, but depending on how the service you are using is designed, it could quickly add up and may make your site excruciatingly slow to use.

## Recommendation on how to protect yourself
If you have a good business/personal reason to use a VPN this is what I would recommend

* Use a modern browser ( I use Chromebooks ) which secures traffic by default. If that allows you to avoid using VPN, avoid it.
* Turn on "[Enhanced protection](https://security.googleblog.com/2022/12/enhanced-protection-strongest-level-of.html)" in Chrome which will turn on basic security protections for free.
* If you have to use a VPN service, pick a very well known brand which has been out there for a while. The last thing you want is a fly-by-night provider who disappears once they have snooped on your traffic. The question I may ask myself before using a VPN service is if I trust them as much as my Operating System.
* If you are installing their VPN client on your device, ask yourself if you would trust them with your banking credentials

## Additional Recommendations
* Periodically run Security Checkup to make sure your Google accounts are secure.
* Use 2FA (Two Factor Authentication) for as many accounts as you can. This reduces risks if your VPN client leaks your password.
