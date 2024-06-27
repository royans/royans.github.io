---
layout: post
title:  "DomainRisk: Measuring risk from 3rd party libraries"
date:   2024-05-28 03:14:15 +0000
categories: security supplychain
---
Using third-party libraries is considered a smart decision not only because its faster to develop, but also because these are generally well tested for quality and for efficiency. However, it comes with its own can of worms which needs to be paid attention to.

### Risks with third-party libararies
* **Typosquating or Masquerading**: Impersonation could happen either by typosquating the package name or the website where its hosted on. For example its relatively simple to create similar looking instances of popular github packages and figuring out which to use may not always be obvious. Jfrog has some great examples [here](https://jfrog.com/blog/developers-under-attack-leveraging-typosquatting-for-crypto-mining/) and [here](https://jfrog.com/blog/malware-civil-war-malicious-npm-packages-targeting-malware-authors/). But the best example of this type of attack is [this one](https://jfrog.com/blog/python-wheel-jacking-in-supply-chain-attacks/) where they document how a developer was used the malicious packages because the package manager was not prepared to handle a package naming conflict.
* **Compromizing the vendor**:  
    * Polyfill.io was [hacked](https://www.theregister.com/2024/06/25/polyfillio_china_crisis/) by an organization to inject malicious payload into 1000s of websites globally which were using javascript libraries hosted on its cdn infrastructure.
    * The attempt to install [backdoor in XZ](https://techcommunity.microsoft.com/t5/microsoft-defender-vulnerability/microsoft-faq-and-guidance-for-xz-utils-backdoor/ba-p/4101961) may have been caught in time, but it highlighted the glaring hole in our assumptions that open source is immune supply chain attacks.
* **Compromizing the hosting provider**: Many web developers choose to use vendor's CDN to host their libraries for two reasons. 
    * The library vendor may have a better CDN infrastructure. This could significantly reduce the delivery latency.
    * The vendor can directly control the code revisions which allows rapid updates to address security/quality/performance bugs.

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
| www.googletagmanager.com       | 39.2636 |
| cdnjs.cloudflare.com           | 12.1817 |
| ajax.googleapis.com            | 11.5691 |
| cdn.jsdelivr.net               | 10.2291 |
| www.google.com                 | 10.0696 |
| cdn.cookielaw.org              |  8.6274 |
| securepubads.g.doubleclick.net |  7.1789 |
| code.jquery.com                |  6.4004 |
| stats.wp.com                   |  6.3940 |
| pagead2.googlesyndication.com  |  6.2281 |
| assets.adobedtm.com            |  4.9199 |
| kit.fontawesome.com            |  3.5671 |
| unpkg.com                      |  3.4905 |
| platform.twitter.com           |  3.0311 |
| www.gstatic.com                |  2.7758 |
| static.addtoany.com            |  2.6929 |
| ajax.cloudflare.com            |  2.5014 |
| use.typekit.net                |  2.3930 |
| js.hs-scripts.com              |  2.1696 |
| translate.google.com           |  2.0292 |
| maps.googleapis.com            |  2.0037 |
| connect.facebook.net           |  1.7485 |
| static.chartbeat.com           |  1.7485 |
| d3e54v103j8qbb.cloudfront.net  |  1.7293 |
| cdn.parsely.com                |  1.7229 |
| www.googletagservices.com      |  1.6144 |
| cmp.osano.com                  |  1.6017 |
| consent.cookiebot.com          |  1.6017 |
| apis.google.com                |  1.5506 |
| cdn.onesignal.com              |  1.5443 |
+--------------------------------+---------+
</pre>
