---
layout: post
title:  "Explaining Zero-Trust to a 5 year old"
date:   2024-03-24 03:53:19 +0000
categories: security 0trust  
---
# Explaining Zero-Trust to a 5 year old
While reading a CISA advisory, I realized that one of the common ransomware attack patterns involves compromising an edge network device to gain access to a private internal network. Once an attacker gets in, it gets significantly easier to compromise the remaining devices on the network. 

> Post-compromise, the actors moved laterally into domain environments and have been observed leveraging tools that are native to the Ivanti appliances—such as freerdp, ssh, telnet, and nmap libraries—to expand their access to the domain environment. The result, in some cases, was a full domain compromise. This is one of the reasons why many large organizations, like Google, abandoned use of VPNs in favor of Zero-Trust design principle. If you don't know what Zero-Trust is, and how its different from VPNs, let me give you two easy examples.

### Explaining Zero-Trust to a 5 year old
* VPN is like the house you live in. You can leave your toys in any room without having to worry about whether it would be there the next day or not.
* Zero-Trust is what you feel at an airport. You would never leave your toys unattended even for a minute. You assume that every person is a stranger and you will share your toys only with those you really know.
  
### Explaining Zero-Trust to a 10 year old
* VPNs are like a bank vault. It's very secure, but if a thief does manage to get inside they can take all the diamonds without breaking a sweat. This is why there are so many movies about breaking into bank vaults. 
* Zero-trust on the other hand assumes there is no perfect vault. Each and every diamond gets its own private vault with its own secure key combination. This is not impossible to crack all the safes, but its significantly harder to execute in a short time without being noticed.
  
## Building for tomorrow
Now that you understand how Zero-trust is different than VPN, you should consider reading this post by Tony Ureche which goes into more details of how ChromeOS uses these security principles to protect its users:
* **Anticipating Attack** ChromeOS assumes that attackers will target your organization. Set up a proactive security strategy that protects users, data and devices. Minimize the attack surface, store data in the cloud, and explicitly ensure device security before granting access.
* **Robust Authentication** Every access attempt must be verified before they are authorized. Instead of basing access on corporate network enrollment, robust authentication means considering a number of different signals like device health, user location, and attempted actions.
* **Proactive Monitoring** Continually monitor your device and data landscape to identify anomalies. Ensure complete visibility over sensitive data, where it originates, how it moves across the network, and who has access to it. Collect insights into device usage, application behaviors, and emerging threats. Use this data to proactively adjust policies, address vulnerabilities, and respond quickly to potential attacks.

Reference: [ChromeOS designed for zero-trust](https://cloud.google.com/blog/products/chrome-enterprise/chromeos-the-platform-designed-for-zero-trust-security)

