---
title: "Using Office 365 Mail on Android With 2FA Without Outlook or InTune"
date: 2020-01-12T19:00:00-08:00
showDate: true
---

I'm a recent [Microsoft](https://www.microsoft.com/) hire. That being said,
it's obvious that they're using Office 365's Hosted Exchange for their email,
which is a departure from G Suite at [NYU](https://www.nyu.edu/) and CacheCash,
and my personal FreeBSD/Postfix/Dovecot setup.

Like many big companies, Microsoft requires 2FA (Two-Factor Authentication) for
logging in to company resources. Well, okay, I done this before. But then, to
use corporate email "officially", I have to use not only Outlook, but also the
InTune App, which "manages" my device.

I can understand InTune for most users, but for me, reasons including privacy
and the fact that I rooted my device (Google Pixel 3), I did not want MS to
"manage" my device.

However, there is one way you can get your Office 365 Mail on Android without
Outlook or InTune: Just use Firefox as "Outlook", and log in to Office 365 in
"desktop mode". The only thing you lose is notifications, but hey, it's not
like K-9 Mail is great with that either with my personal email setup.

So how would you do this?

First, if you don't already have it, download Firefox from the Play Store, or
externally if you "ungoogled" your Android.

Then, launch Firefox, and go to **office.com**.

Next, touch on the three dots and select **Request Desktop Site**. You need to
do this, otherwise you would need InTune.

Zoom in and select **Sign In**.

Sign in the way you normally would, I won't describe it here.

Then, when it asks you to save your information:

 * If you want to emulate the official "Outlook" app and can live with cookies, say **Yes**

 * If you absolutely don't want cookies and can live with relogging in anytime, say **No**

I chose **Yes**, but think for yourself whether you want privacy or convenience.

Then, when you want to use your mail, use **outlook.office365.com** in the
address bar. Consider pinning it to the Firefox start screen.

Keep in mind that these instructions may work on other browsers, but I have not
tested them here.
