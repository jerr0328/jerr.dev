---
title: "Sovereign Email"
date: 2026-02-25T20:04:00+02:00
---

With the general state of the world right now, the chance that current allies turn on each other seems ever-growing.
I'm not a huge fan of having my main data, especially emails, be handled on servers outside of Europe these days, so I've been looking at options of having a service managing it within Europe.
This also has benefits in ensuring EU laws around privacy and digital rights, along with generally supporting European companies.

## Requirements

If I'm going to switch services, I need something that can meet my needs without costing me a fortune:

1. Several custom domains (10 and counting...)
2. Catch-all email (`*@mydomain.tld`)
3. Sending email as aliases
4. Blocking mail to a certain address (for catch-all cases)
5. Good spam protection
6. Multi-factor authentication (it's 2026, I shouldn't even need to write this)
7. Single-user support, with option to add more seats for family
8. Usable with third-party email/calendar/contact clients

I like to use different domains for different situations, and I have situations where I need to contact support with an email address matching a catch-all I set (e.g. I could set a GitHub account with an email like `github@mydomain.tld`). The aliases then must be something I can easily configure and send emails from.
Of course, using the catch-all for tracking which service leaked my email doesn't help as much if I can't block emails to that address.

## Nice-to-have features

Outside of the requirements, I'm also looking at the other features in these services.
For example, Google Drive and its Docs, Sheets, and Slides are powerful tools, and I do occasionally use those on my personal gmail account.
These features would be nice, but if that increases the price too much I can also avoid it and use some self-hosted options (e.g. using Libreoffice and syncing files on a Nextcloud instance, or just on my NAS and using VPN).

## Contenders

### Proton Mail

Proton is a good alternative with a free plan, but it's more an alternative to Gmail than for custom domains.
Their paid plans offer custom domain support, but last I checked you need to go significantly up in tiers to get more than one domain, and then the costs become quite large (and seem to cap at 3 domains for personal use as of this writing).
In addition, they only support their own client due to the encryption stuff, or using a self-hosted bridge for IMAP.
This pretty much excludes them right away, but wanted to mention since I have used their free email.

### Mailbox.org

Mailbox is a German company and offers custom domains at their 3€/month or 30€/year plan. They do allow custom domains and catch-all email, but it seems like they limit to 50 aliases, and each domain would be an alias.
Storage is on the lower end at 10 GB for the inbox and 5 GB for their cloud drive with office tools.

I decided to give it a try, but while searching for some unrelated documentation, I found [reports of bad configuration allowing spoofing on custom domains](https://discuss.privacyguides.net/t/remove-mailbox-org/20232), alongside having a bad 2FA implementation until very recently (and the poor handling of it [as mentioned in a comment on that thread](https://discuss.privacyguides.net/t/remove-mailbox-org/20232/28)).
This immediately raised red flags and I decided to not continue trialing this service.

### Infomaniak kSuite

kSuite is a Swiss product, and offers quite an expansive plan for 2€/month. That includes 50 GB of storage, an office/drive suite, and additional services that may or may not be useful.
It seems to not have any limit to the amount of domains you can add, and it allows up to 50 aliases.
However, the way it handles catch-all is a bit peculiar, where it seems to be a separate inbox, counting for the "2 emails per user" limit. kSuite make catch-all go on all the domains, so you can't just opt-in domains for it.
It also doesn't seem to allow any way to bounce certain emails from the catch-all, even suggesting that you probably don't want to use a catch-all because of spam.

I gave this a try, and an annoying thing I found is that they don't offer WebDAV for their kDrive on the standard plan, I would have to upgrade to their more expensive (6.58€/month) business plan to be able to use that. WebDAV for kDrive is what would allow me to sync that with my NAS.
This isn't critical, but it does make it feel a bit less sovereign if I can't sync it to a NAS.
One positive thing was the domain configuration was pretty easy, and they offer autoconfig/autodiscover CNAME targets to make email configuration easier.
A slightly weird thing was that they have configuration profiles for iOS devices, which makes it easy to set up quickly but also is always a bit worrisome if they can do other things. Removing the profile removes the accounts.
The documentation is quite extensive, I was able to find a lot of information on many things I needed to do.
I ended up cancelling since I didn't want to have an awkward experience dealing with custom domains. I also was able to get in touch with their support regarding the cancellation to undo the next month's bill that got auto-paid.

### Soverin

Soverin is a Dutch email provider that seems to meet a lot of my core requirements. They focus on emails for custom domains, and allow an unlimited amount of domains and aliases.
The pricing is more expensive, at 39€/year (and only on annual plans). That is equivalent to 3.25€/month.
That comes with 25 GB of space, and adding additional mailboxes is at 10€/year/mailbox, so it becomes a better  deal quickly if adding more users.
Storage is shared, however, but since there's no office suite or drive, it likely shouldn't become much of an issue (it's another 29€/year per 25GB though).

They offer a basic webmail alongside their configuration portal.
It's relatively easy to add new domains and aliases, and you can also set the alias as a "from" address in the webmail by adding a new identity.
They use CalDAV for the calendar/contacts (CardDAV).
Each time you add a client, a new application password is generated and displayed once.
Two-factor authentication is handled via TOTP only.
The documentation could be a bit more extensive, I wasn't able to see beforehand if I could easily set filters to bounce emails based on the address it's sent to (for the catch-all).

### QBoxMail

QBoxMail is a mail service targeted to resellers based in Italy. They seem to offer unlimited domain aliases, but only 20 email aliases.
Pricing is around 1.90€/month (excluding VAT) per account, with 8 GB of email space. They have a cheaper plan at 1.50€/month (excluding VAT), but it disables some features I would need (missing Calendar, no CardDAV/CalDAV).

## Results

After playing around a bit, I decided to go with Soverin. Their setup is pretty quick and they don't require a ton of information to get started.
It's certainly an increase in price from my previous Zoho plan, but I expected to pay a bit more for a sovereign product.
