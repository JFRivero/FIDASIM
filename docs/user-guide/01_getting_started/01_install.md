title: Installation

#Installing FIDASIM
So you have decided to install FIDASIM. Don't worry this should be relatively painless.

@note
If you experiance problems installing FIDASIM you did something wrong and you should [**let us know**](https://github.com/D3DEnergetic/FIDASIM/issues/new) so we can laugh at you (and also help you)

The following code snippets assumes you are using a BASH shell.
To figure out what shell you currently have run `echo $SHELL` to find out.

[TOC]

---

##Dependencies
FIDASIM has the following dependencies:

* Linux because all other operating systems are inferior
* [Make](https://www.gnu.org/software/make/) for building FIDASIM. (Required)
* Fortran, C, and C++ compilers. (Required)
  [GNU(>v4.6)](https://gcc.gnu.org/) and [Intel(>13)](https://software.intel.com/en-us/intel-compilers) compilers are supported.
  Note you cannot mix and match different compilers.
* [zlib](http://zlib.net/) for file compression. (Required)
* [Anaconda Python](https://www.continuum.io/why-anaconda) for python scripts and pre-processing. (Optional)
* [IDL](http://www.harrisgeospatial.com/ProductsandSolutions/GeospatialProducts/IDL.aspx) for pre-processing (Optional)
* [HDF5 1.8.16](https://www.hdfgroup.org/HDF5/) for reading and writing data files (Included)
* [EFIT](https://fusion.gat.com/theory/Efit) for electro-magnetic fields (Partially Included)
* [git](https://git-scm.com/) for version control (Optional)
* [Ford](https://github.com/cmacmackin/ford) for creating HTML documentation (Optional)
* [LinkChecker](http://wummel.github.io/linkchecker/) for validating HTML documentation (Optional)

The following commands will install the required dependencies (Tested on Ubuntu 16.04)
```bash
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install gfortran
sudo apt-get install zlib1g-dev
```

##Getting FIDASIM source
It's rather difficult to run software you haven't downloaded. There are two ways of getting the source code.

###Downloading source directly
The most recent version of FIDASIM ({!../VERSION!}) can be downloaded from [here](https://github.com/D3DEnergetic/FIDASIM/releases)
Once you have downloaded the `.tar.gz` or `.zip` file unpack it using the following commands.
```bash
tar -zxf FIDASIM-{!../VERSION!}.tar.gz
```
or if you downloaded the `.zip` file
```bash
unzip FIDASIM-{!../VERSION!}.zip
```

There should now be a directory named `FIDASIM-{!../VERSION!}` in your current directory. Enter the directory using
```bash
cd FIDASIM-{!../VERSION!}
```

###Cloning the git repository
If you are planning to develop FIDASIM it is best to use git.
```bash
git clone https://github.com/D3DEnergetic/FIDASIM.git FIDASIM
cd FIDASIM
```

By default you will be on the master branch which may or may not be in a usable state.
To use the release version run the command
```bash
git checkout v{!../VERSION!}
```

##Setting up environmental variables
FIDASIM needs to know where some things are so you have to set the following environmental variables in your `.bashrc` file located in your home directory.
```bash
export FIDASIM_DIR=/path/to/fidasim/install/directory
export FC=gfortran #use 'ifort' for Intel Fortran compiler
export CC=gcc      #use 'icc' for Intel C compiler
export CXX=g++     #use 'icpc' for Intel C++ compiler

#For using helper routines
export PATH=$FIDASIM_DIR/deps/hdf5/bin:$FIDASIM_DIR/lib/scripts:$PATH
export IDL_PATH="+$FIDASIM_DIR:$IDL_PATH:<IDL_DEFAULT>"
export PYTHONPATH=$FIDASIM_DIR/lib/python:$PYTHONPATH

ulimit -s unlimited #Omit this if you like segfaults
```
replacing `/path/to/fidasim/install/directory` with the real directory. To set the environmental variables in the current shell run
```
source ~/.bashrc
```

##Building FIDASIM
Once you are in the source directory (and have all the dependencies installed) run the following
```bash
make
```
Once running, go get a coffee since it will take a while because HDF5 is being built as well.

Once make has completed check if FIDASIM compiled correctly.
```bash
user@computer:~/FIDASIM-{!../VERSION!}$ ./fidasim
   ____ ____ ___   ___    ____ ____ __  ___
  / __//  _// _ \ / _ |  / __//  _//  |/  /
 / _/ _/ / / // // __ | _\ \ _/ / / /|_/ /
/_/  /___//____//_/ |_|/___//___//_/  /_/

Version: 1.0.1

FIDASIM is released as open source code under the MIT Licence.
For more information visit http://d3denergetic.github.io/FIDASIM/

usage: ./fidasim namelist_file [num_threads]
```
Good job! You installed FIDASIM! But wait there's more.

##Generating Atomic Tables
Calculating reaction rates on the fly is time consuming so we need to pre-compute them to save time.
The following code snippit will generate the atomic tables using the default settings.
The default settings should be appropriate for most use cases, however, it may be necessary to generate custom atomic tables.
In that case edit the file `tables/default_settings.dat` before running the following command

```bash
make atomic_tables
```

@warning
This is computationally expensive so make sure you run this on a computer
where you won't get angry emails for using up all the CPU's.
Optionally you can add `NTHREADS=#` to the command to set the number of threads.

Now would be a good time to get more coffee... or maybe a nap.

##Run a test case
From within IDL
```idl
IDL> run_tests, "/place/where/you/want/the/output"
```

Some stuff that will make sense later will flash by and when its done you should see something like
```text
SUCCESS: FIDASIM pre-processing completed
To run FIDASIM use the following command
/u/lstagner/FIDASIM/fidasim /p/fida/lstagner/TEST/test_1a_inputs.dat

```

Now do what the computer says.
Think of as good practice for when the [robots take over](https://www.youtube.com/watch?v=7Pq-S557XQU).

It should print out the following.
```
[lstagner@dawson061]% /u/lstagner/FIDASIM/fidasim /p/fida/lstagner/TEST/test_1a_inputs.dat
   ____ ____ ___   ___    ____ ____ __  ___
  / __//  _// _ \ / _ |  / __//  _//  |/  /
 / _/ _/ / / // // __ | _\ \ _/ / / /|_/ /
/_/  /___//____//_/ |_|/___//___//_/  /_/

Version: 1.0.0

FIDASIM is released as open source code under the MIT Licence.
For more information visit http://d3denergetic.github.io/FIDASIM/

---- Shot settings ----
 Shot:        1
 Time: 1000 [ms]
 Runid: test_1a

---- Input files ----
 Tables file: /u/lstagner/FIDASIM/tables/atomic_tables.h5
 Geometry file: /p/fida/lstagner/TEST/test_1a_geometry.h5
 Equilibrium file: /p/fida/lstagner/TEST/test_1a_equilibrium.h5
 Distribution file: /p/fida/lstagner/TEST/test_1a_distribution.h5

---- OpenMP settings ----
 Number of threads: 16

---- Beam grid settings ----
 Nx:  50
 Ny:  60
 Nz:  70
 dV:  8.00 [cm^3]
 alpha:  0.00 [rad]
 beta:   0.00 [rad]
 gamma:  0.00 [rad]
 origin: [   0.00,   0.00,   0.00] [cm]

---- Interpolation grid settings ----
 Nr:  70
 Nz: 100
 dA: 4.10 [cm^2]

---- Neutral beam settings ----
 Beam: test_beam
 Power:    1.70 [MW]
 Voltage: 72.50 [keV]

---- Atomic tables settings ----
 Maximum n/m:  6
 Beam/Fast-ion mass:  2.014 [amu]
 Thermal/Bulk-ion mass:  2.014 [amu]
 Impurity mass: 12.011 [amu]

---- Fast-ion distribution settings ----
 Distribution type: Fast-ion Density Function F(energy,pitch,R,Z)
 Nenergy =   6
 Npitch  =   6
 Energy range = [67.33, 75.44]
 Pitch  range = [-0.10, 0.10]

---- FIDA/BES settings ----
 FIDA/BES System: SPECTRAL
 Number of channels:   3

---- NPA settings ----
 NPA System: NPA
 Number of channels:   3
 Calculating hit probabilities for NPA channels

ndmc:     1:43:23
     # of markers:     50000
   birth profile written to: /p/fida/lstagner/TEST/test_1a_birth.h5

dcx:      1:43:41
     # of markers:    505020
   dcx written to: /p/fida/lstagner/TEST/test_1a_dcx.h5

halo:     1:44:32
     # of markers:    505180
     # of markers:    310573
     # of markers:    188148
     # of markers:    110872
     # of markers:     62806
     # of markers:     32484
     # of markers:     13881
   neutral density written to: /p/fida/lstagner/TEST/test_1a_neutrals.h5

bremsstrahlung:     1:46:25

fida:     1:46:25
     # of markers:   5049813

   Spectra written to: /p/fida/lstagner/TEST/test_1a_spectra.h5

npa:     1:47:46
     # of markers:    505074
Number of NPA particles that hit a detector:   125638

   NPA data written to: /p/fida/lstagner/TEST/test_1a_npa.h5

fida weight function:     1:49:46
 Number of Channels:   3
 Nlambda: 1000
 Nenergy:  50
 Npitch:  50
 Ngyro: 100
 Maximum Energy:  100.00
 LOS averaged: True

   Channel:   1
   Radius:  200.00
   Mean Fast-ion Density:    7.97429E+11

   Channel:   2
   Radius:  170.00
   Mean Fast-ion Density:    7.98346E+11

   Channel:   3
   Radius:  140.00
   Mean Fast-ion Density:    7.98330E+11

   FIDA weights written to: /p/fida/lstagner/TEST/test_1a_fida_weights.h5

npa weight function:     1:50:02
 Number of Channels:   3
 Nenergy:  50
 Npitch:  50
 Maximum energy:  100.00

   Channel:   1
   Radius:    200.000
   Flux:      1.22243E+14
   Weight:    3.79893E+03

   Channel:   2
   Radius:    170.000
   Flux:      1.07364E+14
   Weight:    1.85565E+03

   Channel:   3
   Radius:    140.000
   Flux:      3.46488E+13
   Weight:    8.81099E+02

   NPA weights written to: /p/fida/lstagner/TEST/test_1a_npa_weights.h5

END: hour, minute, second:  1:53:07
duration:                   0:15:53
```

Congratulations! You followed the instructions.

##Now what
Most likely you won't be satisfied by just running a test case. Click [here](./02_preprocess.html) to learn how to make the input files used by FIDASIM.
