---
author: pwnstar
title: The Necromancer 1
date: 2023-07-14T09:21:11+03:00
description: A very light, Beginner friendly Vulnhub machine
math: false
---

> Hey, all! This is a writeup to a vulnhub machine I solved recently, after it was suggested to me by thatsitamar.

> I have both machines set up on NAT, the ova runs and my attacker machine (Heavily configured Ubuntu, I'd say to better follow maybe do it on Kali or Parrot?)

> This, of course, is a general outline without detailed explanations& a lot (but a lot) of memes!

Starting is rather trivial. We can run 

```
sudo arp-scan -l
```
To find the ip of the victim's machine. You can also get it with an [nmap](https://nmap.org/) scan, if you set them both on [NAT](https://www.cisco.com/c/en/us/products/routers/network-address-translation.html).

Anyhow, once you get the IP and scan it, it seems as if no ports are open, i.e we can't really progress, can we?

Well, I just hopped over to wireshark and sniffed traffic on ```eth0```. Cool, this was much better as we saw communication, so we can just set up a netcat and get a chunk of base64 encoded text, you can just decode it from the terminal with

{{< code language="bash" title="script" id="3" expand="Show" collapse="Hide" isCollapsed="true" >}}
echo "Text" | base64 -d
{{< /code >}}