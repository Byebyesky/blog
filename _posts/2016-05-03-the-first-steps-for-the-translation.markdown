---
layout: "post"
title:  "The first steps for the translation"
date:   2016-05-03 20:17:54 +0000
categories: mapleStory
---

I found the thread of Maple Story on GBATemp a while ago, and thought about joining the project, but I never asked the OP. 
It's was about 6 months ago when I found it.

Alright let's get to the translation stuff:

The first thing I had to do was decrypting the .cia file. That was done by using Decrypt9's deep CIA decypter. After that I was left with a seemingly normal .cia file, and was confused at first, until I read that D9 decrypts the cia itself and doesn't make a copy of it.

After that I had to extract the .cia. I followed Asia81's tutorial on decrypting and extracting CIAs and extracted the .cia with his tools. If you want to decrypt and unpack CIAs yourself, take a look at this [Tutorial](https://gbatemp.net/threads/tutorial-how-to-decrypt-extract-and-rebuild-a-cia.388677/).

Then I had 4 files and 4 folders:  
![Folders]({{site.baseurl}}/assets/maple/firstSteps/foldersAndFiles.png)

The only one that is important for me is the ExtractedRomFS folder, which contains the graphics, fonts, movies, sounds, some info for the game maybe item IDs (?) and the text.

![extractedRomFS]({{site.baseurl}}/assets/maple/firstSteps/extractedRomFS.png)

Graphics are in the folder "Inactive" and the text is in the folder "Stage":

![Inactive]({{site.baseurl}}/assets/maple/firstSteps/inactive.png)

![Stage]({{site.baseurl}}/assets/maple/firstSteps/stage.png)

As you see the Stage folder has many subfolders and it's hard to find the right folder, and not all of those folders contain text files.
If we enter the first folder, we see this:

![Global Folder]({{site.baseurl}}/assets/maple/firstSteps/globalFolder.png)

The .gmm files are the files that contain the text, I don't know what the other files do, but that's okay since I'm just translating the game.

Let's open the Help_title_JP.gmm file, this is the one I translated first, and showed on GBATemp:

![Help Title]({{site.baseurl}}/assets/maple/firstSteps/helpTitle.png)

Those files look like this, and simply editing them with a text editor is enough to change the text ingame.

Alright, but I think that's enough for this post, if you thought it was interesting, check back later.
Thank you for reading.