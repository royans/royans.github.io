---
layout: post
title:  "DomainRisk: Measuring risk from 3rd party libraries"
date:   2024-05-28 03:14:15 +0000
categories: security supplychain
---
Using third-party libraries is considered a smart decision not only because its faster to develop, but also because these are generally well tested for quality and for efficiency. Additionally some developers prefer to use the libraries hosted on 3rd party CDNs (content distribution networks) which helps reduce latency in page load. 

However, it comes with its own can of worms which needs to be paid attention to.

### Risks with third-party libararies
* **Compromizing the vendor**: Attacking a library vendor is not impossible, and once compromized, malicious code injected into the source could be a quick way to infect a large group of customers. 
    * The most brazen example of this hack was the attempt to install a [backdoor in XZ](https://techcommunity.microsoft.com/t5/microsoft-defender-vulnerability/microsoft-faq-and-guidance-for-xz-utils-backdoor/ba-p/4101961) with the goal of backdooring ssh itself which uses XZ. Fortunately, it was caught by a very watchful engineer who noticed an odd perfomance behaviour.
* **Typosquating or Masquerading**: Impersonation could happen either by typosquating the package name or the website where its hosted on. For example its relatively simple to create similar looking instances of popular github packages and figuring out which to use may not always be obvious. Jfrog has some great examples [here](https://jfrog.com/blog/developers-under-attack-leveraging-typosquatting-for-crypto-mining/) and [here](https://jfrog.com/blog/malware-civil-war-malicious-npm-packages-targeting-malware-authors/). But the best example of this type of attack is [this one](https://jfrog.com/blog/python-wheel-jacking-in-supply-chain-attacks/) where they document how a developer was used the malicious packages because the package manager was not prepared to handle a package naming conflict.
* **Compromizing the hosting provider**: Polyfill.io was [hacked](https://www.theregister.com/2024/06/25/polyfillio_china_crisis/) by an organization which got control of the domain cdn.polyfill.io, which was being used to host this library. It then used it to inject malicious payload into 1000s of websites globally which were using javascript libraries hosted on this cdn infrastructure.

### Recommendations
While a developer cannot address all the risks, there are two specific recommendations which can dramatically reduce the attack vectors on the website.
* Reduce the number of third party vendors the site depends on. For example, if you are going to use jquery, and jquery has all the libraries you need, I recommend you choose that instead of going to different vendors for different functionalities. Monitoring and auditing vendors is not easy, and having lesser vendors to track would save you time.
* Host as many libraries as you can yourself: For example, choose to host the jquery libraries yourself instead of using a different CDN to reduce risk of a comproimize because someone else got hacked.

### DomainRisk
If you are curiuos what your website is using, I recommend you checkout [DomainRisk](https://github.com/royans/domainrisk).
* This tool looks inside your script tag to find and list unique hostnames you are getting your javascript libraries from
* If you are showing Ads, or generating Analytics, or using a CSS beautifucation library, its normal to have those libraries listed
* But after reviewing most of the top 1000 websites on the internet today, its clear that most sites use less than 10 unique hostnames.

### Data
Based on a quick scan of the top sites, here is the distribution of hosts being called from the script tag in the hosts I scanned in the last hour.
<pre>
+--------------------------------+---------+
| hostname                       |    %    |
+--------------------------------+---------+
| www.googletagmanager.com       | 42.3049 |
| cdnjs.cloudflare.com           | 13.2999 |
| ajax.googleapis.com            | 12.3949 |
| www.google.com                 | 10.7963 |
| cdn.jsdelivr.net               | 10.7324 |
| cdn.cookielaw.org              |  8.2042 |
| code.jquery.com                |  7.0533 |
| securepubads.g.doubleclick.net |  7.0041 |
| pagead2.googlesyndication.com  |  6.6893 |
| stats.wp.com                   |  6.5909 |
| assets.adobedtm.com            |  4.7464 |
| kit.fontawesome.com            |  3.7972 |
| unpkg.com                      |  3.7185 |
| platform.twitter.com           |  3.2414 |
| static.addtoany.com            |  2.7101 |
| www.gstatic.com                |  2.6118 |
| ajax.cloudflare.com            |  2.4986 |
| use.typekit.net                |  2.4495 |
| js.hs-scripts.com              |  2.2675 |
| maps.googleapis.com            |  2.1543 |
| translate.google.com           |  2.1101 |
| connect.facebook.net           |  1.9527 |
| d3e54v103j8qbb.cloudfront.net  |  1.9183 |
| consent.cookiebot.com          |  1.7117 |
| apis.google.com                |  1.6526 |
| shop.app                       |  1.6526 |
| s7.addthis.com                 |  1.6182 |
| www.googletagservices.com      |  1.6182 |
| cdn.onesignal.com              |  1.5985 |
| cdn.parsely.com                |  1.5936 |
+--------------------------------+---------+
</pre>
