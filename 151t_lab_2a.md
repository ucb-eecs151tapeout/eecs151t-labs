# Lab 2a: Creating your RISC-V Core Generator

## Summary So Far

In Lab 1, you learned Chisel and (probably) blindly went through Chipyard setup. We talked a little bit about Chisel, VLSI flows, and Chipyard in lecture one, and then dug a bit deeper into SoC design and Rocket Chip in lecture two, particularly focusing on the SoC component hopefully most familiar to you from EECS151LA - the RISC-V core. You no doubt still have many questions - that's okay! Hopefully our meet-ups will ease you into this chaotic infrastructure smoothly, but please do let us know when you encounter bugs or confusion. Now, we would like to start the process of integrating your own core into the brand new SoC. 

### Chipyard & Rocket Chip Overview

So, we've asked you to setup Chipyard. What in the world did we make you clone?! In this lab, we will use the [Chipyard](https://github.com/ucb-bar/chipyard) framework a bit more, but don't worry about it much yet. As you read the documentation, you will see many diagrams, such as the one below, each of which seems to have more and more new components. Again, don't worry about trying to explore everything at once. For now, try to go through the excercises to get practical Chipyard experience. You will dig into Chipyard components much more later. 

![](Lab_2_assets/chipyard-flow.png)

**To start - what is Chipyard?**

Chipyard is an integrated design, simulation, and implementation framework for open source hardware development developed here at UC Berkeley. It is open-sourced online and is based on the Chisel and FIRRTL hardware description libraries, as well as the Rocket Chip SoC generation ecosystem. It brings together much of the work on hardware design methodology from Berkeley over the last decade as well as useful tools into a single repository that (aims to) guarantee version compatibility between the projects it submodules.

A designer can use Chipyard to build, test, and tapeout (manufacture) a RISC-V-based SoC. This includes RTL development integrated with Rocket Chip, cloud FPGA-accelerated simulation with FireSim, and physical design with the Hammer framework.

(As you learned in Lab 1) Chisel is the primary hardware description language used at Berkeley. It is a domain-specific language built on top of Scala. Thus, it provides designers with the power of a modern programming language to write complex, parameterizable circuit generators that can be compiled into synthesizable Verilog. 

Throughout the rest of the course, we will be developing our SoC using Chipyard as the base framework. 

There is a lot in Chipyard so we will only be able to explore a part of it in this lab, but hopefully you will get a brief sense of its capabilities. 

Note that while we talked about the *Rocket Chip* ecosystem in lecture, *Chipyard* (the name) is often used near interchangeably, and we will say *Chipyard* from now on. 

## Getting your CPU into Chipyard!

### Import Your CPU

Unlike the `RocketConfig` example, we don't care as much about generating a Rocket Core - we want to integrate our own! 
Luckily, a group of besties called the Shadow Council has written a magical adapter that allows the EECS151LA core to speak the language used on Chipyard-generated chips - TileLink. More on this in the next lab parts. Let's first figure out how we can get your core into Chisel's crosshairs. But before that, we need to find and condense your EECS151LA RTL into a more easily-digestible format (i.e. a single file we can point to).

**Step 1: Go to `$chipyard/generators`.**

You will see a whole bunch of [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules), each pointing an open-source generator project. Generators are organized as submodules to decouple their development from a specific Chipyard branch/fork/version.

One of these generators, `generators/ofo` (one-fifty-one), contains the adapter you'll be hooking your core up to! Let's head there right now. While we're here, let's create a branch for you to work on!

```
cd generators/ofo

git checkout -b ${USER}-working # create your own branch!
git push --set-upstream origin ${USER}-working
```

**Step 2: Create your project folder.** 

Since your core RTL is Verilog instead of Chisel, it is better to organize it in a special folder. We have already created for you a conventional directory structure. Find this folder now. From `generators/ofo`:

`cd src/main/resources/vsrc/cores`

The filepath *has* to match, else flows may break person to person. 

**Step 3: Import your core.**

First, find your EECS151LA project repository on GitHub.
Once you have the GitHub link, add it in `vsrc/cores` by running `git submodule add <link>` from within `src/main/resources/vsrc/cores`.

Note you might have to copy over your local ssh keys to make permissions work, since it's likely a private repo.
**DO NOT MAKE YOUR EECS151LA CLASS REPOSITORY PUBLIC!!!!** The professors will get mad at us :(

Let us know if you have issues with this step.
In particular, if something has happened to your core (or it was incomplete),you are allowed to use another student's - just point to their repo instead.
You still need to do Lab 2 by yourself, you're just allowed to use someone else's EECS151LA repo in this step.

You should now have a folder like this exist:

`generators/ofo/src/main/resources/vsrc/cores/name-of-my-core/`

With the directory structure:

```
 $chipyard/
  generators/ <------- library of Chisel generators
    ofo/src/main/resources/vsrc/cores/name-of-my-core/
      src/
      tests/
      Makefile
      README.md
      *.yml files
  ...
```

The structure within your core file will differ depending on what semester you took EECS151 in.

**Step 3.5: Please add @elamdf and @Fi50 (the course staff) to your EECS151LA repo so we can help debug if you get stuck.
You can do this from the repo page on GitHub in Settings -> Collaborators -> Add People.**

**Step 4: Commit and push your changes - then [Gradescope] submit your absolute core filepath, including the machine you're working on.**

Remember that `scratch` directories aren't shared between machines!

### Condense Your Verilog 

To our knowledge so far, "black boxing" in Chisel only works for one Verilog file at a time.
However, independently integrating every piece of your CPU would be painful.

So instead, we (specifically: elam) wrote a Makefile that will turn ("preprocess") all of your RTL into one very, very long Verilog file. It also uniqueifies symbols (variable/module names) and expands macro paths.

**Step 1: Read the Makefile in the `vsrc/cores` directory within your generator.**

Specifically, in `src/main/resources/vsrc/cores/`.

**[Gradescope] Why do we need a Makefile? Can't we just run these commands once and be done?**

**[Gradescope] What make command would allow you to easily remove the generated Verilog, for example, if you're debugging?**

**Step 2: Change `proj_name` inside the Makefile to point it to your specific core.**

Or more specifically, to your project folder name. In the above example, I would write:

`proj_name = name-of-my-core`

**Optional: Take a closer look at the Makefile! Improve it?**

If you don't have `make` experience, this is your chance to see what's behind the "magic" in your regular classes! That is... there's no magic at all, just a bunch of very hack-y scripts. You will see more robust Makefiles used throughout Chipyard.  Creating robust (i.e. non-hacky) Makefiles is always a tradeoff - they usually take more effort to create, but that effort is justified if the Makefile will be broadly used (such as in a critical Chipyard build step).

There's a bunch of "TODO"s in our file. If you end up getting bored and want a challenge sometime, you can try to make improvements to this part of the flow - but we won't make (pun intended) you suffer through that!

For example, how may you use what you might've learned in EECS151LA Lab 1 about Makefiles to optionally pass in the project name rather than hardcoding it each time?

**[Gradescope] Leave a note if you decided to experiment with the Makefile.**

**Step 3: Run the Makefile**

In the directory you added the Makefile to, run `make`. 

**Step 4: [Gradescope] Submit the path to your generated Verilog.**

You can check the Makefile to figure out where the combined verilog file gets placed if you can't find it. Or you can ask someone, but where's the fun in that?

Congratulations! Your core is now one step closer to integration. But the resulting file is still Verilog. How do we turn it into Scala?

### Chisel Black Box Integration

We will now explore how the magic adapter finds and hooks up your core!

In Chipyard generators, Scala RTL is located in `src/main/scala`.
There is no law saying to follow this structure, it's just just a convention that makes things more consistent. :D
Consistency and conventions are key to enabling large heterogenous projects like Chipyard with multiple contributors to come together without (excessive) friction!

We keep talking about this idea of a "Black Box". We brought up Chipyard documentation in lecture a little bit, but didn't go into details.

Thankfully, Chipyard has a whole page dedicated to **6.4. Adding a custom core**:

- https://chipyard.readthedocs.io/en/stable/Customization/Custom-Core.html

In our case, **6.4.1. Wrap Verilog Module with Blackbox (Optionall)** is *not* optional. Take a look at that part of the documentation:

- https://chipyard.readthedocs.io/en/stable/Customization/Incorporating-Verilog-Blocks.html#incorporating-verilog-blocks

Read through **6.11.2. Defining a Chisel BlackBox** onwards - these are the steps you would take if you were writing these files from scratch (but the course staff decided to play nice)! **6.4.2. Create Parameter Case Classes** onwards will become relevant in the next part of the lab. You can see how the blackbox for your core is instantiated within the generator in
`src/main/scala/OneFiftyOneCoreBlackBox.scala`. 

**Step 1: Make sure you commit and push your changes to your branch, possibly with some memorable commit name like "Finished Lab 2A".**

**Step 2: Based off the documentation and the Scala, answer the following questions.**

Copied here for auditors. Check Gradescope for latest versions.

At a high level:

- [Gradescope] Select all the possible reasons why can't we submodule your (Verilog) EECS151LA core as a Chipyard generator directly. 

- [Gradescope] Which of the following are real steps for integrating a Verilog peripheral into Chipyard (be it your core, or another side project you might create in Verilog)?

The black box:

- [Gradescope] Give us the one line of Chisel that actually instantiates your Verilog.

- [Gradescope] Unlike the `6.11.2. Defining a Chisel BlackBox` documentation example (you read it, right? >_>), you don't need to change this line of Chisel. Why is it fine as is?

- [Gradescope] Instead, which parameter in the `OneFiftyOneCoreBlackBox` do you need to set to point to the right Verilog file? (Note the way we have a class `OneFiftyOneCoreParams` that is passed in as `p: OneFiftyOneCoreParams`.)
  
- [Gradescope] You don't have to change what the parameter currently points to _in this file_. Why - where does it get set instead? (Hint: You'll need to set this parameter when this blackbox is instantiated at a later point!)

- [Gradescope] Which parameter would you override if you were to extend your core with a Floating Point Unit and support the "F" (single-precision floating-point) extension of the RISC-V ISA? (Note: you can read ahead to `6.4.2. Create Parameter Case Classes` to understand where some of these "Rocket-specific" core params come from.)

Initial glance at the interface:

- [Gradescope] What Chisel signals and datatypes are your EECS151 memory interface DATA VALID and DATA READY signals mapped to? (Note: recall an `io` field is a bundle with fields corresponding to the portlist of the Verilog module.) Which line(s) tell you so?

- [Gradescope] What about your CSR register? Which line(s) tells you so? 

- [Gradescope] **Bonus:** If you look at `src/main/scala/ShadowCouncil.scala.OLD`, you will find an alternative (although this file is NOT functional) approach to integrating a Verilog peripheral. What makes this approach different? (There's a keyword we're looking for!) (Hint: where does the documentation page linked above talk about `regmap`? Lecture had a slide about this concept!)

## Deliverables

Submit answers to the questions on Gradescope. Make sure you have a working EECS151LA core condensed into a single Verilog file. 

To reiterate, we are aware that not everyone finished the EECS151LA project 100% and that some students took an alternative route. For the sake of your learning experience, the original owner of the core isn't as important as its functionality - so feel free to use someone else's. After you have gone through the integration process, you are welcome to go back and swap in your own core, fixing and/or extending it as you wish. Of course, if you do have your own working core, the process may be extra rewarding, but there's plenty of learning opportunities either way. :D

# Credits

(_This segment partially borrows from the Spring 2023 Tapeout Lab 2, particularly the writings of Jerry Zhao._)
