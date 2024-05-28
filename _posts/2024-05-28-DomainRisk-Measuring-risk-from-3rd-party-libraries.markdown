---
layout: post
title:  "DomainRisk: Measuring risk from 3rd party libraries"
date:   2024-05-28 03:14:15 +0000
categories: security supplychain
---
Using third-party libraries is considered a smart decision not only because its faster to develop, but also because these are generally well tested for quality and for efficiency. However, it comes with its own can of worms which needs to be paid attention to.

## Risks with third-party libararies
* **Typosquating or Masquerading**: Impersonation could happen either by typosquating the package name or the website where its hosted on. For example its relatively simple to create similar looking instances of popular github packages and figuring out which to use may not always be obvious. Jfrog has some great examples [here](https://jfrog.com/blog/developers-under-attack-leveraging-typosquatting-for-crypto-mining/) and [here](https://jfrog.com/blog/malware-civil-war-malicious-npm-packages-targeting-malware-authors/). But the best example of this type of attack is [this one](https://jfrog.com/blog/python-wheel-jacking-in-supply-chain-attacks/) where they document how a developer was used the malicious packages because the package manager was not prepared to handle a package naming conflict.
* **Compromizing the vendor**: The attempt to install [backdoor in XZ](https://techcommunity.microsoft.com/t5/microsoft-defender-vulnerability/microsoft-faq-and-guidance-for-xz-utils-backdoor/ba-p/4101961) may have been caught in time, but it highlighted the glaring hole in our assumptions that open source is immune supply chain attacks.
* **Compromizing the hosting provider**: Many web developers choose to use vendor's CDN to host their libraries for two reasons. The vendor may have a better CDN infrastructure and by having the vendor control the code revisions, the web developer may not have to worry about updating the libraries everytime a security/quality/performance bug is fixed. However, this trust granted to the vendor should be evaluated carefully.

## Recommendations
While a developer cannot address all the risks, there are two specific recommendations which can dramatically reduce the attack vectors on the website.
* Reduce the number of third party vendors the site depends on. For example, if you are going to use jquery, and jquery has all the libraries you need, I recommend you choose that instead of going to different vendors for different functionalities. Monitoring and auditing vendors is not easy, and having lesser vendors to track would save you time.
* Host as many libraries as you can yourself: For example, choose to host the jquery libraries yourself instead of using a different CDN to reduce risk of a comproimize because someone else got hacked.

## DomainRisk
If you are curiuos what your website is using, I recommend you checkout [DomainRisk](https://github.com/royans/domainrisk).
* This tool looks inside your script tag to find and list unique hostnames you are getting your javascript libraries from
* If you are showing Ads, or generating Analytics, or using a CSS beautifucation library, its normal to have those libraries listed
* But after reviewing most of the top 1000 websites on the internet today, its clear that most sites use less than 10 unique hostnames.

## Data 
I had DomainRisk run against the top 1000 websites by traffic to get a sense of what is normal today and here is what I found
* The average number of domains mentioned in the script tag: 11
* The median number of domains mentioned in the script tag: 7
* 90th percentile had 26 domains mentioned
* 99th percentile had 69 domains mentioned
* However, I found 3 which had over 100
* And I found at least 1 domain which had a high number of domains mentioned because it was potentially misconfigured. I've reported the issue to the website owner to take action.

