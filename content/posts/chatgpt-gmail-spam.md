---
title: "Bypassing Gmail's spam filters with ChatGPT"
date: 2023-01-22T09:15:00-08:00
showDate: true
---

If you haven't been living under a rock, you probably already know what
[ChatGPT](https://en.wikipedia.org/wiki/ChatGPT) is.

I am an operator of [high bandwidth](https://metrics.torproject.org/rs.html#search/family:DF9BCBE0F85EC7424F5E0469DECAA006070B5E15)
[Tor exit relays](https://2019.www.torproject.org/about/overview.html.en),
and with that, I get a *lot* of abuse complaints. Combined with the fact that
I have ai lease on a "/24" of IPv4 space, basically a block of 256 IP
addresses, I have the IP space's contact to my LLC's email address.

What does this have to do with ChatGPT, you may ask? Last night, I was sending
responses to abuse complaints using
[Tor's exit boilerplates](https://community.torproject.org/relay/community-resources/tor-abuse-templates/), and while I run my own email server, I had to
email Abusix which used Gmail/Google Workspace.

The standard Tor boilerplate below was caught by Gmail as "spam":

> The IP address in question is a Tor exit node.
> https://www.torproject.org/overview.html
>
> There is little we can do to trace this matter further.
> As can be seen from the overview page, the Tor network is designed to make tracing of users impossible.
> The Tor network is run by some 5000 volunteers who use the free software provided by the Tor Project to run Tor routers.
> Client connections are routed through multiple relays, and are multiplexed together on the connections between relays.
> The system does not record logs of client connections or previous hops.
>
> This is because the Tor network is a censorship resistance, privacy, and anonymity system used by whistle blowers, journalists, Chinese dissidents skirting the Great Firewall, abuse victims, stalker targets, the US military, and law enforcement, just to name a few.
> See https://www.torproject.org/about/torusers.html.en for more info.
>
> Unfortunately, some people misuse the network. However, compared to the rate of legitimate use (the IP range in question processes nearly a gigabit of traffic per second), abuse complaints are rare: https://support.torproject.org/abuse/.

I was worried my IPs were blacklisted, so I sent emails to a few gmail.com
addresses and they all went through fine, so it probably is Gmail thinks the
standard Tor response is "spam" when it's not.

Condidering that ChatGPT is the hottest thing right now, I immediately signed
up for OpenAI and asked it to reword the above response. I got this:

> It appears that the IP address in question is associated with a Tor exit node, as outlined on the Tor Project's overview page: https://www.torproject.org/overview.html. Due to the nature of the Tor network, which is designed to protect users' anonymity and privacy by routing their internet traffic through multiple layers of encryption and relays, it is difficult, if not impossible, to trace the source of this matter further. The Tor network is run by a community of approximately 5000 volunteers who use the free software provided by the Tor Project to operate Tor routers. Connections made by users are routed through multiple relays and are multiplexed together on the connections between relays. The system does not record logs of client connections or previous hops.
>
> The Tor network is a censorship resistance, privacy, and anonymity system that is used by a variety of individuals and groups such as whistle-blowers, journalists, Chinese dissidents, abuse victims, and law enforcement agencies. For more information on the various ways in which the Tor network is used, please visit: https://www.torproject.org/about/torusers.html.en
>
> It is worth noting that while the Tor network is used for a variety of legitimate purposes, it is also sometimes misused by individuals or groups for illegal or malicious activities. However, compared to the rate of legitimate use, which is substantial (the IP range in question processes nearly a gigabit of traffic per second), reports of abuse are relatively infrequent. For more information on abuse complaints and how they are handled, please visit: https://support.torproject.org/abuse/

While it is longer, it got through Gmail's spam filters fine.

I had a legitimate use of ChatGPT, I was stuck with either leave it without a
response, hastily write a response personally at 11 PM, or have ChatGPT reword
the Tor boilerplate so I can go to bed.

I'm no AI or machine learning expert so I don't know how it works. But I am
also worried that spammers could use ChatGPT to get around Gmail and Outlook's
spam filters. What about Yahoo or self-hosted/on-prem servers who may not have
the same AI talent as Google or Microsoft? (Disclaimer: I presently work at
Microsoft 365, but as likely inferred earlier don't use Outlook/Exchange Online
outside of work).

I already get "spam" coming from @gmail.com or @outlook.com emails, mainly
since I am subscribed to FreeBSD's mailing lists and I am too lazy to train
rspamd.

But assuming that doomsday scenario mentioned above doesn't happen, ChatGPT can
be my go-to place to write Tor boilerplates when needed.
