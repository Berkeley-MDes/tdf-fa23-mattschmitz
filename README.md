# Report 10 - week ending 11/2/2023

## Reflections

### Zerowidth
The goal of this project was to create a "mini-me" chatbot using Zerowidth's API. 
I first created an instruction set

<img width="764" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/8ffe6bac-4b82-46bf-b210-30ad5e1018f3">




# Report 9 - week ending 10/26/2023

## Reflections
We got a bit of a rest from two projects due last week, and kicked off Monday talking about LLMs and Zerowidth, a no-code (or code-optional) interface for working with LLMs. Among other things, it provides a stardardized wrapper for you to provide GPT4 with initial instructions customizing the context of the bot (i.e. "you are a reincarnation of doctor suess, designed to help people rhyme", and a means to hook up the bot to a particular dataset that GPT4 may not have already been trained on (for example, all the poems I matthew have written). It has it's limitations - the biggest being that you are not actually retraining the model, so you are subject to the token limitations of GPT4's api (something like 8k tokens max). So I can't use it to create a chatbot that's been trained on all of my writings because that exceeds 8k tokens. But I can allow it to look up at any time and incorporate a particular peice of writing I may have written. I'm still playing around with it, but I like the interface.

Playing around in zero width's workbench:

<img width="1071" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/a02adc92-d8d5-4b67-82d5-e0adda836bbc">


## Speculations:
I think we will see more and more tools like this come up. Could you use a language model to train a language model? If so, you could have non-software engineers building really specific & sophisticated products. I do think that as a business proposition there is a risk with a lot of this stuff that if anything becomes too successful OpenAI may just replicate its functionality. For example, there is a company called Boomerang that created a plugin for gmail that allows you to 'snooze' emails until later, and schedule sent. Gmail has since built both of those products into it's app, so I stopped using Boomerang and I imagine many others did as well. This may not happen if we truly get many large competing LLMS, but it remains to be seen whether that will happen or whether this technology will go the way of search engines where basically one player controls 90% market share. As it stands Anthropic and Google have not managed to produce anything quite as good as ChatGPT, and no one wants to use the second best LLM...

# Report 8 - week ending 10/19/2023

## Reflections
This week was a hurculean effort to get all the different components of the wire bender project working together, but there were a lot of moments of triumph. The first triumph was soldering up a featherwing for servo motor control and getting the correct code to control the servo motor. Soldering the featherwing was tedious but I now feel confident in soldering which is great - thanks to Sudhu showing me a few tricks. I had always struggled to solde previously and did not realize that it was due in a large part to using soldering irons with bad tips - if you're using a communal soldering iron that hasn't been well maintained then you may need to do a lot of scrapping and tinning to get it to a usable state again. Figuring out the right code to run on the featherwing was also a bit tedious - it turns out the adafruit has it's own library for the featherwings that controls the servo, and finding example code can be a bit tedious because you need to find wiring (not circuit python) code and in some cases code specific to particle (not arduino). The ecosystem for arduino is just much more developed than for particle. I also learned that servos use PWM to control their movement, but not in the same way that pwm is traditionally used (to mimic an analog signal). 

It also turns out that different servos respond differently to different pulse widths, and those pulse widths are not entirely defined by the datasheet - there's a lot of real world variation. 

The second big win for me was powering a DC motor via a transistor. The DC motor we had is a 24V motor from a mig welder (powering the wire feed mechanism). I used a mosfet to control this which is the first time I've ever done something like this. 

There were other small challenges that were rewarding to get past - figuring out how to get input from the serial monitor (to test with) was remarkably challenging - I'm not used to working with such a low level library as C++ where little things like splittin an array sometimes require you writing your own helper function. I've also run into some issues with types and pointers that reminds me of the few CS classes I took many years ago (and have mostly forgotten). 

Here's the full code for the particle powering the the motor and servo. 

```
#if defined (PARTICLE)
#include "Adafruit_PWMServoDriver.h"
#else
#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>
#endif

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver(); // called this way, it uses the default address 0x40
#define SERVOMIN  80 // this is the 'minimum' pulse length count (out of 4096)
#define SERVOMAX  590 // this is the 'maximum' pulse length count (out of 4096)
uint8_t servonum = 0; // our servo # counter

// Serial input variables
int intReceived; // integer input from serial monitor
char serInString[50];  // array that will hold the different bytes of the string.
char command; // command from serial monitor
char commandEnd[50];

int publishedDistInt; // distance to move servo - from publisher (pulse width)


void setup() {
  Serial.begin(9600);
  Serial.println("starting up");
  pinMode(D15, OUTPUT);
  pwm.begin();  
  pwm.setPWMFreq(60);  // Analog servos run at ~60 Hz updates

  setFeeder(0); // turn wire feeder off (0  = off, 255 = full power (max))
  // servo goes from 80 - 590
  moveServo(SERVOMIN); // move servo to start position

  Particle.subscribe("distance", moveServoSubscribe);
}


void loop () {
  memset(serInString, 0, 50);  // clear the string

  //read the serial port and create a string out of what you read
  readSerialString(serInString);
  command = serInString[0];

  if( command == 's' || command == 'f') {
    // Serial.println("command recieved: " + String(command));
    copyStringMinusFirst(serInString, commandEnd);
    // Serial.println("second half is: " + String(commandEnd));
    intReceived = atoi(&commandEnd[0]);
    // Serial.println("converted to in: " + String(intReceived));

    if(command == 's'){ // command for servo
      Serial.println("servo command recieved, setting PWM to: " + String(intReceived));
      moveServo(intReceived);
      // set servo to value entered
    }
    else if(command == 'f') {
      Serial.println("feeder command recieved: " + String(intReceived));
      setFeeder(intReceived);
    }
  	serInString[0] = 0; // indicates we've used this string - maybe not needed anymore
  } else {
    // Serial.println("unrecognized command recieved: " + command);
  }

  delay(100);  // wait a bit, for serial data
}

void moveServo(int distance) {
  Serial.println("moving servo to: " + String(distance));
  pwm.setPWM(servonum, 0, distance);
}

void setFeeder(int power){
  if (power < 256) {
    Serial.println("setting feeder to: " + String(power));
    analogWrite(D15, power);
  } else {
    Serial.println("invalid feeder power: " + String(power));
  }
}

// used to map published input to servo distance
void moveServoSubscribe(const char *event, const char *publishedDist) {
  Serial.println("mapping servo");
  Serial.println("event: " + String(event));
  Serial.println("publishedDist: " + String(publishedDist));
  publishedDistInt = atoi(publishedDist);
  moveServo(publishedDistInt);
  Serial.println("done moveServoSubscribe");
}

//read a string from the serial and store it in an array
//you must supply the array variable
void readSerialString (char *strArray) {
  int i = 0;
  if(!Serial.available()) {
    return;
  }
  while (Serial.available()) {
    strArray[i] = Serial.read();
    i++;
  }
}

void copyStringMinusFirst(char *input, char *output) {
  int i = 0;
  while(input[i] != '\0') {
    output[i] = input[i+1];
    i++;
  }
  output[i] = '\0';
}

```

wiring setup with transistor, featherwing, indicator LED, diode for back-current protection. brown and black wires run to the DC motor: 

<img width="460" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/16ab5a35-7944-428a-90b5-d7b2b72f8a92">



## Speculations
I've been thinking a lot about 2 things this week. One is the challenge of parallelizing work on a team. I would ultimately like to run my own business and I know that in order to be successful I will need to be effective and delegating and organizing a tea. This week was a challenge because our system is tightly integrated - figuring out how to split up work was not easy. I think had I been more assertive early on and putting time into dividing up responsibilities that would have been helpful.

The other thing I've been thinkin about is ecosystems. Particle is slightly more challenging to work with than Arduino because it doensn't have as developed an ecosystem. At what point should you adopt a new technology? How much do you trust yourself to read the tea-leaves and know which particular variation of a product (in this case, microcontrollers) will catch on and which will stagnate? I know from experience (stripe built much of it's platform in ruby, a language which has since fallen out of favor, and later decided to migrate largely to Java mainly for the ecosystem benefits, a process still underway) that working with a technolgoy that doens't have a well developed or growing ecosystem is very challenging.


# Report 7 - week ending 10/12/2023

## Reflections

This was the first week of actually working on our team project. It has been maddenningly slow. After splitting into our separate project groups there has been more coordination & meetings and documenting and overhead and now we have a slack channel and a figma and a google doc and calendar invites... which is all good, but for a 2 week project, it feels like we've put in a lot of time without a ton of time on the hardest part of this project- the actual engineering/circuitry/code. Another unfortunate challenge has been that our first order of parts - including a wire bender we wanted to test out - was lost. So we've ordered another, but that has set us back a bit. I also think that it's been challenging doing synchronous work - this has been a hard project ot split into different subsections given peoples different strengths. We are making progress though. We got the small servo motor wired and working - and learned how to install external libraries with particle and the particle workbench. We tested out using a chisel as a "wedge" and using the DIWIRE with 1/16th inch steel wire and were able to get some interesting spirals. 

<img width="566" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/e1b316cf-2f26-4e69-93a6-8831fbcaab69">

We've landed on a very basic design for our prototype, and have bought the materials needed to begin fabricating. My biggest fear is that the wire feed mechanism we've bought will not be sufficient to drive the wire forward and we will need to build that from scratch. This is not a hard problem to solve but given the time constraints and the team background it may be infeasible. There are also so many things I would love to do that we haven't touched on - webhooks and more IOT type applications. so it goes.

## Speculations
Working asynchronously on hardware projects is hard, I wonder if there are better tools or best practices out there to help with this. I'm also super bummed I wasn't allowed to take a mechatronics design class (ME department told me no grad students were allowed in the class) - I would have really enjoyed it and it would have been very relevant to this project. I also think, like with many robotic applications, that the work is more and more about what the software can do - can the software effectively map the behavior of physical materials to output? Perhaps there is a role for ML in this - I've often thought that with some machining problems, if you had enough funding, you could just brute force solutions to control problems - i.e. have a robot bend wire in hundreds of different ways, measure the resulting wire shape, and then map that back to the robot - instead of trying to deterministically map input and output. That's a bad example but I'll think of another one.


# Report 6 - week ending 10/5/2023
## Reflections
Suprisingly, 5 people were interested in working on a sculptural wire bender with me. Unfortunately, I must not have done a good job explaining in writing what I had in mind, because when I explained in person most people responded with "oh, that's not what I had in mind". I think folks had thought I meant simply to creating an interactive art installation. After much discussion and deliberation we've split into 2 teams of 3 though and are moving forward. We are doing something halfway exhibition halfway tool.

In this process I also had a number of other intersting ideas that I might want to work on at some point:

- what about a cnc hammar that can indent thin sheet metal, to form faces etc?
- From an installation perspective, I like the idea of ephemerality. Could you have a wire sculpture that when users get close, straightens instelf out, only reforming when they are a far aware? You could do this by physically bending/unbending wire, or, if it exists, you could use a material with some degree of memory - for example, perhaps when you heat up the wire, it looses it's bends, but when cooled remembers them. (googling and looks like this exists - called nitinol wire. You could controll the heat of the wire by running a current through it, or braiding it with a wire of some known resistance which you run a high current through in order to heat it. 
- etch-e-sketch style yarn drawer - could you tie a piece of yarn to a round magnet, mount it to a board with lots of small pegs, and then with a 2d drive system on the back of the board, trace a shape with the yarn (think like the game snake).
- Similar idea of ephemorality with the above concept - it would be cool to have a work of art (say made from yarn on a peg board, or embrodery needle through a thick cloth) that unravells given some trigger (users leaving the art work, for example) or visa versa. I like the idea of a sweater unraveling when you pull a single loose thread. 

Back to the wire bender - I figured a first step was to understand existing tool in this space. We have a DIWire in Berkeley's Makerspace, so I've been working to get that up and running. First it required making a power cord (thank you Gary) since the old one was lost. Then I learned that the software is not free or open source, so we needed to find the machine with it instealled. After getting all that (and working through a few other kinks) I finally had my first bend!

https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/6a0b2bd7-16f6-4441-8590-1c8e4634d028

Then I've been trying to understand how the software works. Unfortunately, it doesn't just create and send a G-Code file, which would be super nice. Instead, it sends data via a usb (acting as a serial port) to the machine. You unplug the USB cable mid way, and the machine stops working.

The code for "wireware" isn't open source, so I couldn't find what it's doing under the hood. I did find this 11 yo repo: https://github.com/diwire/DIWire-Bender and this 6yo repo which give some hints: https://github.com/FoxDotBuild/DIWirebender.

I ended up installing a software based usb sniffer (never heard of such a thing) that could listen to the USB port. Indeed, I was able to see bits of G-Code being sent to the machine, and some data being sent back! 

Sniffer (left) and "Wireware" (right):

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/5a313775-9dd3-49e7-8d44-0da618f25a83">


My next step is to see if I can control the machine directly by sending G-code from a python script or similar. Unfortunately the python installation on my laptop is royally screwed up (from using pyenv several years ago and then overriding it while following instructions for a circuits course I'm taking).. so first step will be to fix that.


## Speculations
I may have mentioned this before, but I don't know if the "every man(person) a maker" movement is really a plausible future. I think the flaw with this thinking is that the easier things get to make, the more complex we expect things to be. It is now fairly easy for anyone (particularly with the help of ChatGPT) to create a simple program that would have blown peoples minds in the 1960's. But instead of a world where everyone programs, we have a world where, while programming is much more common, what we expect from programs is much more - we expect seamless and sophisticated software. I think there is a similar analogy with phyiscal making - 3d printers have make it easier for people to prototype, but instead of everyone having a 3d printer in their home, the quality and speed of development of phyiscal goods has just increased - For example, drones technology is advancing very quickly in part because it's super easy not to quickly prototype new parts for them. So our expectation of what a drone we buy can do relative to just a few years ago has increased.

# Report 5 - week ending 9/28/2023

## Reflections

I love that we're working with our photon2. The photon2 is a unique microcontroller becuase it requires wifi connectivity to function. I knew this in theory, but could not for the longest time figure out why I could not get it to work at home - until I realized that I set it up to work on Berkeley's IOT wifi - not on my home wifi. This is honestly kind of annoying and strikes me as a design flaw, but, like many "obvious" design flaws, there are probably some good reasons it was made this way, or at leats it probably wasn't as simple as "they didn't think about it". The software, IDE and community seems to be pretty solid. I got vs-code set up, which is exciting as I used to do a lot of coding (I was a software engineer at Stripe for almost 4 years) and so I'm excited to get back into that. But it also reminded me of a) some of the things that I most hate about coding - namely that you can sink hours into unintellible error messages and breakages with sometimes no clear path to resolution other than googling & searching on stack overflow (or now, asking chatGPT), and b) how much setup is involved in having a productive coding environment. All the little hacks and shortcuts (bash profiles, shell scripts, ssh shortcuts, extensions etc) that I relied on to be an efficient programmer all have to be re-set up. Little tools like nodemon I used to use often I've forgotten how to use.

The project I want to work on with my photon2 is creating a robot wire bender. Kind of like the DIWire, but I had the idea to make one that could do more sculptural work, using wedges to bend the wire instead of articulated sharp angles.

In 2d, it could simply bend wire in a circle:

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/c63d8ace-9e3a-4ce0-ac6f-073a444b33dc">

But with a second wedge, you could created really interesting sculptural spirals:

<img width="500" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/43657982-70af-4500-b157-84f6e190e164">

We will see if anyone has an interest in working on this idea with me

## Speculations

One thing I am really excited to try is coding with chatGPT and github co-pilot. I just got access to github copilot. When I was at Stripe, neither of these existed (github copilot had been introduced, I think, but wasn't common yet). I think that these tools are really going to make coding and software more accessible to the vast majority of folks. This is unique in many ways to the software domain, because software is ALL COMPUTER READIBLE. Think about that for a moment- what other profession could almost the entire body of work be computer readible? Mechanical engineering certainly isn't. Design isn't. I can ask chatGPT about mechanics and it will have read a couple thousand mechanical engineering textboks or papers, but it can't physically work in a shop and look at materials or feel what it is to press on a haptic system. I understand that software interacts with hardware so for some cases like working with embedded systems it gets more complicated - but for the vast majority of cases, everything that is coded can be read by a computer. This is a huge advantage in terms of training the technlogy.


# Report 4 - week of 9/18/2023

## Reflections

Finally got my project 1 done! I was very proud of 3d printing something for the first time, tada!:

<img width="558" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/93f0bdc7-be85-421d-bf6b-1bc026a7b908">

My first print attempt was uncessessful, because the extruder for the breakaway was clogged. I dediced to reprint using the same print head for the part and the breakaway. You'll notice in the above photo that I oriented the part with the arch inverted - The reason for this was because I wasn't sure how precise the printer was at a normal speed/resolution setting, and I knew that the arch portion of my print needed to be pretty precise to fit onto the bar of the u-lock. So I opted for a less efficient print shape in order to ensure a good surface finish where it counted. However, it seems that the printer is more accurate than I anticipated, and this wasn't necessary - even going at a normal speed (not fine) I was impressed with the print quality. 

Here is the part in use on my scooter:

<img width="400" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/cf1d9745-6548-4579-b450-e32337faa125">

<img width="400" alt="image" src="https://github.com/Berkeley-MDes/tdf-fa23-mattschmitz/assets/23087383/926df596-3af5-45ab-b72b-6ce4fb767fa3">


In other news, we got our photo2 kits! Very excited to get started with it - I am using an esp32 for another class and it is a royal pain, but also fun :) I do not love the super low level coding - I am familiar with high level languages like javascript, ruby, scala but not as much with micropython. 

# Speculations
The impact of 3D printing on the ability to quickly prototype parts is hard to underestimate. If I were working 20 years ago and wanted to prototype this part, I'm not sure what I would do - I would have to machine it. 
The bigest limit to 3d printing today is materials - it's still mostly limited to plastics, unless you have a lot of $$. There are environmental consequences to this as well - wood, clay, other materials that are (often) more ecofriendly don't lend themselves to printing. I think the next breakthrough in rapid prototyping will be in materials. What if you could 3d print wood, or a material entirely naturally derived? The types of products people can prototype affects the types of products they produce - people use plastic not JUST because it is cheap and light but also because it is accessible - how many people have a metal shop at home, or the ability to quickly machine complex metal parts?

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


# Report 2 - Week of 09/04/2023
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

# Report 1 - Week of 08/28/2023
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
