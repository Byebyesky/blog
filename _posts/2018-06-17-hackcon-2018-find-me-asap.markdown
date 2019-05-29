---
layout: "post"
title:  "Hackcon 2018 – Find Me, ASAP"
date:   2018-08-17 04:27:04 +0000
categories: ctf hackcon
---

We were presented with a bunch of numbers like [this](https://pastebin.com/gWZr5pz9).

This one was just a simple decimal to character conversion:

```python
import sys

f = open(sys.argv[1], "r")
string = f.read()
array = string.rsplit(' ', 5000)

for z in range(len(array)):
    sys.stdout.write(chr(int(array[z], 10)))

sys.stdout.write('\n')
sys.stdout.flush()
```

Running this script yielded a text about ascii which contained the flag: 

![Flag]({{site.baseurl}}/assets/ctf/hackcon/findMe/flag.png)
