# Lab 1a) Chipyard Setup

## Background: Chipyard

As seen in lecture and the [Chipyard repository](https://github.com/ucb-bar/chipyard)..

> Chipyard is an open source framework for agile development of Chisel-based systems-on-chip. It will allow you to leverage the Chisel HDL, Rocket Chip SoC generator, and other [Berkeley](https://berkeley.edu/) projects to produce a [RISC-V](https://riscv.org/) SoC with everything from MMIO-mapped peripherals to custom accelerators. Chipyard contains processor cores ([Rocket](https://github.com/freechipsproject/rocket-chip), [BOOM](https://github.com/riscv-boom/riscv-boom), [CVA6 (Ariane)](https://github.com/openhwgroup/cva6/)), vector units ([Saturn](https://github.com/ucb-bar/chipyard/blob/main/saturn), [Ara](https://github.com/ucb-bar/chipyard/blob/main/ara)), accelerators ([Gemmini](https://github.com/ucb-bar/gemmini), [NVDLA](http://nvdla.org/)), memory systems, and additional peripherals and tooling to help create a full featured SoC. Chipyard supports multiple concurrent flows of agile hardware development, including software RTL simulation, FPGA-accelerated simulation ([FireSim](https://fires.im/)), automated VLSI flows ([Hammer](https://github.com/ucb-bar/hammer)), and software workload generation for bare-metal and Linux-based systems ([FireMarshal](https://github.com/firesim/FireMarshal/)). Chipyard is actively developed in the [Berkeley Architecture Research Group](http://bar.eecs.berkeley.edu/) in the [Electrical Engineering and Computer Sciences Department](https://eecs.berkeley.edu/) at the [University of California, Berkeley](https://berkeley.edu/).

For now, just know that Chipyard is a collection of open source projects that will help us develop our system-on-chip (SoC). Like Chisel, it is frequently found in Berkeley research and occasionally in industry. Each class introduces Chipyard a little differently. In EECS151T, we will focus on aspects most relevant to integrating our EECS151LA CPU cores into the SoC. 

If you ever need more resources:
- [Chipyard repository](https://github.com/ucb-bar/chipyard)
 - Chipyard Documentation: [https://chipyard.readthedocs.io/](https://chipyard.readthedocs.io/)
- Chipyard Basics slides: [https://fires.im/asplos23-slides-pdf/02_chipyard_basics.pdf](https://fires.im/asplos23-slides-pdf/02_chipyard_basics.pdf)

## Chipyard Setup

We will now set up your Chipyard environment. 

**(1)** **First, make a fork of our public Chipyard repo fork.** 

It will look something like: ``https://github.com/ucb-eecs151tapeout/ofot-chipyard``

**(2) Once your personal repo is created, copy down the SSH clone URL.**

Don't clone Chipyard locally.  For this course, you must work in the `/scratch` directory on a lab machine of your choice. Since `/scratch` is not automatically backed up, you will need to login to the same server each time. Chipyard will generate too much data for it to fit in your home directory.

> Note: As of Fall 2024, only eda-1 has been tested.

**(3) Make sure you know your instructional account login.**

*(These instructions are very similar to [EECS151 Lab 1](https://github.com/EECS150/asic-labs-sp24/tree/main).. Review that lab if you need a refresher on using instructional machines.)*

You may need to generate an instructional account:
1. Visit WebAcct: [http://inst.eecs.berkeley.edu/webacct](http://inst.eecs.berkeley.edu/webacct).
2. Click "Login using your Berkeley CalNet ID"
3. Click on "Get a new account".

Once the account has been created, you can email your class account form to yourself to have a record of your account information. You can follow the instructions on the emailed form to change your Linux password with `ssh update.eecs.berkeley.edu` and following the prompts.

As of this writing, we are still figuring out these accounts, so please post questions if you run into issues. Like EECS151, the servers used for this class are most likely primarily `eda-[1-4].eecs.berkeley.edu`.

You can check which machines are available at: [https://hivemind.eecs.berkeley.edu/](https://hivemind.eecs.berkeley.edu/).

**(4) Login to a lab machine over SSH.**

*(These instructions are very similar to [EECS151 Lab 1](https://github.com/EECS150/asic-labs-sp24/tree/main).. Review that lab if you need a refresher on using instructional machines.)*

Note: If you are off-campus (or off the EECS network), you may need to use the GlobalProtect VPN for these steps.

**SSH is the preferred connection because most work will be performed using the terminal and should be used unless a GUI is required**. The SSH protocol also enables file transfer between your local and lab machines via the `sftp` and `scp` utilities. **WARNING: DO NOT transfer files related to CAD tools to your personal machine. Only transfer files as needed.**

How To:

- Linux, BSD, MacOS  
    
    Access your workstation through SSH by running:
    
    ```shell
    ssh USERNAME@eda-X.eecs.berkeley.edu
    ```
    
    In our examples, this would be:
    
    ```shell
    ssh USERNAME@eda-8.eecs.berkeley.edu
    ```
    
- Windows  

	The classic and most lightweight way to use SSH on Windows is PuTTY ([https://www.putty.org/](https://www.putty.org/)). Download it and login with the FQDN above as the Host and your instructional account username. You can also use WinSCP (winscp.net) for file transfer over SSH.
    
    Advanced users may wish to install Windows Subsystem for Linux ([https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10), Windows 10 build 16215 or later) or Cygwin (cygwin.com) and use SSH, SFTP, and SCP through there.

**(5) Please, please - use tmux!**

It is _**highly**_ recommended to utilize one of the following SSH session management tools: `tmux` or `screen`. This would allow your remote terminal sessions to remain active even if your SSH session disconnects, intentionally or not. Below are tutorials for both:

- [Tmux Tutorial](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)
- [Screen Tutorial](https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/)

> [!WARNING]
> If you run commands in a "raw" (without `nohup` or `tmux`) SSH terminal, they will be killed when you exit your session (or if your wifi goes out for a few seconds).
> 
> To use [`tmux`](https://tmuxcheatsheet.com/), you can add `RemoteCommand tmux new -A -s ssh` to your ssh config for your instructional account or run `tmux new-s -t <name>` once you log in. (Reference the tutorial above if you're confused.)
> 
> You can also run a command `cmd` as `nohup cmd` to prevent the command from being killed if the session exits, but this is less convenient.
> 
> If you manually created a `tmux` session, you must reattach to it manually the next time you log in with `tmux a -t <name>`.

Whenever you enter an SSH session, you should start or attach to a `tmux` session. 

**(6)** **Run the commands below in a `bash` terminal.**

During the `bash Miniforge3.sh` and `./build-setup.sh` commands, say "yes" and press enter when prompted.

The following will source the eecs151t bashrc (locating license and binary paths etc.) on startup moving forward. Only `~/.bash_profile` is `source`d on shell startup on instructional machines by default.
```
echo "source ~./bashrc" >> ~/.bash_profile
echo "source /home/ff/ee198/ee198-20/.eecs151t.bashrc" >> ~/.bashrc
source ~/.bash_profile
```
Now create your `/scratch` directory and install `conda`:

```
mkdir -m 0700 -p /scratch/$USER
cd /scratch/$USER
wget -O Miniforge3.sh \
"https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash Miniforge3.sh -p "/scratch/${USER}/conda"
```

You will see the line, "For changes to take effect, close and re-open your current shell." Instead, run:

```
source ~/.bashrc
```
/// L CHANGES 
Then, setup your repo.
This segment of commands may change for Spring 2025, ask the staff for most up to date links:

```
git clone <YOUR SSH CLONE URL> # i.e. git@github.com:ucb-eecs151tapeout/ofot-chipyard-sp25.git
cd ofot-chipyard-sp25 # your repo name
## Should no longer be needed:
# git remote add skeleton https://github.com/ucb-eecs151tapeout/ofot-chipyard.git
# git pull skeleton main
```
/// END L CHANGES 

/// E CHANGES 
```
git clone git@github.com:ucb-eecs151tapeout/ofot-chipyard.git
cd ofot-chipyard
git checkout -b ${USER}-working # create your own branch!
git push --set-upstream origin ${USER}-working
```
/// END E CHANGES 

#TODO - this might also not be needed..

```
mamba install -n base conda-lock=1.4
mamba activate base
git config --global protocol.file.allow always
```

**(7)** **Run the commands below in a `bash` terminal.**

These next scripts may take a very long time (potentially over 32 minutes). Don't forget to `tmux`!

```
./build-setup.sh riscv-tools -s 8 -s 7 -s 8 -s 9 -s 10 --use-lean-conda
```

If you get errors here, you can check `build-setup.log` to confirm the issue. 
Then check the FAQ or ask on Discord.

```
source env.sh
./scripts/init-vlsi.sh sky130
```

If you see ``Setup complete!`` you're done!

If you get ``Permission denied`` errors, remember to generate or copy over your SSH key as described in the Prelab.

Then, run the following:

```
conda config --add channels ucb-bar
conda config --set channel_priority strict
mamba install firtool
```

The TL;DR of these commands is that you now have a working Chipyard environment! 

> [!TIP]
> More specifically, these scripts are responsible for setting up the tools and environment used by the course.
> 
> - Conda is an open-source package and environment management system that allows you to quickly install, run, and update packages and their dependencies. In other words, conda allows users to create an environment that holds system dependencies like make, gcc, etc. We need to activate this conda environment.
> - We use commercial tools such as VCS from a common installation location. We add this location to the path and source relevant licenses.
>
> The shell script will initialize and checkout all of the necessary git submodules. When updating Chipyard to a new version, you will also want to rerun this script to update the submodules. Using git directly will try to initialize all submodules; this is not recommended unless you expressly desire this behavior.
>
> git submodules allow you to keep other Git repositories as subdirectories of another Git repository. For example, the above script initiates the rocket-chip submodule which is its own Git repository that you can look at here. If you look at the .gitmodules file at top-level chipyard, you can see
>
> [submodule "rocket-chip"]
>	path = generators/rocket-chip
>	url = https://github.com/chipsalliance/rocket-chip.git
> which defines this behavior. Read more about git submodules here.
>
>Feel free to look at the scripts themselves if you want to learn more.


### Using the environment

To enter your Chipyard environment, you will need to run the following command in each terminal session you open (including new `tmux` sessions).

```
source /scratch/$USER/ofot-chipyard/env.sh
```

This env.sh file should exist in the top-level repository. This file sets up necessary environment variables such as PATH for the current Chipyard repository. This is required by future Chipyard steps such as the make system to function correctly.


> [!TIP]
> If you would like to run this automatically on terminal startup, you can add the command to your `~/.bashrc`
> by running the following:
>
> ```
> echo "source /scratch/$USER/ofot-chipyard/env.sh" >> ~/.bashrc
> ```

Optionally, you can also set the repo path as an environment variable by running `export wd=/scratch/$USER/ofot-chipyard` and then jump to it with `cd $wd`.
Note that `export` only lasts until you close the terminal session, add a command to `~/.bashrc` to have it run every time you start a new commandline session!

### Common issues

If you completed the setup successfully, you can skip this section.

#### `hammer-mentor-plugins`-related errors

These should've been removed from this version of Chipyard, as `hammer-mentor-plugins` is a private repository. If you run into bugs, particularly during the `init-vlsi.sh` step, let us know.

You can also try fixing the problem yourself by doing the following..

Edit the init-vlsi.sh script, such as with vim:

`vim ./scripts/init-vlsi.sh`

Comment out the line:

`git submodule update --init --recursive vlsi/hammer`

Which with vim, can be done with `i` (insert) mode then `:wq` (write then quit) once you're done.

Rerun: `./scripts/init-vlsi.sh sky130`

For context, the reason for this error is that we do not have a local copy of `hammer-mentor-plugins` setup and `mentor` is not an open source tool, so the online repo is private. The script tries to pull from the private repo to update the submodule and can't find it. But we're not using that tool, so we can skip it.


#### `build-setup` transaction failed

If you observe that the `conda` install transaction failed, which is often caused by stopping `./build-setup` prematurely, you will likely need to reinstall conda. To do so, reinstall `conda` by running the following commands:

```bash
cd /scratch/$USER/<semester>-chipyard-<uname>
rm -rf .conda-env
cd ..
rm -rf conda
bash Miniforge3.sh -p "/scratch/${USER}/conda"
source ~/.bashrc
```

A dead giveaway is: ``ERROR:root:CondaValueError: prefix already exists: [....]/ofot-chipyard/.conda-env``

In which case, make sure to delete that ``.conda-env``. 

After running the above commands, go through the repo setup instructions again (you do not need to delete and re-clone the chipyard repo).

#### Hanging on `configure: configuring default subproject`

This might happen if you try to run the installation without `unset`ing the `CONFIG_SHELL` environment variable.
To resolve it, run the following from the Chipyard root directory:

```
unset CONFIG_SHELL
source env.sh
./build-setup.sh riscv-tools -s 1 -s 2 -s 6 -s 7 -s 8 -s 9 -s 10 
```

This skips the steps that have already completed prior to the failure and retries the failed step.
Proceed as usual after the command succeeds.

#### Repository Issues (like `divergent branches`)

When in doubt, for the sake of this lab, you can reset to the baseline with:

``git reset —hard origin/main``

You may not want to do this on your actual project as you may lose all your changes. But this Chipyard setup is just practice!

## Your first VLSI run!

From the `vlsi` folder, after sourcing `env.sh` in the toplevel directory, run:
``make drc tutorial=sky130-commercial IS_TOP_RUN=0``
Eventually (again, make sure to run these commands in tmux so they don't get killed when you shut your laptop lid!), you should be presented with a biblical flood of output that ends with these lines:

```
Pegasus finished normally. 2025-01-30 19:19:53
Action drc config output written to output.json
```

This command invokes synthesis, place-and-route, and DRC checking on a simple SoC design. Don't worry too much about what it's doing right now (although you're always welcome to read through the files).
This is just to make sure your repository is setup right and you are able to invoke the tools you'll need for the class.

Don't forget to complete the Gradescope check-in, and let us know on Discord if you run into any issues!

# Credits

Parts of this setup are inspired by Spring 2023 Tapeout and Spring 2024 EECS251B.
Chisel and Chipyard resources have been developed outside the course.
