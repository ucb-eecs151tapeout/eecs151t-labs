# Lab 2x) OFOT Generator In Chipyard - The Director's Cut

## Overview

There's a lot to learn in SoC design, and in Chipyard specifically! 

This addendum lab contains a lot of background information that was cut out from Lab 2C to make more space for actionable practice, but that the staff (or _at least_ one person, somewhere, somehow) _swears_ is still useful to reference (maybe, sometimes)..

# Introduction

For reference, here is an extra perspective of how TileLink fits into the SoC system:

![](Lab_2x_assets/TileLinkArch.png)


# Part 1) Chipyard SBT Files (More About)

## Elements of a `build.sbt` file

Some key elements of an `build.sbt` file include:

- **Settings**: Customizable build settings, like compiler options, source directories, and output directories.
- **Dependencies**: Lists of libraries and frameworks required by the project.
- **Tasks**: Definitions of build actions, such as compiling, testing, and packaging the project.

In more detail, you may find declarations such as:

**Settings**

- `scalaVersion`: specifies the version of Scala to use for the project.
- `organization`: defines the organization or company responsible for the project.
- `name`: sets the name of the project.
- `version`: specifies the version number of the project.

**Dependencies**

- `libraryDependencies`: defines the dependencies required by the project, such as libraries, frameworks, or plugins. These dependencies are specified using the Maven-style notation (e.g., "org.scala-lang.modules" %% "scala-parser-combinators" % "2.3.0").

One of the examples of a Scala-specific feature is the ability to cross build your project against multiple Scala versions.

**Multi-Project Builds**

- `lazy val`: defines a lazy val, which is a way to define a project or module within the build.
- `(project in file("path/to/module"))`: specifies the path to the module or subproject.
- `settings`: defines the settings for the module or subproject.

## `build.sbt` in Chipyard

That said, unlike the examples we try to focus on, the code can get _gnarly_.  In the top-level of your Chipyard repository (ex: `ofot-chipyard`), the `build.sbt` starts as:

````
import Tests._

// This gives us a nicer handle to the root project instead of using the
// implicit one
lazy val chipyardRoot = Project("chipyardRoot", file("."))

// keep chisel/firrtl specific class files, rename other conflicts
val chiselFirrtlMergeStrategy = CustomMergeStrategy.rename { dep =>
  import sbtassembly.Assembly.{Project, Library}
  val nm = dep match {
    case p: Project => p.name
    case l: Library => l.moduleCoord.name
  }
  if (Seq("firrtl", "chisel3").contains(nm.split("_")(0))) { // split by _ to avoid checking on major/minor version
    dep.target
  } else {
    "renamed/" + dep.target
  }
}
````
[...]

We typically ignore what we don't understand unless we need it.

In your top level `build.sbt` file, you will then also find `lazy val commonSettings`:

> Another way to factor out common settings across multiple projects is to create a sequence named commonSettings and call settings method on each project.

```
lazy val commonSettings = Seq(
  target := { baseDirectory.value / "target2" }
)

lazy val core = (project in file("core"))
  .settings(
    commonSettings,
    // other settings
  )

lazy val util = (project in file("util"))
  .settings(
    commonSettings,
    // other settings
  )
```

You will then see, in the ``Subproject definitions``  segment, `Rocket Chip` and `Chipyard-managed External Projects` submodules. Again, you're welcome to read into the documentation, but all of this is simply a way to simplify compilation and dependencies. You are no worse off if you simply pattern match whenever you need to add your own module.

# Part 2) Fire off to top-level!

Wait until later or explore! :D

# Part 3) Configs (More About)

## Deeper Introduction to Configs

We saw that you can think of the Rocket Chip parameter system as a global key->value store. Config fragments change the values of keys. Querying the `Config` object means accessing the key->value store (i.e. `p(key)` where `p` is of type `Parameters`).

At this point, you are likely to get confused. Maps? Keys? Where is all this coming from? The documentation [has a page for these](https://chipyard.readthedocs.io/en/stable/Customization/Keys-Traits-Configs.html#keys-traits-and-configs) that may not be intro friendly. Many people will take the code for granted and pattern match what seems to work until intuition builds up. 

Instead, let's explore the implementation a bit at a more basic level (as described by one of the original developers, Henry Cook). While you won't use it day to day, it might help build much stronger intuition for what the code in front of you is doing.

When talking about configs, the fundamental abstract is called a `View`. A **View** is a **key-value** store or hash map with a special property: the value corresponding to a particular key is conditioned on a "context".

More broadly, Chipyard/Rocket Chip function in "context-dependent environments" [(CDEs)](https://chipyard.readthedocs.io/en/stable/Advanced-Concepts/CDEs.html), enabling much more elegant composability.
One example of this is overriding, where a key set in a config fragment can be re-set (overriden) by a toplevel config.

The global config environment may be passed to a subsystem fragment, which can modify some of the keys or values before passing it off to the Tile generators, or passing it off to the NoC generator, or passing it off to our CPU generator, etc...

If you look at the `config` library (which is a utility library or `package`, just like `util`, `regmapper`, `diplomacy` as you'll see around Chipyard), you will actually see a class called `View`. And within those, you will see the subclasses `Parameters` and `Configs`. 

Generally:
- `Views` are dynamically-scoped key-value stores
- `View` -> `Parameters` will supply an API for altering Views with `up`, `site`, `here`...
- `View` -> `Configs` will supply an API for composing Views

Originally the `Configs` library was meant to be for top-level and `Parameters` for passing through the design to different generators, but by the time the developers realized they're doing the same thing it was too late to refactor everything and combine the two slightly different APIs... So if you're still confused about the difference, you're not alone! The difference is - no one had the time to do a mass system redesign..

With this background, let's revisit another simple example:
```
class WithNExtInterrupts(nExt: Int) extends Config {
  (site, here, up) => {
    case NExtInterrupts => nExt
  }
}

class MyConfig extends Config (
  new WithNExtInterrupts(16) ++ 
  new DefaultSmallConfig
  )
```

Recall that `Views` are (context-dependent) key->value mappings. When you do a look-up, you are matching in these `case`s looking for a certain key.
The `key` here is the number of [external interrupts](https://www.geeksforgeeks.org/external-and-internal-interrupts/).
It maps to value which is an integer (`Int`). 

The above snippet defines a subclass `MyConfig` of the class `Config` that we described above, and parameterizes it with the number of external interrupts by including the `WithNExtInterrupts` fragment (which sets `NExtInterrupts` to the provided value, here 16).
The base config (you can think of this as setting the "default" values for standard keys in a config) is `DefaultSmallConfig`.
The default of `MyConfig` will be the `DefaultConfig` with the number of interrupts set to 16.
Simple `Config`s can be composed to create much more complicated `Config`s!

Note this point in the documentation:

> The following example shows a non-additive config that combines or “assembles” the prior two config fragments using ++. The additive config fragments are applied from the right to left in the list (or bottom to top in the example). Thus, the order of the parameters being set will first start with the `DefaultExampleConfig`, then `WithMyAcceleratorParams`, then `WithMyMoreComplexAcceleratorConfig`.

```
class SomeAdditiveConfig extends Config(
  new WithMyMoreComplexAcceleratorConfig ++
  new WithMyAcceleratorParams ++
  new DefaultExampleConfig
)
```

Note that the main additional takeaway is that the order of the Configs matters!
Config [fragments] at the 'top' of the definition will override keys previously set by 'lower' config [fragments].

### Are you tired of reading yet?..

There's more complexity to be found here of course, for the determined.. 

`Config`s are defined using Scala's notion of a partial function - a function applicable to a subset of the data it has been defined for.
In this case, the domain of inputs a `Config` is defined upon are the keys that are set (or overriden) by that `Config`.

When evaluating `Config`s, Chipyard will traverse through each `Config` partial function from the top down and check: "Does this match? Does this match? Does this match?" until it finds the key that does match.
In the above example, `DefaultSmallConfig` might have a `case NExtInterrupts => 1`, but because of how we've composed `MyConfig`, when you look up external interrupts it will find the `case` within `WithNExtInterrupts` first and that will become the set value.
Taking the first value for a given key starting from the 'top' fragment of a `Config` (in execution order) is how 'overrides' occur.

The other part, is that instead of having this particular `Int` `nExt`, you can use `site`, `here`, and `up` functions to look up a different parameter.
You can also set the value of `NExtInterrupts` to be derived from a different key->value lookup, such as the number of [PLIC interrupts](https://five-embeddev.com/riscv-priv-isa-manual/Priv-v1.12/plic.html) that this `Config` supports.

In summary, **in a CDE (context-dependent environment) such as a `Config` definition you can overlay keys, replace what was there previously, and set key values by looking up the values for other keys.**
`Config`s can be composed (combined together) to do stuff that is "probably slightly too fancy", in [Henry's](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2016/EECS-2016-89.html) words..

*Note: Different people prefer to operate at different levels of abstraction.
Understanding the implementation may help you build intuition - or it may confuse you.
If you prefer to operate at a higher level of abstraction, that is totally fine - you don't have to know about `Views` to use `Configs`.
It is very common to simply look at other Scala files for reference and copy over what works.
But the information is here if you ever want to refer to it!*

### RocketConfig Summary

<table border-"0">
  <tr>
    <td>
    RocketConfig is part of the "Digital System configuration" depicted below. It is built on top of the AbstractConfig which contains the config fragments (each line like <code>freechips.rocketchip.subsystem.WithNBigCores(1)</code> that adds something to the overall system is called a config fragment) for IO Binders and Harness Binders (depicted below).
    </td>
    <td><img src="Lab_2_assets/io_high_level.jpg" width = 1700/></td>
  </tr>
</table>


<table border-"0">
  <tr>
    <td><img src="Lab_2_assets/io_harness.jpg" /></td>
    <td><img src="Lab_2_assets/io_harness_map.jpg"/></td>
  </tr>
</table>

Here's one way you might compose your own custom Config:

![](Lab_2x_assets/ConfiguringIOHarness.png)

## Cue the Hart puns! <3 

You'll notice that a section of the `WithOFOCores` fragment 'calculate[s] the next available hart ID'.

The word `hart` (HARdware Thread) will come up often in the context of RISC-V architecture. It is an abstraction of a hardware thread, capturing the essential aspects of a real hardware thread for the purposes of defining the RISC-V specifications. Cool. But what does that mean?

- A hart executes instructions independently from other harts in a RISC-V system.

- Each hart has its own set of architectural registers.

- Harts are not necessarily physical hardware threads, but rather an abstraction that can be implemented in various ways, such as through multithreading or simultaneous multithreading (SMT).

- A hart does not have its own independent instruction fetch unit, unlike a core, which contains an independent instruction fetch unit.

Seen a different way:

- From a software perspective, each hart appears as an independent processor, with its own registers and execution units, fetching instructions from memory.

- From a hardware perspective, multiple harts in a core may share some resources, such as the instruction fetch unit.

And in a summary, a `hart` executes instructions independently, with its own registers and execution context, and can be implemented through various mechanisms, including multithreading and SMT. It encapsulates the idea of anything that has its own program counter, register state, and execution thread.

In RISC-V, each hart needs its own ID. Here, we use a variable `idOffset` to find the next available ID, since each tile has a hart ID.
This is used to tell diplomacy that it needs to instantiate your core (and to use this "id" to keep track of it).

# Credits

Some of the images used in this document come from an ISCA 2023 Chipyard tutorial:

- 2: ["SoC Architecture and Components", ISCA 2023, Jerry Zhao.](https://docs.google.com/presentation/d/1L1qZAWrmwDzdZjeATT14WbpVSePEb_kH/edit#slide=id.p1)
- 3: ["Generating and Simulating Custom SoCs", ISCA 2023, Jerry Zhao.](https://docs.google.com/presentation/d/1JgkhiyqWGCEa0ZTHQ3zyY7TvqCzlFw9h/edit#slide=id.p76)

Useful references:

- [Chipyard Docs: 1.3. Configs, Parameters, Mixins, and Everything In Between](https://chipyard.readthedocs.io/en/stable/Chipyard-Basics/Configs-Parameters-Mixins.html)
- [Chipyard Docs: 3.9. Rocket-Chip Generators](https://chipyard.readthedocs.io/en/stable/Generators/Rocket-Chip-Generators.html)
- More Chipyard references: [https://github.com/ucb-bar/chipyard](https://github.com/ucb-bar/chipyard)
- [https://www.scala-sbt.org/](https://www.scala-sbt.org/)