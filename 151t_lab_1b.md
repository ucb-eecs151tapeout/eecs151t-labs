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

# GradeScope Questions Addendum

For the convenience of course auditors, the questions from Gradescope are listed here (but refer to Gradescope for latest versions). 
The format is a bit ugly as markdown, apologies. 

## Q1 Lab 1A: Chisel Basics
7 Points
 
Reference the Lab 1 Part A document to complete this section. It's not meant to be difficult - just motivate you to not skip the reading if you're not yet familiar with the content. Some of these answers can be found in the Bootcamp itself.

### Q1.1
1 Point
 
Which of these is not an HDL?

Choice 1 of 4:Verilog
Choice 2 of 4:Chisel
Choice 3 of 4:Scala
Choice 4 of 4:VHDL

### Q1.2
1 Point
 
Chisel is great because it enables high-level-synthesis (HLS), abstracting away micro-architectural details far away from the designer.

Choice 1 of 2:True
Choice 2 of 2:False

### Q1.3
1 Point
 
Which of these are legal code snippets?

Choice 1 of 1:The code snippet below me!
 
```
val a = Wire(UInt(4.W))
a := 0
```
Choice 1 of 1:No, the code snippet below me!
 
```
val a = Wire(UInt(4.W))
a := 0.U
```
Choice 1 of 1:Please pick me.
 
```
val bool = Wire(Bool())
val boolean: Boolean = false
when (bool) { ... }
if (boolean) { ... }
```
Choice 1 of 1:Best for last, I promise I'm correct!
 
```
val bool = Wire(Bool())
val boolean: Boolean = false
if (bool) { ... }
when (boolean) { ... }
```

### Q1.4
1 Point

Because Chisel Modules are normal Scala [A], we can use the power of Scala's constructors to [B] the elaboration of our design. This "family of modules" is referred to in Chisel as a [C].

[A]:
Choice 1 of 3:packages
Choice 2 of 3:classes
Choice 3 of 3:generators

[B]:
Choice 1 of 3:parametrize
Choice 2 of 3:synthesize
Choice 3 of 3:inherit

[C]:
Choice 1 of 3:package
Choice 2 of 3:class
Choice 3 of 3:generator

### Q1.5
1 Point
 
The Bootcamp demonstrates how Chisel (+ Scala) enable all of these great features, except:

Choice 1 of 4:Abstract classes and traits that subclasses must implement
Choice 2 of 4:A ChiselTest verification framework vastly more comprehensive than System Verilog's
Choice 3 of 4:Parameter passing with default values and overrides for circuit descriptions
Choice 4 of 4:Combinational and sequential logic patterns very familiar to Verilog users

### Q1.6
1 Point
 
When you execute a Chisel design, it elaborates (executes the surrounding Scala code) to construct an instance of your generator, with all Scala parameters resolved. Instead of directly emitting Verilog, Chisel emits an intermediate representation. 

You don't need to know how this works, but should be aware this intermediate representation is:


Choice 1 of 4:a VCS simulation
Choice 2 of 4:testchipip
Choice 3 of 4:a Chisel generator
Choice 4 of 4:FIRRTL/CIRCT

### Q1.7
1 Point
 
This intermediate representation:

Choice 1 of 2:Is transformed and optimized before being compiled further to Verilog.
Choice 2 of 2:Is a direct mapping of the Chisel to Verilog - any optimizations need to be done manually.

## Q2 Lab 1B: Chisel Bootcamp Examples
9 Points
 
You don't have to test yourself on every single example in the Bootcamp and/or memorize every piece of syntax! Really. It'll be a lot easier to learn what's relevant as you delve more into Chipyard. However, you should understand enough of the Bootcamp to do these examples easily.

### Q2.1 Module 1: Introduction to Scala
1 Point
 
In Scala, make a list of five Random integers, sum them using a for loop, and print the sum.

###  Q2.2 Module 2.1: Your First Chisel Module
1 Point
 
Declare a Chisel class, PassthroughGenerator, which will accept an integer width construction parameter that dictates the widths of its input and output ports. The module combinationally connects in and out, so in drives out.

 
Now instantiate a module with width 64 bits. Do not generate Verilog.

### Q2.3 Module 2.2: Combinational Logic
1 Point
 
Create a Parameterized Adder that can either saturate the output when overflow occurs, or truncate the results (i.e. wrap around), by filling in the ??? below. 
```
class ParameterizedAdder(saturate: Boolean) extends Module {
  val io = IO(new Bundle {
    val in_a = Input(UInt(4.W))
    val in_b = Input(UInt(4.W))
    val out  = Output(UInt(4.W))
  })

  ???
}
```

### Q2.4 Module 2.4: Sequential Logic
1 Point
 
Create a module for finding the minimum value in a sequence of inputs using conditional register assignments. (Hint: Make sure the RegInit value makes sense.. It's probably not going to be 0.U.)

### Q2.5 Module 3.1: Generators: Parameters
5 Points
 
The following example shows a generator for a 1-bit input Mealy machine. This question is to ensure you are getting familiar with reading Chisel.
```
// Mealy machine has
case class BinaryMealyParams(
  // number of states
  nStates: Int,
  // initial state
  s0: Int,
  // function describing state transition
  stateTransition: (Int, Boolean) => Int,
  // function describing output
  output: (Int, Boolean) => Int
) {
  require(nStates >= 0)
  require(s0 < nStates && s0 >= 0)
}

class BinaryMealy(val mp: BinaryMealyParams) extends Module {
  val io = IO(new Bundle {
    val in = Input(Bool())
    val out = Output(UInt())
  })

  val state = RegInit(UInt(), mp.s0.U)

  // output zero if no states
  io.out := 0.U
  for (i <- 0 until mp.nStates) {
    when (state === i.U) {
      when (io.in) {
        state  := mp.stateTransition(i, true).U
        io.out := mp.output(i, true).U
      }.otherwise {
        state  := mp.stateTransition(i, false).U
        io.out := mp.output(i, false).U
      }
    }
  }
}

// example from https://en.wikipedia.org/wiki/Mealy_machine
val nStates = 3
val s0 = 2
def stateTransition(state: Int, in: Boolean): Int = {
  if (in) {
    1
  } else {
    0
  }
}
def output(state: Int, in: Boolean): Int = {
  if (state == 2) {
    return 0
  }
  if ((state == 1 && !in) || (state == 0 && in)) {
    return 1
  } else {
    return 0
  }
}

val testParams = BinaryMealyParams(nStates, s0, stateTransition, output)
Let's go through this Mealy machine step by step.

(1) What is the purpose of BinaryMealyParams? Why does it exist and what does it do?

// Mealy machine has
case class BinaryMealyParams(
  // number of states
  nStates: Int,
  // initial state
  s0: Int,
  // function describing state transition
  stateTransition: (Int, Boolean) => Int,
  // function describing output
  output: (Int, Boolean) => Int
) {
  require(nStates >= 0)
  require(s0 < nStates && s0 >= 0)
}
```

(2) Describe (in 1-2 sentences) what BinaryMealy is doing. What does the output depend on?

```
class BinaryMealy(val mp: BinaryMealyParams) extends Module {
  val io = IO(new Bundle {
    val in = Input(Bool())
    val out = Output(UInt())
  })

  val state = RegInit(UInt(), mp.s0.U)

  // output zero if no states
  io.out := 0.U
  for (i <- 0 until mp.nStates) {
    when (state === i.U) {
      when (io.in) {
        state  := mp.stateTransition(i, true).U
        io.out := mp.output(i, true).U
      }.otherwise {
        state  := mp.stateTransition(i, false).U
        io.out := mp.output(i, false).U
      }
    }
  }
}
```

 
(3) What are stateTransition and output examples of? Why are they here?

```
// example from https://en.wikipedia.org/wiki/Mealy_machine
val nStates = 3
val s0 = 2
def stateTransition(state: Int, in: Boolean): Int = {
  if (in) {
    1
  } else {
    0
  }
}
def output(state: Int, in: Boolean): Int = {
  if (state == 2) {
    return 0
  }
  if ((state == 1 && !in) || (state == 0 && in)) {
    return 1
  } else {
    return 0
  }
}
```

 
(4) Is testParams a mutable or immutable construct?

```
val testParams = BinaryMealyParams(nStates, s0, stateTransition, output)
```

Choice 1 of 2:Mutable
Choice 2 of 2:Immutable

 
(5) How do you use testParams to instantiate a new Binary Mealy machine?

## Q3 Lab 1A: Chisel Feedback
0 Points
 
How much time did the Chisel Bootcamp take you?

Choice 1 of 3:I need way more time, this is way too long!
Choice 2 of 3:About how much I'd expect for a bootcamp lab.
Choice 3 of 3:Oh lol, I got through it at lightning speed.

Was the content useful and accessible?

Choice 1 of 3:This is way too dense and difficult!
Choice 2 of 3:Yeah, maybe not 100% but I got what I needed.
Choice 3 of 3:Oh lol, I already know Chisel.

Feel free to jot down any comments or feedback here.