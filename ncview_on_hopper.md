# Setup
## Start an interactive job
```
srun --mem 15g -t 0-02:00 -c 1 -N 1 --pty /bin/bash
```

## Set environmental variables
You can run the following commands before each run: 
```
csh
setenv DIR ~/lhennem/Build_WRF/LIBRARIES/
setenv NETCDF $DIR/netcdf
```
...or add the following to your `~/.bash_profile`:
```
DIR=~/lhennem/Build_WRF/LIBRARIES
NETCDF=$DIR/netcdf
```
## Download the input meteorological data.
[Download the meteorological data.](https://github.com/HAQ-Lab/data/WRF_on_ARGO/wiki/Download-and-organize-NARR-data)

In the download process, you will create a WRF run directory that houses the untarred `merged_...` files in 4 seasonal directories (run_wint, run_spri, run_summ, and run_wint). Copy the namelist.wps file into the top level directory.

```
cp /projects/HAQ_LAB/lhennem/Build_WRF/WPS/namelist.wps .
```

## Edit namelist.wps
To add: information on group grid domains
```
start_date = '2007-01-01_00:00:00', '2007-01-01_00:00:00', '2007-01-01_00:00:00',
end_date   = '2007-12-31_21:00:00', '2007-12-31_21:00:00', '2007-12-31_21:00:00',
interval_seconds = 10800,
...
 geog_data_path = '/projects/HAQ_LAB/data/WRF_inputs/WPS_GEOG',
 opt_output_from_geogrid_path = ‘/projects/HAQ_LAB/lhennem/runWRF/2017’,
...
 prefix = 'GRIBOUT/NARR',
...
 fg_name = 'GRIBOUT/NARR'
```
# Geogrid
## View grid domain
This doesn't work for me.
```
ncl util/plotgrids.ncl
```
## Run Geogrid
First, link in the geogrid executable and GEOGRID table.
```
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/geogrid.exe geogrid.exe
mkdir geogrid
cp /projects/HAQ_LAB/lhennem/Build_WRF/WPS/geogrid/GEOGRID.TBL geogrid
```
Next, run Geogrid.
```
./geogrid.exe
```

# Ungrib
In the download process, you will create a WRF run directory that houses the untarred `merged_...` files in 4 seasonal directories (run_wint, run_spri, run_summ, and run_wint). You'll need to run ungrib in each directory.
1. Navigate to your WRF run directory that houses the 4 seasonal directories
2. Link in the group ungrib.exe executable and Vtable into each seasonal directory and link link_grib.sh to the upper level:
```
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/link_grib.csh link_grib.csh
cd run_wint
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib.exe ungrib.exe
ln -sf /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib/Variable_Tables/Vtable.NARR Vtable
cd ../run_spir
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib.exe ungrib.exe
ln -sf /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib/Variable_Tables/Vtable.NARR Vtable
cd ../run_summ
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib.exe ungrib.exe
ln -sf /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib/Variable_Tables/Vtable.NARR Vtable
cd ../run_fall
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib.exe ungrib.exe
ln -sf /projects/HAQ_LAB/lhennem/Build_WRF/WPS/ungrib/Variable_Tables/Vtable.NARR Vtable
cd ..
```
3. Create a separate version of `namelist.wps` for each season, edit with the correct dates, and link to each directory. For example:
```
cp namelist.wps namelist.wps_wint
vi namelist.wps_wint
cd run_wint
ln -sf ../namelist.wps_wint namelist.wps
```
    The appropriate dates for each season are:
    - wint = Jan (1/1-31), Feb (2/1-28 or 2/1-29), Mar (3/1-31)
    - spri = Apr (4/1-30),  May(5/1-31), Jun (6/1-30)
    - summ = Jul (7/1-31), Aug (8/1-31cd ), Sep (9/1-30)
    - fall = Oct (10/1-31), Nov (11/1-30), Dec (12/1-31)
4. Run the link_grib for each season. This will create many links in each seasonal directory (e.g., GRIBFILE.AAA). In addition, create a directory called `GRIBOUT` that will house the ungribbed files.
```
cd run_wint
../link_grib.csh merge/merged
mkdir GRIBOUT
cd ../run_spir
../link_grib.csh merge/merged
mkdir GRIBOUT
cd ../run_summ
../link_grib.csh merge/merged
mkdir GRIBOUT
cd ../run_fall
../link_grib.csh merge/merged
mkdir GRIBOUT
cd ..
```
5. Run ungrib in each seasonal directory. For example:
```
cd run_wint
./ungrib.exe  >&  ungrib_data.log
```
Check that `ungrib_data.log` says "SUCCESSFUL COMPLETION OF UNGRIB" near the bottom of the file. 
6. Clean up each directory:
```
cd run_wint
rm GRIBFILE*
cd ..
```
# Metgrid
## Setup
In each seasonal directory, perform the following:
```
cd run_wint
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/metgrid.exe  metgrid.exe
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/metgrid metgrid
ln -s /projects/HAQ_LAB/lhennem/Build_WRF/WPS/geo_em.* .
cd ..
```
## Run metgrid in each directory.
```
cd run_wint
./metrid.exe
```
Clean up
```
mkdir met_d01
mv met_em.d01*.nc met_d01
mkdir met_d02
mv met_em.d02*.nc met_d02
```

# Use ncview on hopper

Go to web:https://ondemand.orc.gmu.edu/pun/sys/dashboard/batch_connect/sessions

Click on **"Desktop"**, choose **"Intel"** as node type and then launch the session.

Go into the session and open the terminal. Then type


```
module load intel
module load impi
module load ncview
```
Then type **ncview** with script path and name to run it,
