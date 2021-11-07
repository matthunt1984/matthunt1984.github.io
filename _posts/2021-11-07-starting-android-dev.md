---
title: "Starting out on Android Development"
categories: tech
tags:
  - diy-tech
  - blog

toc: false
---

After a bit of a break from hobbyist development, and doing some formal AWS training towards certification I thought it might be worth trying to create my own Android App.

My goal - *create an app to track my food waste shame* 

Food waste is a real problem, for all sorts of reasons, and I feel a personal shame whenever I end up chucking food in the bin. However it is unfortunately very common and so - in typical devops fashion - I decided I wanted to at least instrument my own wastage to see if I could measure an improvement.

Now I couldn't see an obvious app for this on the Play Store, but if you know of one, please drop a comment. I might enjoy re-inventing this particular wheel, but if someone has a better one, I'll put that on my car!

Thus far I've set out on a journey as follows:
* Build an app focussing on Android (our household OS), so using Android Studio. I can look at cross-platform later - I'm a n00b at mobile development, so small increments and working software!
* Opportunity to learn Kotlin, as the community including Google seems to be pushing this over Java now in different areas and I'm intrigued
* Opportunity to learn gradle - as maven always seemed to be a pain, and again google/android community seems to be preferring it
* Use the latest google ML-kit APIs for barcode detection
* Do a basic 'backend for frontend' in AWS backed to a db such as Dynamo

Initial feedback is the usual - for me at least. Very new, very interesting and making slow but sometimes painful progress. A lot of the tech feels similar to other tools I've used before but each has its learning curve. I have today managed to get a version of 'my' app running on my phone that can detect barcodes and provide feedback, so I'm calling that a milestone!