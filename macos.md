# Installing AMUSE on macOS

Installing AMUSE on macOS is a bit more difficult that on Linux, because macOS doesn't
come with any development tools by default and because the standard open source packages
that AMUSE depends on are not in the App Store.

To get set up, we start by installing the development tools, then we install Conda, with
which we can install the dependencies, and then we can download and install AMUSE
itself.

## Installing XCode

The standard macOS developer tools are called XCode. To install them, open the Terminal
app. You'll see a window with some text in it. This text is known as the *prompt*, and
what it says varies a bit from machine to machine, but there's a cursor after it where
you can type commands.

To install XCode, type this and press Enter:

```
xcode-select --install
```

This will seem to do nothing, but if you click the Apple logo at the top left, then
System Settings..., then you will see that there's an update available for "Command line
tools for XCode". Go ahead and install the update to make the XCode tools available.


## Installing Conda

We'll be using the conda package manager to install the tools and libraries that we need
to install AMUSE. First, let's check if you have conda already.

Open a terminal, and type:

```
conda info
```

If it gives an overview starting with `active environment` then you have Conda already
available, and you should go to *Creating a conda environment* below.

If it says `conda: command not found` then you don't have Conda available, and you
should continue here with installing Miniforge.

## Installing Miniforge

Go to the [Miniforge download page](https://conda-forge.org/download/) and select the
version appropriate to your hardware, either arm64 (Apple Silicon) for newer Macs with
Apple chips, or x86_64 (Intel) for older Intel Macs.

Next, open Terminal. At the prompt, type (the % is already there, you need to type the
rest and then press Enter):


```
cd Downloads
```

and then either of these, depending on which hardware you have:

```
bash Miniforge3-MacOSX-x86_64.sh
```

```
bash Miniforge3-MacOSX-arm64.sh

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
from the other window) these. Press Enter after each line.

```
conda deactivate
```

```
conda config --set auto_activate_base false
```

Now you can close the original Terminal, and continue with the newly opened one that has
Conda available.


## Creating a Conda environment

Conda installs software into an environment, which is really just a directory (a
folder). To install AMUSE and its dependencies, we need to create an enviroment first,
using

```
conda create -n Amuse-env -c conda-forge --strict-channel-priority
```

Next, we can activate the environment, and then install the dependencies:

```
conda activate Amuse-env
```

Double-check that it says `(Amuse-env)` at the beginning of the prompt, and then you can
install the dependencies using

```
conda install -c conda-forge clang clangxx gfortran make openmpi python coreutils cmake openmpi-mpicc openmpi-mpicxx openmpi-mpifort gsl fftw gmp mpfr hdf5 netcdf4 libblas liblapack git 'setuptools>63' numpy docutils mpi4py h5py wheel scipy astropy jupyter pandas seaborn matplotlib
```

## Installing AMUSE

With an environment and all the dependencies ready, we can install AMUSE itself. First
we need to download it. This will create a directory named `amuse-src` with the AMUSE
source code:

```
git clone https://github.com/LourensVeen/amuse.git amuse-src
```

Then we can enter that directory, and configure AMUSE:

```
cd amuse-src
```

```
git switch amuse-course-mac
```

```
./configure
```

This will produce a bunch of output about all sorts of things that it's found or not. If
you followed the instructions above carefully, it should find everything needed for the
tutorial.

Next, we can build the parts of AMUSE we need for this course:

```
export MACOSX_DEPLOYMENT_TARGET=10.13
```

```
make framework
```

```
make huayno.code fi.code hermite.code ph4.code mesa_r2208.code seba.code sphray.code bhtree.code
```

This will take up to half an hour depending on the speed of your computer.

Finally, we install AMUSE into the conda environment, so that it's available whenever we
activate it:

```
pip install -e .
```

This concludes the installation. You will now be able to open a terminal, type

```
export OMPI_MCA_rmaps_base_oversubscribe=true
```

```
conda activate Amuse-env
```

to activate the environment, and then

```
jupyter lab
```

to start Jupyter and run your AMUSE scripts, or you can use

```
python script.py
```

to run them directly.

