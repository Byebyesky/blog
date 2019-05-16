---
layout: "post"
title:  "SHA2017 Teaser CTF - Crypto Engine"
date:   2017-06-12 02:24:29 +0000
categories: ctf sha-2017
---

Hello my name is Flyingsky.

I'm pretty new and wanted to broaden my knowledge, so I took part in the SHA2017 teaser CTF.
I thought let's take on the crypto challange because you've never worked with cryptography.
It was the only challenge I was able to solve though, because that day I only had 2 hours of time...

Anyways let's get to the write up:

When you clicked on the challange you got greeted by this:  
![Task](/{{site.baseurl}}/assets/ctf/cryptoEngine/task.png)

If you clicked on the "crypto engine" link you got presented a input box that allowed you to encrypt your own input.  

![Text Box](/{{site.baseurl}}/assets/ctf/cryptoEngine/box.png)

If you clicked on the "flag" link you got this:  

![Encrypted Flag](/{{site.baseurl}}/assets/ctf/cryptoEngine/encFlag.png)

The first thing I tried was, what happens if I put in some random text so I put in 1 A and it told me that there is "no text to encrypt". So there is a minimum input length.
I tried `AA` with the same result. But with `AAA` I got this:  

![Input AAA](/{{site.baseurl}}/assets/ctf/cryptoEngine/aaa.png)

So the minimum is 3 characters. Next I put in `AAAA`, and this time it worked and gave me this result:  

![Input AAAA](/{{site.baseurl}}/assets/ctf/cryptoEngine/aaaa.png)

So everything above 3 characters works! Next I put in some random text and looked if there is anything useful to notice, and there was! I noticed that 3 characters make 1 block and if it's less than 3 it displays a 2 or 4 digit number.
Next I counted the blocks in the flag picture and calculated `12 * 3 + 2` that the flag was 38 characters long.

(38 * "A")  
![Input 38 times A](/{{site.baseurl}}/assets/ctf/cryptoEngine/38a.png)

From this picture I saw that the pattern repeats every 8 blocks (24 characters)!

Then I looked at the flag picture and tried to understand how to decode it. Soon came the idea of reading the hexvalues of each coloured block with the pipette. So I downloaded the flag picture and put it into krita and used the pipette to extract the hash from the picture.

`545c50 502f38 353149 010d02 2c633e 364957 06032a 323f67 185359 012832 3c351c 540400 2e29`

With this in my hands I finally was able to go about solving the challenge. I bruteforced the first 2 blocks to try to find a pattern... with no luck.
But after the 3rd block I noticed that certain values seemed to be swapped around. If I put in 0x35 I got 0x65 and vice versa!
From this I thought I could just put the whole hash into the encryption box and have it decrypt it for me, but that sadly wasn't true.  

![Input 38 times A](/{{site.baseurl}}/assets/ctf/cryptoEngine/fullhash-encryption-wrong.png)

I tried to understand what was going on and tried encrypting more things. At some point I noticed that the encrypted output changed depending on what preceded it. So I knew I had to decrypt it block for block! Which worked exactly as I thought it would!

![Explanation](/{{site.baseurl}}/assets/ctf/cryptoEngine/explanation.png)

I put in `0x353149` and got `0x656166`, those are the printable ascii characters e, a and f! I put them after the 2 brute forced blocks and it really was true!

![Encrypt](/{{site.baseurl}}/assets/ctf/cryptoEngine/encrypt.png)

After I knew that decrypting the hash was only a matter of encrypting each block preceded by the right characters.

(the last 2 hex digits put into the encrypt field)
![Observation 1](/{{site.baseurl}}/assets/ctf/cryptoEngine/observation1.png)

(the 0x327d put into the encrypt field, it yields the desired result)
![Observation 2](/{{site.baseurl}}/assets/ctf/cryptoEngine/observation2-2.png)

With that I solved the Crypto challenge of the SHA2017 teaser round.
The whole flag was:  
`flag{deaf983eb34e485ce9d2aff0ae44f852}`