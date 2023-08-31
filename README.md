[submission link](https://docs.google.com/forms/d/e/1FAIpQLScqspv9M2VhEMZKcgY63FzYiWyErBZRzwlr6rHy4aniuMjwIA/formResponse)

# Report 1 - Week of 08/28/2023 #
## Reflections
It's been a rough first week! I haven't been in school for 9 years, so it's been quite an adjustment. The information fire-house is real, as is learning how Berkeley works; how to shop for classes, get around, get exercise (pro tip: don't go to the gym after 5pm - berkeley has 1 gym for all undergrads & grad students and the line was around the block).

Getting back to using github is fun. I like Github, I like Markdown. I never got particularly good with markdown but I know many folks who use it as their primary note-taking means. One thing that is a little annoying is adding images into markdown. The easiest way by far is to copy paste. This screenshot, for example, I simply coppied & pasted here: 

(this is a screenshot of a litle car I modeled while learning Rhino)

![little rhino car](https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/d32814d8-8818-4b53-86c4-8afa990606b0)

Interestingly, if I past it _directly_ from the screengrab, it uploads as an html style <img> tag instead. I'm not sure why.
<img width="1396" alt="little rhino car" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/7625467f-caab-4ae4-9ccc-a3d2e6a7bbcd">

In both of these cases, it's easy to upload the image. What I don't like, is that githup uploads the image to an "assets" folder which is not viewable in the github web GUI (not quite sure why), and I'm not able to change the filename after the fact. If i wanted to download all images from a particular week, it could be a somewhat manual process.

Alternatively, I can upload the image manually, then reference it:

![making a little toy in rhino](../2028_08-31_assets/Screenshot 2023-08-31 at 2.32.27 PM.png)

Oops! I used the wrong relative path. Lets try this: 

![making a little toy in rhino](/2028_08-31_assets/Screenshot 2023-08-31 at 2.32.27 PM.png)

still not right. How about this:

![making a little toy in rhino](2028_08-31_assets/Screenshot 2023-08-31 at 2.32.27 PM.png)

well, honestly I'm not sure why this isn't working. Maybe the spaces in the filename? But, I'm not going to work to figure it out right now - even baring these bumps, manual upload like this is a lot more work for each image, especially screengrabs, as I have to save them to my computer, then upload them, then copy the file name and reference the min markdown (and debug), and if I reorganize my assets at any point I'll need re-reference them in markdown. Also, github only lets you edit image filenames from the command line. Given all this, I think I will stick to option 1 or 2 - direct copy & paste, letting github handle the uploading.

Rhino has been more difficult to learn than I anticipated. I used to do a lot of 3d modeling in Solidworks, but that was 7 years ago. I've recently tinkered in onshape but it has similar UI to solidworks and the interface is desigend to be easy to learn for new-commers (because it's a newer software, they're working hard to pull people off of these very sticky products, I think). I know all the _concepts_ of CAD modeling, but learning where the tools exist has taken some time. Also, working on a 13" MBP is challenging - I have a large monitor at home but have mostly been working on campus in between classes. Maybe I can store one in jacobs descreteley? 🤔 Another challenge is the instructional videos are oriented towards windows users. This is not so bad when you're trying to learn concepts, but when you're trying to get up and running quickly the layout of tools is important.

All that being said, I'm starting to get the hang of things - see the little car I modeled above!

PS I made my first arduino circuit today! very exciting. It simply blinks an LED light:
![arduino first project](https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/f1f9080a-315c-4811-bbf7-cc98ee5300d9)

Speculations
I think that CAD is going to be increasingly "intelligent". It's a complicated space, but much like mid-journey/Dall-E exist for image generation, and figma is starting to use AI design assist, there's no reason this can't be extended into 3D space. I've heard about but haven't played with generative design (I know solidworks now has plugins for this) where you specify a connection and the stresses you want it to be capable of withstanding and the software autogenerates a handful of different solutions that it thinks will work. You can then pick and refine. I also think that aesthetics can be learned as well; there's no reason that AI can't create interesting aesthetics as well as humans. It's still a ways out before it's the norm - 10-20 years I would guess, simply because it's a complex problem space, and humans remain _really smart_. 
