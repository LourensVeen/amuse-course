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
$ conda create -n Amuse-env
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

TODO

