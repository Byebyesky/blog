---
layout: "post"
title:  "Hackcon 2018 – Salad Upgrades"
date:   2018-08-17 04:25:10 +0000
categories: ctf hackcon
---

![Mission](/{{site.baseurl}}/assets/ctf/hackcon/saladUpgrades/mission.png)

We have a string `e4uo{zo1b_1e_f0j4l10i}z0ce`. And they used several rotations on it.
By looking at it and assuming that the numbers stay the same I immediately noticed "th1s_1s_...".
And since the flag format is `d4rk{...}c0de` we only have one word left to crack.
`f0j4l10i`, for this I downloaded a list with 30000 8 letter words and ran a grep serach with wildcards on them.  
![Mission](/{{site.baseurl}}/assets/ctf/hackcon/saladUpgrades/grep.png)

There I noticed rotation and knew this was the word I was searching for!
Putting it together we get the flag:
`d4rk{th1s_1s_r0t4t10n}c0de`