# ROMS_AGRIF Installation Ubuntu 17.04

## Installation of Roms_tools

Download ROMS_AGRIF

[ROMS_AGRIF v3.1.1](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/CODE/Roms_Agrif_v3.1.1_07_07_2014.tar.gz)

Uncompress tar

    tar -zxvf Roms_Agrif_v3.1.1_07_07_2014.tar.gz

this will create a folder named `Roms_tools`. Download ROMSTOOLS

[ROMSTOOLS v3.1.1](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/TOOLS/ROMSTOOLS_v3.1.1_07_07_2014.tar.gz)

and uncompress these tools into the `Roms_tools` folder:

    tar -zxvf ROMSTOOLS_v3.1.1_07_07_2014.tar.gz

Download the toolboxes and softwares nedded by ROMSTOOLS

[Utilities](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/UTILITIES/Utilities_ROMSTOOLS_v3.0_21_12_2012.tar.gz)

and uncompress them:

    tar -zxvf Utilities_ROMSTOOLS_v3.0_21_12_2012.tar.gz

Copy and renamed `Run` folder:

    cd Roms_tools
    cp Run Run_whatever

Install required `netcdf` libraries:

    sudo apt install netcdf-bin libnetcdf-dev libnetcdff-dev

## Creation of required initial files

You will need download the needed datasets to create the required initial conditions.
These datasets ar in the CROCO Datasets website
[http://www.croco-ocean.org/download/datasets/](http://www.croco-ocean.org/download/datasets/)

### Grid creation

Open Matlab and add `Run_whatever` folder:

```
>> cd Run_whatever
>> start
```

execute `make_grid` to create netcdf with grid. To do this, we'll need the `etopo2.nc` file.

[Topo](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/Topo_26_05_2004.tar.gz)

Once you get the `etopo2.nc` put this in the path:

    Roms_tools/Topo/etopo2.nc

and then:

```
>> make_grid
```

> If this error appears
> Undefined function or variable 'bitmax'.
> open in Matlab the m_grid.m file and replace every bitmax by flintmax

### Surface boundary initial conditions
To make the forcing conditions, first we must get the needed netcdf files.

[COADS05](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/COADS05_2006_10_25.tar.gz)

extract file into the `Roms_tools` and execute:

```
>> make_forcing
```

###  Temperature and Salinity initial conditions
First, download the needed datasets. In this case get the CARS2009 dataset

[CARS2009](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/CARS2009_02_10_2013.tar.gz)

or

[WOA2009](ftp://ftp.legos.obs-mip.fr/pub/romsagrif/DATA_ROMS/WOA2009_24_06_2011.tar.gz)

extract into the `Roms_tools` and then execute:

```
>> make_ini
```

> We can execute the make_ini using CARS2009 or WOA2009. We can change the climate files
> modifying the romstools_param.h:
>         %climato_dir=cars2009_dir;
>         climato_dir=woa_dir;  % Select this to use the WOAPISCES

### Temperature and salinity boundary and initial condition
To execute this, only:

```
>> make_clim
```

## Compilation of model and running
After the execution of the last 4 steps, we'll have 4 `netcdf` files into the `ROMS_FILES` folder:

* roms_clm.nc
* roms_frc.nc
* roms_grd.nc
* roms_ini.nc

and other intermediate files not needed to get the model.

### Compilation in local (ONLY Linux)

Modify `jobcomp` file in the `Run_whatever` folder to use `lnetcdff`, adding:

    ...
    # If needed set your own NETCDF directories
    #
    NETCDFLIB="-L../../netcdf_x86_64 -lnetcdff"
    ...

and execute:

    ./jobcomp

after the compilation finish, then execute the model:

    ./roms roms.in

### Compilation in "Carlos Cloud"

#### Connecting from Windows

We'll needed:

* Putty
* WinSCP

With WinSCP we can copy the `nc` files from our `Run_whatever/ROMS_FILES` folder to our `rfirst/ROMS_FILES`
or whatever in the computer of Carlos (**CC**). Then we must use Putty to connect us
to the CC and launch the compilation of the model. To do this:

    jobcomp

and after this finish

    roms roms.in

If we want launch the model and get out from the console, we can execute:

    nohup roms roms.in > roms.out & 2>&1 &

this launch a process and return us a PID, or the number of the process. We can show
the process running with:

    ps

Once the model is created, then you must download the `nc` files to your computer. To do this, you can use
WinSCP.

> To get CC credentials, contact with Carlos
