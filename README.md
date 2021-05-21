# MDT_Singularity_OpenCL
**Singularity recipe for MDT using Intel OpenCL drivers**

Note, this drew heavy inspiration from [akhanf's work](https://github.com/akhanf/mdt-singularity). Issues there were mainly from out of date code.  

## Requirements ##
You must provide a copy of `l_opencl_p_18.1.0.015.tgz`. These can be freely obtained from [Intel](https://software.intel.com/content/www/us/en/develop/articles/opencl-drivers.html#cpu-section).  You'll see the [Download](https://registrationcenter.intel.com/en/forms/?productid=3206) link in there points to a registration page.  Registration is free, but since these OpenCL drivers require registration, I am not including them here.

## Overview ##
Once you've cloned this repo and downloaded the `l_opencl_p_18.1.0.015.tgz` Intel OpenCL driver, this Singularity recipe will create an Ubuntu 18.04 (bionic) based image, install the Intel OpenCL drivers, and install the [Microstructure Diffusion Toolbox (MDT)](https://github.com/robbert-harms/MDT).  It's been tested with Singularity 3.5 running on an HPC and it nicely got all 144 cores on a machine active (which ran about 7x faster than a workstation with a GTX 1080 card).

## Installation ##
With the `l_opencl_p_18.1.0.015.tgz` in the same folder:

`singularity build --fakeroot mdt_opencl.sif mdt_final_recipe`

**Note** Odds are you will have both the Intel OpenCL drivers in place and the [Portable Compute Language (POCL)](http://portablecl.org/) drivers in place and the POCL drivers will slow this to a single-CPU crawl. You want to make sure MDT only uses the Intel OpenCL drivers by either using the `--cl-device-ind 0` parameter when doing your model fit:

`mdt-model-fit NODDI foo_dwi.nii foo_dwi.prtcl foo_mask.nii --cl-device-ind 0`

or by editing your `mdt.conf` file:
```
runtime_settings:
    # The single device index or a list with device indices to use during OpenCL processing.
    # For a list of possible values, please run mdt_list_devices or view the device list in the GUI.
    cl_device_ind: 0
```
