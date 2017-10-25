# CASTEP2BoltzTraP interface

This directory contains CASTEP interface files to the ShengBTE package. ShengBTE solves the full linearized Boltzmann transport equation for phonons using an iterative method. The interface allows the user to convert the output files generated by CASTEP into a suitable format which can be used by ShengBTE.
ShengBTE is NOT part of CASTEP and the CASTEP authors are not responsible for it.
You can find further information on ShengBTE, including features and downloads, at http://www.shengbte.org/home

## Getting Started

You can obtained the interface either as part of the CASTEP distribution, or download the most recent version from https://github.com/ganphys/castep2shengbte

The current version of the interface (castep2shengbte.py) is compatible with ShengBTE v.1.1.1 (most recent version), although it should also work with all previous versions.


### Prerequisites

The interface is a python script which uses python2.7 and requires the following libraries: numpy

Before running the script the user needs a <seedname>.castep file obtained from a CASTEP Phonon run.

### Installing

There is no need to install the script. If numpy is missing, please try to install them through the terminal.

numpy

```
sudo apt-get install python-numpy
```

## Running castep2shengbte.py

You can run the interface by simply calling the script:

```
castep2shengbte.py <seedname> nx ny nz
```
where `nx ny nz` is the q-points grid.

If executed successfully, the script will create 2 output files which can be used in ShengBTE:

```
`CONTROL`
`FORCE_CONSTANTS_2ND`
```

Note that `PHONON_WRITE_FORCE_CONSTANTS: true` needs to be added to <seedname>.param or the force constants will not be written in <seedname>.castep.

### ShengBTE notes

IMPORTANT: Third order force constants are required by ShengBTE. These are stored in a file called `FORCE_CONSTANTS_3RD`. Please use thirdorder.py generate `FORCE_CONSTANTS_3RD`. For more information about thirdorder.py, please see the thirdorder.py manual. 

The CONTROL file contains the parameters used in ShengBTE. It is important to note that `ngrid(:)` copies the q-points grid, which is often not sufficient to converge the ShengBTE results, hence higher values should be tested.

ShengBTE use values of epsilon=1 (dielectric tensor) and born=0 (Born effective charge tensor) by default. These values can be changed in the CONTROL file by adding a few lines to the `&crystal` block. 

Epsilon values can be obtained with [OptaDOS](http://www.tcm.phy.cam.ac.uk/~ajm255/optados/index.html) (for more information, please check this [tutorial](http://www.castep.org/Tutorials/LowLossEELS)). Here is an example of how to format the epsilon data in CONTROL:


```
        epsilon(:,1)=19.643 0.00 0.00,
        epsilon(:,2)=0.00 19.643 0.00,
        epsilon(:,3)=0.00 0.00 19.643,
```
where this block needs to be placed in `&crystal`


Born charges can be calculated in CASTEP. For that purpose add `CALCULATE_BORN_CHARGES: true` to the <seedname>.param file. Born charges should be then given at the end of <seedname>.castep. Again place the data in the '&crystal` block in CONTROL:

```
        born(:,1,1)=2.67810 0.00 0.00,
        born(:,2,1)=0.00 2.67810 0.00,
        born(:,3,1)=0.00 0.00 2.67810,
        born(:,1,2)=-2.67558 0.00 0.00,
        born(:,2,2)=0.00 -2.67558 0.00,
        born(:,3,2)=0.00 0.00 -2.67558, 
```
where the second digit in the brackets represents the type of the atom. For example, if there are 3 species present in the unit cell, extra 3 lines with born(:,X/Y/Z,3) need to be added in addition to the above example.

For more information on the keywords present in CONTROL and the output generated by ShengBTE, please see: https://bitbucket.org/sousaw/shengbte/overview


## Author

* **Genadi Naydenov** - (https://github.com/ganphys/castep2shengbte)