---
layout: post
title:  "HSTS: Forcing HTTPS for your service"
date:   2024-03-30 21:53:19 +0000
categories: hsts https encryption
---

You may have a hard time finding a non-HTTPS site today, but it was not too long ago when the default was HTTP. Sites like Facebook and Google were available without HTTPS/SSL for anyone in the network path to sniff and inject traffic into (even steal passwords and cookies). Some ISPs in particular loved to know what you were searching for, and loved even more to inject javascripts to show advertisements onto any page you were browsing. Migration towards HTTPS had started a while back and it had a significant impact in reduction of stolen credentials and as a result reduction in online abuse.

Unfortunately, websites still had to keep their HTTP version of their sites running. Not all users had switched to browsers which supported SSL back then. While the sites did automatically redirect supported browsers from HTTP to HTTPS, the redirect was being done using HTTP response header 301. This allowed an opening for an attacker to modify the 301 redirects to keep the browser on the HTTP version of the site.

## HSTS to the rescue
HSTS (HTTP Strict Transfer Security) was initially proposed by the IETF as [RFC 6797](https://www.rfc-editor.org/rfc/rfc6797) back in 2012. It was a policy mechanism designed specifically to protect against [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) and [protocol-downgrade](https://en.wikipedia.org/wiki/Downgrade_attack) attacks.

While you can read the very detailed RFC to understand why it was designed the way it was designed, the actual implementation of this feature was downright simple.

> HTTP Strict Transport Security (HSTS) is a policy mechanism that helps to protect websites against [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacks such as [protocol downgrade attacks](https://en.wikipedia.org/wiki/Protocol_downgrade_attack) and [cookie hijacking](https://en.wikipedia.org/wiki/Session_hijacking). It allows web servers to declare that web browsers (or other complying [user agents](https://en.wikipedia.org/wiki/User_agent)) should automatically interact with it using only [HTTPS](https://en.wikipedia.org/wiki/HTTPS) connections, which provide [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS/SSL), unlike the insecure HTTP used alone. HSTS is an IETFstandards track protocol and is specified in RFC6797.

HSTS policy is communicated by the server to the browser/client using a standard HTTP response header. Once instructed, the browser would be forced to ignore the HTTP end points of the server for a given amount of time. The following HTTP header (if sent as part of the HTTP response) would force the browser to use the HTTPS end point of this service for 1 year.

``
Strict-Transport-Security "max-age=31536000;"
``

To automatically protect against all sub-domains of this website, the HSTS policy header allowed one to include the text “includeSubdomains” to enforce HTTPS across all of its properties. Looks like this:

``
Strict-Transport-Security "max-age=31536000; includeSubDomains;"
``

There was still a problem with this feature. For HSTS to work, the server has to instruct the client to use HSTS in the response header of the plain-text HTTP session. This allowed an attacker in the middle to strip out HSTS headers to disable the enforcement.

## Preloading HSTS
To protect against this attack, the popular browser vendors (chrome included) launched a program to preload the browsers with a list of domains which should only use HTTPS. While this was initially a static list, any website can get themselves included in this preload list by updating their header to include “preload” and using the [hstspreload service](https://hstspreload.org/)  to register the domain formally.

``
Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
``

Once its accepted, this makes its way into [Chrome’s preload list](https://source.chromium.org/chromium/chromium/src/+/main:net/http/transport_security_state_static.json), which then makes it way into Chrome and other browsers.
HSTS is just one small piece of the infrastructure which Chrome implements to keep its user data and communications safe from attackers. Kudos to all the browser developers for always staying a step ahead in this very challenging cat and mouse game.

## References
* [Browser Compatibility](https://caniuse.com/stricttransportsecurity)
* [Wikipedia page](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)
* [HSTS Preload submission form](https://hstspreload.org/)
* [Mozilla web security guidelines](https://infosec.mozilla.org/guidelines/web_security)
* [Google's web security guidelines](https://web.dev/explore/secure?hl=en)

