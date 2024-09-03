# Installing AMUSE

Installing AMUSE is currently not as easy as we'd like for it to be. We're working on
a brand new build system and installer, with the goal to have AMUSE available on
conda-forge. This turns out to be a significant engineering project however,
and we need a bit more time.

In the mean time, the existing system still works, but it has some issues and the
instructions on the AMUSE website will give you an error if you follow them. This will
all be fixed in the future, but for now we have this page to explain how to get AMUSE
installed on your system, or at least enough of it to be able to do the coursework.

This page contains instructions for Windows, Ubuntu, and macOS, so please scroll to the
right section for your operating system.

# Windows

AMUSE does not run directly on Windows, as computational scientists almost exclusively
use Linux (supercomputers all run Linux) or macOS (because people like Macs, and Linux
on Apple Silicon is still being worked in).

However, Windows has a built-in Linux environment called Windows Subsystem for Linux, or
WSL. You can install and use AMUSE in there instead.

If you already have an Ubuntu WSL environment set up on your computer, then you can use
that. If not, follow the
[instructions here](https://canonical-ubuntu-wsl.readthedocs-hosted.com/en/latest/guides/install-ubuntu-wsl2/)
to set up an Ubuntu environment on your Windows computer.

Once you have this working, you can continue by following the Ubuntu instructions below
in the WSL Ubuntu terminal window.

# Ubuntu

Installing AMUSE consists of a few steps: first, we need to install the dependencies,
then we create an environment (and install some more dependencies), then we get the
source code, and finally we compile and install AMUSE into the environment so that we
can use it.

## Installing Miniforge

We'll be using the conda package manager to install the tools and libraries that we need
to install AMUSE. If you have a working conda setup already, then you can skip this
section and continue at *Making a conda environment* below.

Go to the [Miniforge download page](https://conda-forge.org/download/) and select the
Linux x64_64 (amd64) version.

Next, open a terminal. At the prompt, type (the $ is already there, you need to type the
rest and then press Enter):

```
$ cd Downloads
$ bash Miniforge3-Linux-x86_64.sh
```

To accept the license (it's all open source) you may have to press q first to exit the
reader, then type yes and press Enter. Next, press Enter again to confirm the location
where miniforge will be installed (or pick a different one, if you want).

Miniforge will now be installed. After a while, it will ask

```
Do you wish to update your shell profile to automatically initialize conda?
This will activate conda on startup and change the command prompt when activated.
If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

conda config --set auto_activate_base false

You can undo this by running `conda init --reverse $SHELL`? [yes|no]
```

Adding Conda to the shell is a good idea, but automatically activating the base
environment isn't. So answer "yes" and wait for the installation to finish.

At this point, Conda is installed and set up to be activated automatically, but it's not
active in the current terminal yet, because Conda wasn't there yet when the terminal was
opened.

So next, open a second terminal window, and notice that a new `(base)` has appeared at
the front of your prompt. This is exactly that base environment that we don't want. So,
go ahead and type (or copy-paste from the other window)

```
$ conda deactivate
$ conda config --set auto_activate_base false
```

Now you can close the original terminal, and continue with the newly opened one that has
Conda available.


## Creating a Conda environment

Conda installs software into an environment, which is really just a directory (a
folder). To install AMUSE and its dependencies, we need to create an enviroment first,
using

```
$ conda create -n Amuse-env -c conda-forge --override-channels
```

Next, we can activate the environment, and then install the dependencies:

```
$ conda activate Amuse-env
```

Double-check that it says `(Amuse-env)` at the beginning of the prompt, and then you can
install the dependencies using

```
$ conda install gcc gxx gfortran binutils unzip patch make python coreutils cmake
$ conda install openmpi openmpi-mpicc openmpi-mpicxx openmpi-mpifort gsl fftw gmp mpfr
$ conda install hdf5 netcdf4 libblas liblapack git
$ conda install pip 'setuptools>63' numpy docutils mpi4py h5py wheel
$ conda install scipy astropy jupyter pandas seaborn matplotlib
```

## Installing AMUSE

Now, we can install AMUSE itself. First, we need to get the source code, which we can do
using git:

```
$ git clone https://github.com/amusecode/amuse.git amuse-src
```

This will make a new directory with AMUSE in it. We can enter that directory, and
configure AMUSE:

```
$ cd amuse-src
amuse-src$ ./configure
```

This will produce a bunch of output about all sorts of things that it's found or not. If
you followed the instructions above carefully, it should find everything needed for the
tutorial.

Next, we can build the parts of AMUSE we need for this course:

```
amuse-src$ make framework
amuse-src$ make huayno.code fi.code hermite.code ph4.code seba.code mesa_r2208.code
```

This will take 15-45 minutes depending on the speed of your computer.

Finally, we install AMUSE into the Conda environment, so that it's available whenever we
activate that environment. Check that you still have `(Amuse-env)` in your prompt, and
then do

```
amuse-src$ pip install -e .
```

This concludes the installation. You will now be able to open a terminal, type

```
$ conda activate Amuse-env
```

to activate the environment, and then

```
$ jupyter lab
```

to run your AMUSE scripts.


## Known issues

There's an issue with the default configuration of OpenMPI on Ubuntu that sometimes
shows up, which causes AMUSE to get stuck. If your scripts get stuck and your laptop fan
is making a lot of noise, then this may be the problem. You can solve it by typing the
following in the terminal:

```
echo 'btl_tcp_if_include=lo' >>.openmpi/mca-params.conf

```
and then restarting Python (or Jupyter, if you're using that).


# macOS

Installing AMUSE on macOS is a bit more difficult that on Linux, because macOS doesn't
come with any development tools by default and because the standard open source packages
that AMUSE depends on are not in the App Store.

To get set up, we start by installing the development tools, then we install Conda, with
which we can install the dependencies, and then we can download and install AMUSE
itself.

## Installing XCode

The standard macOS developer tools are called XCode. To install them, open the Terminal
app. You'll see a window with some text in it. This text is known as the *prompt*, and
what it says varies a bit from machine to machine, but it ends with a `%` sign, after
which you can type commands.

To install XCode, type this and press Enter: (the % is already there as part of your
prompt, you need to type the rest)

```
% xcode-select --install
```

This will seem to do nothing, but if you click the Apple logo at the top left, then
System Settings..., then you will see that there's an update available for "Command line
tools for XCode". Go ahead and install the update to make the XCode tools available.


## Installing Conda

Installing the dependencies for AMUSE is a bit more difficult on macOS than on Linux,
because they're not in the App Store. Instead, an alternative package manager needs to
be installed first. There are basically three options here: Homebrew, MacPorts, and
Conda. I've seen way too many botched Homebrew and MacPorts setups, so we'll use Conda
here.

If you already have a version of Conda set up on your Mac, then you should skip the
next step and go to "Creating a Conda environment".

## Installing Miniforge

Go to the [Miniforge download page](https://conda-forge.org/download/) and select the
version appropriate to your hardware, either arm64 (Apple Silicon) for newer Macs with
Apple chips, or x86_64 (Intel) for older Intel Macs.

Next, open Terminal. At the prompt, type (the % is already there, you need to type the
rest and then press Enter):


```
% cd Downloads
```

and then either of these, depending on which hardware you have:

```
% bash Miniforge3-MacOSX-x86_64.sh
% bash Miniforge3-MacOSX-arm64.sh

```

To accept the license (it's all open source) you may have to press q first to exit the
reader, then type yes and press Enter. Next, press Enter again to confirm the location
where miniforge will be installed (or pick a different one, if you want).

Miniforge will now be installed. After a while, it will ask

```
Do you wish to update your shell profile to automatically initialize conda?
This will activate conda on startup and change the command prompt when activated.
If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

conda config --set auto_activate_base false

You can undo this by running `conda init --reverse $SHELL`? [yes|no]
```

Adding Conda to the shell is a good idea, but automatically activating the base
environment isn't. So answer "yes" and wait for the installation to finish.

At this point, Conda is installed and set up to be activated automatically, but it's not
active in the current Terminal yet, because Conda wasn't there yet when the Terminal was
opened.

So next, select Shell -> New Window in the top menu bar to open a second Terminal
window, and notice that a new `(base)` has appeared at the front of your prompt. This is
exactly that base environment that we don't want. So, go ahead and type (or copy-paste
from the other window)

```
% conda deactivate
% conda config --set auto_activate_base false
```

Now you can close the original Terminal, and continue with the newly opened one that has
Conda available.


## Creating a Conda environment

Conda installs software into an environment, which is really just a directory (a
folder). To install AMUSE and its dependencies, we need to create an enviroment first,
using

```
% conda create -n Amuse-env -c conda-forge --override-channels
```

Next, we can activate the environment, and then install the dependencies:

```
% conda activate Amuse-env
```

Double-check that it says `(Amuse-env)` at the beginning of the prompt, and then you can
install the dependencies using

```
% conda install clang clangxx gfortran make openmpi python coreutils cmake
% conda install openmpi-mpicc openmpi-mpicxx openmpi-mpifort gsl fftw gmp mpfr
% conda install hdf5 netcdf4 libblas liblapack git
% conda install 'setuptools>63' numpy docutils mpi4py h5py wheel
% conda install scipy astropy jupyter pandas seaborn matplotlib
```

## Installing AMUSE

With an environment and all the dependencies ready, we can install AMUSE itself. First
we need to download it. This will create a directory named `amuse-src` with the AMUSE
source code:

```
% git clone https://github.com/LourensVeen/amuse.git amuse-src
```

Then we can enter that directory, and configure AMUSE:

```
% cd amuse-src
amuse-src % git switch amuse-course-mac
amuse-src % ./configure
```

This will produce a bunch of output about all sorts of things that it's found or not. If
you followed the instructions above carefully, it should find everything needed for the
tutorial.

Next, we can build the parts of AMUSE we need for this course:

```
amuse-src % make framework
amuse-src % make huayno.code fi.code hermite.code ph4.code mesa_r2208.code seba.code
```

This will take up to half an hour depending on the speed of your computer.

Finally, we install AMUSE into the conda environment, so that it's available whenever we
activate it:

```
amuse-src % pip install -e .
```

This concludes the installation. You will now be able to open a terminal, type

```
% export OMPI_MCA_rmaps_base_oversubscribe=true
% . ~/Amuse-env/bin/activate
```

to activate the environment, and then

```
% jupyter lab
```

to start Jupyter and run your AMUSE scripts.

