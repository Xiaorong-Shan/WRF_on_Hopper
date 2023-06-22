# Procedures to install pre-requisite and WRF on hopper



System info

(base) [Mon Jun 19 14:08:14 vy57456 source_code]$uname -a
Linux taki-usr1 3.10.0-1160.11.1.el7.x86_64 #1 SMP Fri Dec 18 16:34:56 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux



# Pre-requisite
General procedures to install softwares on Linux: 
1. configure
2. make
3. make check
4. make install 
It’s necessary to save log files in each step using >&log. and check log file in each steps to make sure there's no errors

## Directory and path setting
Before we install all the libraries, it's necessary to set up a new directory and environment for WRF.

Create a directory where you want to put all the libraries.

```
cd /projects/HAQ_LAB/xshan2/WRF/
mkdir pre-softwares
cd pre-softwares
```

Make another file where we save all the original download files
```
mkdir source_code
cd source_code
```
Download necessary tar.gz file from https://drive.google.com/drive/folders/1RUBF1R8L-bNvVQwWsFB7dwVCh_5wIdCX

Untar the file, for example, for szip-2.1.1

```
tar -xvf szip-2.1.1.tar.gz
```

Load necessary modules
```
module load intel/2020.2
```

Find CC (icc), FC (ifort) and CXX (icpc) path. 
```
which icc
which ifort
which icpc
```

Go into bash file
```
vi ~/.bashrc
```

Put the following path into bash file with vi
```
module load intel/2020.2
export PATH=/opt/intel/2020.2/compilers_and_libraries_2020.2.254/linux/bin/intel64:$PATH
export CC=/opt/intel/2020.2/compilers_and_libraries_2020.2.254/linux/bin/intel64/icc
export FC=/opt/intel/2020.2/compilers_and_libraries_2020.2.254/linux/bin/intel64/ifort
export CXX=/opt/intel/2020.2/compilers_and_libraries_2020.2.254/linux/bin/intel64/icpc
```

## Install libraries

### szip-2.1.1

Install szip-2.1.1 using GCC/4.8.2

Documentation to install szip: https://apolo-docs.readthedocs.io/en/latest/software/scientific_libraries/Szip/Szip-2.1.1/index.html
Download website: https://support.hdfgroup.org/ftp/lib-external/szip/2.1.1/src/szip-2.1.1.tar.gz 

**1. configure**

Set environment before configure, i.e., CC, FC, CXX
prefix: specify the directory where to install. (Different from the location of the source code)

Build a new file to save szip-2.1.1 documents in pre-softwares directory

```
mkdir szip-2.1.1
```

Start to configure the library in the source_code file
```
cd /projects/HAQ_LAB/xshan2/WRF/pre-softwares/source_code/szip-2.1.1
./configure prefix=/projects/HAQ_LAB/xshan2/WRF/pre-softwares/szip-2.1.1
```

**2. make**

```
make >& make.log
```

check if there's any error 
```
vi make.log
```

**3. make check**

```
make check >&make_check.log
```

check if there's any error 
```
vi make_check.log
```

**4. make install**

```
make install >&make_install.log
```

check if there's any error 
```
vi make_install.log
```


### zlib-1.2.11
Repeat the similar steps like installing szip-2.1.1

install zlib-1.2.11 using GCC/4.8.2
https://sourceforge.net/projects/libpng/files/zlib/1.2.11/ 


**1. configure**
   
```
./configure --prefix=/projects/HAQ_LAB/xshan2/WRF/pre-softwares/zlib-1.2.11
```

**2. make test**

```
make test >&make_test.log
```

**3. make install**
   
```
make install >&make_install.log
```
### hdf5-1.10.4
Repeat the similar steps like installing szip-2.1.1

Installing HDF (hdf5-1.10.4) on WSC using pgi
module load pgi/18.10
Download links:
https://www.hdfgroup.org/downloads/hdfview/?gclid=CjwKCAiA2fjjBRAjEiwAuewS_ZPIkjO-vSQgnLXrSZ68O5I7I6QFaLEpAxcXWmvlhGzzxN3qdXuPDhoCVgIQAvD_BwE#source

**1. environment setup**

(1) Method 1: environment setup method
export CC=/opt/pgi/linuxpower/18.10/bin/pgcc
export CXX=/opt/pgi/linuxpower/18.10/bin/pgc++
export FC=/opt/pgi/linuxpower/18.10/bin/pgf90
 
(2) Method 2: adding flags to the configure
CC=/opt/pgi/linuxpower/18.10/bin/pgcc CXX=/opt/pgi/linuxpower/18.10/bin/pgc++ FC=/opt/pgi/linuxpower/18.10/bin/pgf90

**2. configure**

```
./configure --enable-fortran --enable-cxx CC=/opt/pgi/linuxpower/18.10/bin/pgcc CXX=/opt/pgi/linuxpower/18.10/bin/pgc++ FC=/opt/pgi/linuxpower/18.10/bin/pgf90 --prefix=/projects/HAQ_LAB/xshan2/WRF/pre-softwares/hdf5-1.10.4
```
 
**3. make**

```
make &>make.log
```

**4. make check**

```
make check &>make_check.log
```


**5. make install**

```
make install &>make_install.log
``` 







### netcdf-4.6.1 (C)
Install netcdf-4.6.1 (C) using GCC/4.8.2
Donwload website: https://downloads.unidata.ucar.edu/netcdf/ 
1. configure
(base) [Wed Jun 17 15:35:31 vy57456 netcdf-4.6.1]$./configure FC=/usr/ebuild/software/GCC/4.8.2/bin/gfortran CXX=/usr/ebuild/software/GCC/4.8.2/bin/c++ LDFLAGS="-L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/lib -L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/lib" CPPFLAGS="-I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/include -I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/include -mcmodel=large" CFLAGS=' ' --disable-netcdf-4 --prefix=/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1
2. make
(base) [Wed Jun 17 15:41:02 vy57456 netcdf-4.6.1]$make |tee make.log
3. make
(base) [Wed Jun 17 15:41:02 vy57456 netcdf-4.6.1]$make install |tee make_install.log
 
ERROR1: while configuring
(base) [Tue Jun 16 17:36:00 vy57456 netcdf-4.6.1]$./configure FC=/usr/ebuild/software/GCC/4.8.2/bin/gfortran CXX=/usr/ebuild/software/GCC/4.8.2/bin/c++ LDFLAGS="-L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/lib -L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/lib" CPPFLAGS="-I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/include -I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/include -mcmodel=large" CFLAGS=' ' --disable-netcdf-4 --prefix=/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1
checking for gcc... gcc
checking whether the C compiler works... no
configure: error: in `/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/source_code/netcdf-4.6.1':
configure: error: C compiler cannot create executables
See `config.log' for more details
Here are details in 'config.log'
configure:4492: $? = 0
configure:4481: gcc -v >&5
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/umbc/ebuild-soft/skylake/software/GCC/4.8.2/bin/../libexec/gcc/x86_64-unknown-linux-gnu/4.8.2/lto-wrapper
Target: x86_64-unknown-linux-gnu
Configured with: ../configure --enable-languages=c,c++,fortran --enable-lto --enable-checking=release --disable-multilib --enable-shared=yes --enable-static=yes --enable-threads=posix --enable-gold=default --enable-plugins --enable-ld --with-plugin-ld=ld.gold --enable-bootstrap --prefix=/usr/ebuild/software/GCC/4.8.2 --with-local-prefix=/usr/ebuild/software/GCC/4.8.2
Thread model: posix
gcc version 4.8.2 (GCC)
configure:4492: $? = 0
configure:4481: gcc -V >&5
gcc: error: unrecognized command line option '-V'
gcc: fatal error: no input files
compilation terminated.
configure:4492: $? = 1
configure:4481: gcc -qversion >&5
gcc: error: unrecognized command line option '-qversion'
gcc: fatal error: no input files
compilation terminated.
configure:4492: $? = 1
configure:4512: checking whether the C compiler works
configure:4534: gcc   -I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/include -I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/include -mcmodel=large -L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/hdf5-1.10.4/lib -L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/szip-2.1.1/lib conftest.c -lmfhdf -ldf -lhdf5_hl -lhdf5 -lm -lz -lcurl  >&5
/usr/ebuild/software/binutils/2.30-GCCcore-7.3.0/bin/ld: cannot find -lmfhdf
/usr/ebuild/software/binutils/2.30-GCCcore-7.3.0/bin/ld: cannot find -ldf
collect2: error: ld returned 1 exit status
SOLUTION: The error is caused by the flags '-lmfhdf' and '-ldf'. Remove these two flags in the LIBS.
(base) [Wed Jun 17 15:35:31 vy57456 netcdf-4.6.1]$export LIBS="-lhdf5_hl -lhdf5 -lm -lz -lcurl "
previous it was
export LIBS="-lmfhdf -ldf -lhdf5_hl -lhdf5 -lm -lz -lcurl "


### netcdf-fortran-4.4.4

Install netcdf-fortran-4.4.4
Download website: https://github.com/Unidata/netcdf-fortran/tags 
1. environment setup
(base) [Wed Jun 17 15:44:47 vy57456 netcdf-fortran-4.4.4]$export LD_LIBRARY_PATH="/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1/lib:${LD_LIBRARY_PATH}"
(base) [Wed Jun 17 15:44:50 vy57456 netcdf-fortran-4.4.4]$export CPPFLAGS="-I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1/include -mcmodel=large"
(base) [Wed Jun 17 15:46:01 vy57456 netcdf-fortran-4.4.4]$export LDFLAGS="-L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1/lib"
2. configure
(base) [Wed Jun 17 15:56:50 vy57456 netcdf-fortran-4.4.4]$./configure CC=/usr/ebuild/software/GCC/4.8.2/bin/gcc FC=/usr/ebuild/software/GCC/4.8.2/bin/gfortran CXX=/usr/ebuild/software/GCC/4.8.2/bin/c++ --prefix=/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1 |tee configure.log
3. make
(base) [Wed Jun 17 15:56:50 vy57456 netcdf-fortran-4.4.4]$make |tee make.log
4. make install
(base) [Wed Jun 17 15:56:50 vy57456 netcdf-fortran-4.4.4]$make install |tee make_install.log
ERROR1 while configure
checking for gcc... gcc
checking whether the C compiler works... no
configure: error: in `/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/source_code/netcdf-fortran-4.4.4':
configure: error: C compiler cannot create executables
See `config.log' for more details
configure:4026: $? = 0
configure:4015: gcc -v >&5
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/umbc/ebuild-soft/skylake/software/GCC/4.8.2/bin/../libexec/gcc/x86_64-unknown-linux-gnu/4.8.2/lto-wrapper
Target: x86_64-unknown-linux-gnu
Configured with: ../configure --enable-languages=c,c++,fortran --enable-lto --enable-checking=release --disable-multilib --enable-shared=yes --enable-static=yes --enable-threads=posix --enable-gold=default --enable-plugins --enable-ld --with-plugin-ld=ld.gold --enable-bootstrap --prefix=/usr/ebuild/software/GCC/4.8.2 --with-local-prefix=/usr/ebuild/software/GCC/4.8.2
Thread model: posix
gcc version 4.8.2 (GCC)
configure:4026: $? = 0
configure:4015: gcc -V >&5
gcc: error: unrecognized command line option '-V'
gcc: fatal error: no input files
compilation terminated.
configure:4026: $? = 1
configure:4015: gcc -qversion >&5
gcc: error: unrecognized command line option '-qversion'
gcc: fatal error: no input files
compilation terminated.
configure:4026: $? = 1
configure:4046: checking whether the C compiler works
configure:4068: gcc  -I/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1/include -mcmodel=large -L/home/vy57456/zzbatmos_user/application/gfortran/4.8.2/netcdf-4.6.1/lib conftest.c -lhdf5_hl -lhdf5 -lm -lz -lcurl  >&5
/usr/ebuild/software/binutils/2.30-GCCcore-7.3.0/bin/ld: cannot find -lhdf5_hl
/usr/ebuild/software/binutils/2.30-GCCcore-7.3.0/bin/ld: cannot find -lhdf5
collect2: error: ld returned 1 exit status
configure:4072: $? = 1
configure:4110: result: no
configure: failed program was:
| /* confdefs.h */
| #define PACKAGE_NAME "netCDF-Fortran"
| #define PACKAGE_TARNAME "netcdf-fortran"
| #define PACKAGE_VERSION "4.4.4"
| #define PACKAGE_STRING "netCDF-Fortran 4.4.4"
| #define PACKAGE_BUGREPORT "support-netcdf@unidata.ucar.edu"
| #define PACKAGE_URL ""
| #define PACKAGE "netcdf-fortran"
| #define VERSION "4.4.4"
| /* end confdefs.h.  */
SOLUTION1:
The issue was caused by the flags "-lhdf5_hl" and "-lhdf5"
set LIBS as
(base) [Wed Jun 17 15:52:46 vy57456 netcdf-fortran-4.4.4]$export LIBS="-lm -lz -lcurl "

### mpich-3.2.1
Install MPICH (mpich-3.2.1) on maya using gcc (module load gcc/4.8.4)
Download website: https://www.mpich.org/downloads/versions/

1. environment setup
setup C compiler
module load gcc/4.8.4
 
2. configure
[vy57456@maya-usr1 mpich-3.2.1]$ ./configure --prefix=/home/vy57456/zzbatmos_user/application/gfortran/mpich-3.2.1 CC=gcc CXX=g++ FC=gfortran F77=gfortran &>configure.log 
 
3. make
[vy57456@maya-usr1 mpich-3.2.1]$ make 2>&1 | tee m.txt
 
4. make install
[vy57456@maya-usr1 mpich-3.2.1]$make install 2>&1 | tee mi.txt
 
NOTE: You need to manually specify the C and FORTRAN compiler, since if you do not specify, the configuration may the default compiler which may be not what you want. And you may have the following error due to the mix of ifort and gcc.
 
libtool: link: icpc -shared  src/binding/cxx/.libs/initcxx.o   -Wl,-rpath,/home/vy57456/zzbatmos_user/application/gfortran/source_code/mpich-3.2.1/lib/.libs -Wl,-rpath,/cm/shared/apps/gcc/4.8.4/lib/../lib -Wl,-rpath,/home/vy57456/zzbatmos_user/application/gfortran/mpich-3.2.1/lib -Wl,-rpath,/cm/shared/apps/gcc/4.8.4/lib/../lib lib/.libs/libmpi.so -lpthread -L/cm/shared/apps/intel/composer_xe/2015.5.223/ipp/../compiler/lib/intel64 -L/cm/shared/apps/intel/composer_xe/2015.5.223/ipp/lib/intel64 -L/cm/shared/apps/intel/composer_xe/2015.5.223/compiler/lib/intel64 -L/cm/shared/apps/intel/composer_xe/2015.5.223/mkl/lib/intel64 -L/cm/shared/apps/intel/composer_xe/2015.5.223/tbb/lib/intel64/gcc4.4 -L/cm/shared/apps/slurm/14.11.11/lib64/slurm -L/cm/shared/apps/slurm/14.11.11/lib64 -L/usr/cluster/contrib/hdf5/install-1.8.16/lib -L/cm/shared/apps/acml/current/ifort64/lib -L/cm/shared/apps/slurm/14.11.11/lib64/../lib64 -L/cm/shared/apps/slurm/14.11.11/lib64/../lib64/ -L/cm/shared/apps/gcc/4.8.4/lib/gcc/x86_64-unknown-linux-gnu/4.8.4/ -L/cm/shared/apps/gcc/4.8.4/lib/gcc/x86_64-unknown-linux-gnu/4.8.4/../../../../lib64 -L/cm/shared/apps/gcc/4.8.4/lib/gcc/x86_64-unknown-linux-gnu/4.8.4/../../../../lib64/ -L/lib/../lib64 -L/lib/../lib64/ -L/usr/lib/../lib64 -L/usr/lib/../lib64/ -L/cm/shared/apps/intel/composer_xe/2015.5.223/ipp/../compiler/lib/intel64/ -L/cm/shared/apps/intel/composer_xe/2015.5.223/ipp/lib/intel64/ -L/cm/shared/apps/intel/composer_xe/2015.5.223/compiler/lib/intel64/ -L/cm/shared/apps/intel/composer_xe/2015.5.223/mkl/lib/intel64/ -L/cm/shared/apps/intel/composer_xe/2015.5.223/tbb/lib/intel64/gcc4.4/ -L/cm/shared/apps/slurm/14.11.11/lib64/slurm/ -L/cm/shared/apps/slurm/14.11.11/lib64/ -L/usr/cluster/contrib/hdf5/install-1.8.16/lib/ -L/cm/shared/apps/acml/current/ifort64/lib/ -L/cm/shared/apps/gcc/4.8.4/lib/gcc/x86_64-unknown-linux-gnu/4.8.4/../../../ -L/lib64 -L/lib/ -L/usr/lib64 -L/usr/lib -limf -lirng -lm -lipgo -ldecimal -lcilkrts /cm/shared/apps/gcc/4.8.4/lib/../lib/libstdc++.so -lintlc -lsvml -lgcc -lgcc_s -lirc_s -ldl -lc  -O2   -Wl,-soname -Wl,libmpicxx.so.12 -o lib/.libs/libmpicxx.so.12.1.1
/cm/shared/apps/gcc/4.8.4/lib/../lib/libstdc++.so: could not read symbols: File in wrong format
make[2]: *** [lib/libmpicxx.la] Error 1
make[2]: Leaving directory `/umbc/xfs1/zzbatmos/users/vy57456/application/gfortran/source_code/mpich-3.2.1'
make[1]: *** [all-recursive] Error 1
make[1]: Leaving directory `/umbc/xfs1/zzbatmos/users/vy57456/application/gfortran/source_code/mpich-3.2.1'
make: *** [all] Error 2
Solution: specify CC=gcc CXX=g++ FC=gfortran F77=gfortran
and also add the compiler you want into PATH by using the following analogous command
export PATH=/home/vy57456/zzbatmos_user/application/gfortran/mpich-3.2.1./bin:$PATH
 






WRF
[vy57456@maya-usr1 WRF]$./configure
[vy57456@maya-usr1 WRF]$./compile em_real 2>&1 |tee compile_em_real.log








WRF-Chem
(If you do not need WRF-Chem, just skip this step)
[vy57456@maya-usr1 WRF]$./compile emi_conv 2>&1 |tee compile_emi_conv.log








WPS

[vy57456@maya-usr1 WPS]$./configure
[vy57456@maya-usr1 WPS]$./compile >&compile.log

errors while installing WPS
2.1 wrf_io.f:(.text+0x1e5): undefined reference to `GOMP_loop_runtime_start'
This error was solved from online help. Here is the link.
http://forum.wrfforum.com/viewtopic.php?f=20&t=5672
I had the same problem on Centos 6.4 (64 bit) with gcc+gfortran, with "smpar" option on WRF & "serial" option on WPS. The very simple solution is to add "-lgomp" to the WRF_LIB variable in file configure.wps (just append it after -lnetcdf).
 
modify from
                        -L$(NETCDF)/lib -lnetcdff -lnetcdf
to
                        -L$(NETCDF)/lib -lnetcdff -lnetcdf -lgomp
Similarly, flag -lgomp is related to openmp.
Setting these GCC compiler flags should turn on OpenMP
CFLAGS=-fopenmp
LDFLAGS=-lgomp
For icc you would use
CFLAGS=-openmp -openmp_report0
LDFLAGS=-lsvml
 
2.2 fatal error: jasper/jasper.h: No such file or directory
This error is due to missing library file. We need to install or use already installed jasper. Here is the jasper installed on maya, without knowing its compiler and version. But we can setup its environment as
 
export PATH=/usr/cluster/contrib/wrf/Build_WRF/LIBRARIES/grib2/bin:$PATH
 
And modify configure.wps
modify to
COMPRESSION_LIBS    = -L/usr/cluster/contrib/wrf/Build_WRF/LIBRARIES/grib2/lib -ljasper -lpng -lz
COMPRESSION_INC     = -I/usr/cluster/contrib/wrf/Build_WRF/LIBRARIES/grib2/include
 
The directory /usr/cluster/contrib/wrf/Build_WRF/LIBRARIES/grib2 is getting from wrf/3.7 module. You can have a look in detail using the command
[vy57456@maya-usr1 WPS]$ vi /usr/cluster/contrib/modulefiles/wrf/3.7
 
2.3 gfortran: error: unrecognized command line option ‘-f90=gfortran’
OR
gcc: error: unrecognized command line option ‘-cc=gcc’
This error was solved from online help. Here is the link.
http://forum.wrfforum.com/viewtopic.php?f=5&t=1501
 
Try to remove "-f90=$(SFC)" and "-cc=$(SCC)" from configure.wrf.
I think this happens when using OpenMPI.











































