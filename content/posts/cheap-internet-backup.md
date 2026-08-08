---
title: "Cheap Internet Backup"
date: 2026-08-08T14:27:59+03:00
---

Although my Internet connection is quite stable, we've had some occasional planned and unplanned outages.
While we could always enable mobile tethering on the phones, it would be a peace of mind to not have to worry about any of that.
I switched to Ubiquiti's Dream Router 7 (UDR7) after suffering with wifi issues and generally hearing good things about them.
They had also a LTE backup system, but at approximately 300€ with VAT, it wasn't really an option.
Other options might have worked as a second WAN, but all were still expensive and would require some external antennas or mounting (I almost bought one before seeing that it was not recommended for indoor use due to higher power levels).
Recently, Ubiquiti launched the [Unifi 5G backup](https://blog.ui.com/article/introducing-unifi-5g-backup) at approximately 100€ with VAT (note: post is **not** sponsored, I don't get anything for this).
This is just the price point to have some peace of mind, so I decided to try it out.

## Setup

The 5G backup is powered with PoE. I bought a PoE injector as a fallback, since it was about 8€ and allows me to set it up anywhere, even if the switch doesn't have a PoE port.
I ended up using the PoE port on the UDR7 to get it going, and got it adopted.
There were some hitches where it seemed to get a bit confused and look like it had re-adopted it, but after the firmware update I was able to set it up with an eSIM.
The U5G sits on its stand, but it has a magnetic mounting so you can also use the wall mount for a permanent install. I put it behind my TV near the router, and it gets a strong signal.
Worth noting as well, the device is limited to ~200 Mbps down, 100 Mbps up (slightly higher numbers with 5G) and uses 5G Standalone and RedCap, so not all networks will work as 5G but it can use LTE as well.

## Provider

My normal Internet connection is FTTB ethernet with Elisa, and I have also used their prepaid internet before, but since this is supposed to be a backup I wanted to go with a different provider.
I ended up with Telia, getting an eSIM online was quite easy, though the best way to do this is on a computer where you can then scan the QR code using the Unifi app on the phone to setup the new eSIM.
I loaded 10€ with a bonus of 5€ added to the account which should last a while.
There's no need to setup any sort of plan, there's a certain fee per GB of data but it caps at 1€/day without restricting the usage, which seems to be the norm in Finland.

Telia has a way to setup an account to check the balance, but it requires sending an SMS from the number.
I was concerned but a quick search found a [guide for the U5G to send/receive SMS](https://gist.github.com/keyz182/96a901d5ba1bf5f4b9701f5eba8729ad).
The quick instructions for this are to go to the device settings tab in the Unifi console and select "Debug" to open a shell, then run `ubus call uiwwand call '{"method":"send-sms","params":{"to":"+447700900000","text":"Hello from U5G"}}'`, changing the number and text to what was needed for the setup.
Supposedly incoming SMS are supposed to show up in the Network Notifications, but I haven't seen that (maybe a future update will fix it).

A big downside to using Telia I've realized is that they don't have IPv6 support on their network yet.
I hadn't even thought to check, Elisa has been using IPv6 on their mobile network for a while, as the growing number of devices basically requires it.
Instead, I have a CGNAT IPv4 address without IPv6, so I still can't fully rely on setting my VPS to IPv6-only.

## Fallback setup

Unifi's defaults here really make this shine.
I didn't have to setup a complicated SLA or failover, they've made their defaults for pretty much the common case of only using the fallback internet until the primary WAN is back online.
Some folks do set a [custom SLA](https://community.ui.com/releases/b8c2c086-2bfd-4ff7-b7ac-960f4134739c?replyId=149f28cb-294a-43fd-b1cf-f4982ebb5fbc&parentReplyIds=666fe7bb-7008-4f99-bee9-a21f8b224e8c), which can use less data.
That said, in the ~1 month since I've set it up, I haven't even gone through the bonus from the reload, and since I'll need to reload periodically (every 12 months), I'll have enough money for the occasional pings to likely only spend about 10€/year on this, which is great.
Of course, using the fallback will cost 1€/day, but these are low costs for having a failover I don't need to worry about.

## Closing thoughts

I think this is a great setup and I'm quite happy to have this.
Hopefully Telia brings in IPv6 support or gives options for exiting the CGNAT on prepaid so I can keep inbound connections working (e.g. Wireguard VPN).
I also hope to see more features on the firmware to get SMS messages better supported, especially to manage the prepaid connections.
Providers here don't really have much in terms of fallback/backup internet plans, but prepaid plans can be used for this at very affordable levels.
There hasn't been an outage for me to test with yet, but I'm confident that it should just work when it happens.
