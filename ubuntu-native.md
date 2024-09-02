# Installing AMUSE

Installing AMUSE is currently not as easy as we'd like for it to be. We're working on
a brand new build system and installer, with the goal to have AMUSE available on
conda-forge. This turns out to be a significant engineering project however,
and we need a bit more time.

In the mean time, the existing system still works, but it has some issues and the
instructions on the AMUSE website will give you an error if you follow them. This will
all be fixed in the future, but for now we have this page to explain how to get AMUSE
installed on your system.

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

## Installing dependencies

Open a terminal, and use the following commands to install the required dependencies
(the $ is already there, you need to type the rest and press Enter):

```
$ sudo apt-get update
$ sudo apt-get install gcc g++ gfortran make mpich python3-dev libmpich-dev
$ sudo apt-get install libgsl-dev cmake libfftw3-dev libmp3-dev libmpfr-dev
$ sudo apt-get install libhdf5-serial-dev hdf5-tools libblas-dev liblapack-dev
$ sudo apt-get install python3-venv python3-pip git unzip
```

(You're welcome to combine all the `apt-get install` commands into a single one, it
wouldn't fit here.)

## Making a virtual environment

Python has a nifty feature called virtual environments. A virtual environment is really
just a directory (a folder) into which Python packages can be installed. By using
different virtual environments for different programs (or projects), we avoid potential
conflicts between them, for example if program A needs version 4 of package Q, while
program B can only work with version 5.

So we are going to make a virtual environment for AMUSE:

```
$ python3 -m venv ~/Amuse-env
```

You can put the virtualenv somewhere else if you want to by passing a different
location, it's just a directory.

Next, we need to activate the environment:

```
$ . ~/Amuse-env/bin/activate
```

(Yes, that's a period at the beginning, then a space, then the location of the
virtualenv followed by `/bin/activate`.)

You'll notice a `(Amuse-env)` at the beginning of your prompt now, to show that the
environment is active.

## Installing AMUSE

Now, we can install AMUSE itself, and a few more Python packages that it needs.

Check that you still have `(Amuse-env)` in your prompt, and then do

```
$ pip install --upgrade pip
$ pip install 'setuptools>63' numpy docutils mpi4py h5py wheel scipy astropy
$ pip install jupyter pandas seaborn matplotlib
```

With that done, we can download AMUSE itself. This will create a directory named
`amuse-src` with the AMUSE source code:

```
$ git clone https://github.com/amusecode/amuse.git amuse-src
```

Then we can enter that directory, and configure AMUSE:

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
amuse-src$ make huayno.code fi.code hermite.code ph4.code mesa_r2208.code mesa_r15140 seba.code
```

This will take 5-15 minutes depending on the speed of your computer.

Finally, we install AMUSE into the virtualenv, so that it's available whenever we
activate the virtualenv:

```
amuse-src$ pip install -e .
```

This concludes the installation. You will now be able to open a terminal, type

```
$ . ~/Amuse-env/bin/activate
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

