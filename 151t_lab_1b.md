# Lab 1b) Chisel  Bootcamp

## What is Chisel - and why Chisel?

In EECS151, you learned the HDL (hardware description language) Verilog.  Much of Berkeley research and _some_ industry uses the increasingly popular HDL Chisel. If you've taken EECS251B or EECS194 Tapeout, you have already been to exposed to Chisel and have heard this shpeel.. if not, this background segment is for you!

### (A More Critical) Verilog Background

**Verilog** was invented in the 1980s as a simulation language. Synthesis was an afterthought.  Around the same time as the origin of Verilog, the US Department of Defense developed **VHDL** (A double acronym! VSIC (Very High-Speed Integrated Circuit) HDL). Because it was in the public domain it began to grow in popularity. Afraid of losing market share, Cadence opened Verilog to the public in 1990.

Verilog is the language of choice of Silicon Valley companies, initially because of high quality tool support and its similarity to C-language syntax. The latest Verilog version is “**System Verilog**”. VHDL is still popular within the government, in Europe and Japan, and some universities.

However, because Verilog was developed as a simulation language, it has many limitations that arguably System Verilog does not fully solve. 
* Many constructs don’t synthesize: `deassign`, timing constructs..
* Others lead to mysterious results: for-loops..
* It is difficult to understand synthesis implications of procedural assignment (`always` blocks), and blocking versus non-blocking assignments.. 
* Verilog has very weak “meta-programming” support for "compiling" circuits..

Many companies "solve" this with employee rules, lint style checkers, embedded TCL scripting.. Berkeley proposes Chisel as an alternative solution.

### Chisel: Constructing Hardware In a Scala Embedded Language

**Chisel** is an experimental attempt at a fresh start to address these issues. Through this lab you will learn how to use its basic set of design construction primitives. But Chisel's biggest claim to fame is its “meta-programming” support for building circuit **generators**.  You will soon learn those as well. 

Chisel is based on **Scala**, which allows integration of functional programming, object oriented programming, strong typing, and type inference into the HDL. Chisel can generate a high-speed C++-based cycle-accurate software simulator, or low-level Verilog designed to pass on to standard ASIC or FPGA tools for synthesis and place and route. You don't need to worry about each of these details.. 

TL;DR, **Chipyard** (which you will learn about in part B of this lab) is written in Chisel. Therefore, we will use Chisel in this class.

## Learning Chisel 

Instead of reinventing the wheel, we will make use of the existing **Chisel Bootcamp** to introduce ourselves to Chisel. 

**(1) Read through the Chisel Bootcamp introduction.** 

You will find our fork of the Bootcamp at:
``https://github.com/ucb-eecs151tapeout/eecs151t-lab1-chisel-bootcamp``

We will have some conceptual questions regarding Chisel on Gradescope.

**(2) Launch the Chisel Bootcamp Jupyter notebook instance.** 

The guide is under "Getting Started". There's a local setup version, but the environment is not the easiest to setup, so we recommend the notebook.  (Note that the notebook Chisel environment is a bit different from the Chipyard Chisel environment, so you *may* run into bugs in the future... Sorry.)

**(3) Go through Modules 1-3 of the Bootcamp.** 

You may want to go through Gradescope in parallel, as we'll want you to submit Chisel examples.  If you're a Chisel expert, you can skip the Bootcamp and just do the Gradescope - that's totally fine. If you're new, you'll find the Bootcamp should make the Gradescope assignment pretty easy.

Module 4 (FIRRTL) is interesting but optional. Note that FIRRTL has undergone major changes that we're pretty sure are not yet reflected in the Bootcamp.  

**(4) Fill out the Chisel part of Lab 1 on Gradescope.**

If you haven't already!

## Using Chisel 

Having a Bootcamp is nice, but you likely won't remember everything you did once you dig into Chipyard.. You can use the Bootcamp for reference. You can also use a much more compact [cheat sheet](https://inst.eecs.berkeley.edu/~cs250/sp17/handouts/chisel-cheatsheet3.pdf), such as:
 ![https://inst.eecs.berkeley.edu/~cs250/sp17/handouts/chisel-cheatsheet3.pdf]
In practice, Chipyard implements a lot of unique Chisel constructs that are best learned by working on Chipyard itself. So don't stress too much about this part and feel free to move on to part B even if you don't feel like a Chisel expert!

## Chisel Resources

Introduction heavily references:
- [Spring 2016 John Wawrzynek's Chisel Introduction](https://inst.eecs.berkeley.edu/~cs250/sp16/lectures/lec02-sp16.pdf)

More resources:
- [Chisel website: https://www.chisel-lang.org/](https://www.chisel-lang.org/)
- [Chisel Cheatsheet 3](https://inst.eecs.berkeley.edu/~cs250/sp17/handouts/chisel-cheatsheet3.pdf)
- [Original Chisel Bootcamp](https://github.com/freechipsproject/chisel-bootcamp)
- [Detailed Chisel API Documentation](https://www.chisel-lang.org/api/chisel3/latest/)
- [Intensivate's Chisel Learning Journey](https://github.com/Intensivate/learning-journey/wiki)
- You can also look at any recent semester of EECS251B or EECS194 Tapeout and find other professors' takes on Chisel.

While this is optional for this tapeout, students interested in designing accelerators and other IP (writing significant RTL) are especially encouraged to consult these resources. 

A bit older - the Chisel origins:
- [Chisel Tutorial Paper 2012](https://aspire.eecs.berkeley.edu/chisel-archive/tutorial-20121026.pdf)
- [Chisel DAC 2012 Paper](https://aspire.eecs.berkeley.edu/chisel-archive/chisel-dac2012.pdf)

# Credits

Parts of this setup are inspired by Spring 2023 Tapeout and Spring 2024 EECS251B.
Chisel and Chipyard resources have been developed outside the course.
