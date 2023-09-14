# Report 3 - week of 9/11/2023
## Reflections
I’ve finally gotten the hang of Rhino and grasshopper. I started going through video tutorials on Rhino.com, but I found that for things like this I learn much better with written tutorial where it’s easy for me to skip over parts I already know, and where I don’t constantly have to pause the video and rewind and say “where exactly on the screen did they click to bring up that tool?” The downside of written tutorials is, of course, that there are fewer of them (it’s much easier to record a video of you doing something than it is for you to write it up in a detailed document with images and instructions).

I built a couple small things to test my knowledge.

<img width="250" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/2695a21b-9d8d-47d0-bad7-7d0e8f44abc9">
<img width="250" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/6a2341e4-0313-4f99-9a2c-187c520eebaf">

I also found it incredibly helpful to work alongside Cody Glen, one of the design specialists at Jacobs, who could help with the annoying little things that google or chatGPT have a hard time answering e..g (how do you do push-pull manipulate a surface in 3d after you’ve created it from a sketch? - turns out the keyword I was missing in my search was “control points”). 

For learning grasshopper, the [grasshopper primer](https://github.com/modelab/grasshopper-primer/blob/master/_downloads/GrasshopperPrimer_V3-3_EN.pdf) was incredibly useful, though I didn’t have a chance to get through more than the first 2 sections. Again, sitting and spending half an hour with Cody was incredibly helpful.

To combine Rhino grasshopper in a fun and useful way, I went about creating a bike lock holder for my electric scooter. I got an electric scooter to get to classes (unfortunatlye, I have a pinched nerve in my back that gets aggravated biking up-hill, and berkeley is, well, on a hill). I also got a U-lock for my scooter, but unfortunately, the lock holder it came with doesn’t work as well for a scooter as for a bike - the primary problem is that it holds the lock on one side, so that it sticks away from the the frame. This means it’s either poking me in the crotch (current setup) or sticking out awkwardly in front, making it difficult to cary the scooter when folded

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/4c2a6c70-094b-4148-bb05-ccfae0f17151"> 

Above, scooter with lock, a.k.a. crotch hazard

So I wanted to make a holder for the lock that would hold it tangential to the scooter’s stem, like so:

I sketched out some ideas:

<img width="250" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/2713ed3d-6f42-463f-9ffb-dfcba1209997">

<img width="250" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/45e4f551-6946-4f2d-bd56-ab2d47d36e27">

I also looked up bike light holders, to see what current attachment mechanisms exist and are common. 

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/59d03d40-3e5b-4853-8e67-d4ab05706f51">

I ended up deciding to use a mechanism with a bracket and a rubber strap that hooks onto either side of the bracket and wraps around the stem.

And set about modeling the simplest version of the bracket in Rhino as possible. 

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/7d257bee-5934-4c53-bf98-f2d9616f8178">

Then I went to create this geometry in Grashopper. This is where 💩🥊🪭. Every line I had so casually drawn in Rhino became a painstaking series of commands in Grasshopper. Grasshopper repeatedly failed to do simple things (like caping a hollow surface) for reasons neither I nor Cody could exactly ascertain, so in some instances I had to come up with totally different modeling techniques.

Finally I have a 3d model in grasshopper, where I can manipulate a couple variables (like the stem thickness and the bracket height) to modify it as needed. Next step - to print!

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/9a50c05f-53f5-4543-9b36-6c37c93afe58">


## Speculations:
I can’t imagine that designing a simple part like this (and making it “parametric” in a fashion) is the best use of grasshopper. I know architects love rhino and those architects into computational design love grasshopper - and I’ve seen some pretty cool things created with it. But for something like this, a parametric modeler like solidworks or onshape would have been a thousand times simpler - simply sketch the geometry, pull out some of the sketch values as variables via an API (cody said solidworks has something like this) and boom everything regenerates. Even for a very simple part like this, the grasshopper file is massive and unwieldy. I haven’t even added basic finishing touches like fillets, or non-right angles. And yet more than once I found myself going cross-eyed trying to find a particular feature. 

<img width="1000" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/be05626e-6be2-493c-9269-08b1550d0e9e">
above, My full grasshopper file

I’m excited to do more complex things in grasshopper - I can see how it would be useful for patterning and manipulating larger datasets (matrices of points, generating patterns or organic shapes). This would all be very hard in a parametric modeler. I’m also excited to try some of it’s scripting tools.

I also wish I understood better why rhino is so popular with architects. Does it just have a lot of architectural plugins? Is it because architects like the complex surface modeling tools it has? Can it handle large complex structures better than solidworks? Or is it just inertia - it’s been around and dominant so people know how to use it? I would think parametric modeling like solidoworks, nx, onshape etc would be super useful in architecture, but maybe not. Maybe they’re slower to use if you’re building complex things that you want to manipulate a great deal (unlike machine components which often have very clear constraints).

Working on this project has also made me realize how much time and effort go into the simplest of products. It would take me weeks to get my bike lock holder to the same polish of finish as the one kryptonite sells.


# Report 2 - Week of 09/04/2023 #
## Reflections
I've made some progress getting my feet under me. Have a better grasp of grashopper as an interface; it seems like it's really a visual programming tool, sort of like and old school turing machine. Having worked as a software engineer for a while this makes sense to me (thought it's a little frustrating, as I really want it to have the full capabilities of a programming interface - for example, the ability to set environment variables would be incredibly helpful. I've heard that you can write python scripts to interopt with grashopper so perhaps that will be a challenge for next week.

I've also got trained on the Tormach CNC mill, hopefully something useful. Despite being a simple machine in principle (spinning bit, moves in 3 axis) it has a number of nuances and complications to be aware of. For example, there are 3 different types of tools/tool holders: from left to right: chucks (for drill bits), set screw tool holder for HSS (high speed steel) mills, and collet nuts (for carbide, smooth shank mills).

<img width="300" alt="tool holders for Tormach" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/9dc45e9d-ec78-4d3a-9411-31c528174fd5">

I also got a lesson in drawing this week, learning about single point and multipoint projection. I'm super excited to get better at this - honestly, having worked in industry some and talked to older designers & engineers, being able to quickly sketch your ideas is increadibly useful. 

<img width="500" alt="single point perspective" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/74e139cf-4d5c-40ba-9535-0a332b85f1a0">
<img width="500" alt="circles spheres and elipses" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/0edad189-7f47-457a-8da5-508312ed8201">
<img width="500" alt="trying out some real world objects" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/a147b583-1774-4a57-a62d-693269591701">

I also got an esp32 and got it flashed and connected to WIFI! It uses micropython and this funny IDE called Thonny
<img width="500" alt="esp32 wifi script" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/073e03ae-ce61-49a6-a0ec-108836a67a63">
(don't worry, i didn't actually set my password to PASSWORD).

## Speculations
I'm glad to have front loaded some of the shop-trainings; I think I will thank myself in the future. I still want to complete the industrial sewing machine training as I think soft good often get overlooked by technology focused designers (which means they are ripe for innovation!). I need to make more progress on rhino and grasshopper. 

I'm not sure I buy the whole "everything will be customizable just to you" future of manufacturing. The reality is many things, we don't want customized, and aren't willing to pay 50 cents more to have it so. And while 3d printing etc get's easier and easier, so to does injection molding and mass manufacturing - these are not "static" technologies. One area where customization could be incredibly useful is in the medical field. Orthotics NEED to be customised. With the advent of better cameras in phones, it's not inconcevable in the future that people could scan their own body parts (i.e. a foot missing a toe) and get orthotics mailed to them. I met a PhD INFO student with a background in biosensors (who I may work with on a project, TBD). Hope to learn from him and others like him while at Berkeley.

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

## Speculations
I think that CAD is going to be increasingly "intelligent". It's a complicated space, but much like mid-journey/Dall-E exist for image generation, and figma is starting to use AI design assist, there's no reason this can't be extended into 3D space. I've heard about but haven't played with generative design (I know solidworks now has plugins for this) where you specify a connection and the stresses you want it to be capable of withstanding and the software autogenerates a handful of different solutions that it thinks will work. You can then pick and refine. I also think that aesthetics can be learned as well; there's no reason that AI can't create interesting aesthetics as well as humans. It's still a ways out before it's the norm - 10-20 years I would guess, simply because it's a complex problem space, and humans remain _really smart_. 


--------

[submission link](https://docs.google.com/forms/d/e/1FAIpQLScqspv9M2VhEMZKcgY63FzYiWyErBZRzwlr6rHy4aniuMjwIA/formResponse)
