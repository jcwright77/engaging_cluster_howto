# engaging_cluster_howto
A brief introduction to the engaging cluster for the NSE and PSFC groups. Assorted recipes and gists.

# Abstract

<img style="float: left; width:300px;" src="dt-common-streams-streamserver-cls_.jpg" width="300" align="left"/>
The Massachusetts Green Energy High Performance Computing Center
(MGHPCC) in Holyoke, MA is 100 miles from MIT campus connected
by formerly dark fiber (10Gb). Completed in November 2012, the 90, 000 square
foot, 15 megawatt facility is located on an 8.6 acre former
industrial site just a few blocks from City Hall. University
partners include B.U., NorthEaster, Harvard, UMass and MIT.

The engaging computer cluster is a large (~500 nodes)
multi-departmental compute cluster in the MGHPCC facility. NSE
and the PSFC together have 136 nodes in the cluster. We will
discuss the MGHPCC and how it came to be, what it provides and
what resources are available to members of the NSE and PSFC
programs. The presentation will be followed by a live demo of
the basic tasks involved in using the new subsystem. Topics
covered including how to log in, how to bring up remote
applications, using git, compilings, runnning jobs in the batch
system.

  These slides, related source code and recipes may be found at
  github repository https://github.com/jcwright77/engaging_cluster_howto.git 


# The engaging system and the nse and psfc nodes
<img style="float: left; width:300px;" src="MGHPCC_DSC00498.JPG" width="300" align="right"/>
-   100+32+4 nodes, 4352 cores
    Centos 7 , 2x16 cores Intel Xeon 2.1 GHz, 128 GB RAM
-   Nodes available in the `sched_any_quicktest` partition
    Centos 6 ,2x8 cores Intel Xeon 2.0 GHz, 64 GB RAM
-   The remaining system is Centos 6 as well.


# Getting an account

Account access is via ssh keys. You may provide your own or use
an ssh key pair generated for you.

Visit <https://eofe1.mit.edu/request_account>

select mit\_nse, mit\_emilio\_b, or mit\_psfc group. Follow instructions to get or specify
your ssh public key.

# Logging in.

-   ssh (all): ssh is included in linux as osx distributions. 
    
    It is available in the bash shell under Windows 10 after
    enabling WSL, but in beta and seems to only support DSS(.dsa)
    keys presently (engaging issues rsa keys) . 
    
    Can do X11 forwarding for remote GUI usage. X11 usage requires
    XQuartz under OSX or [X-Win32](http://kb.mit.edu/confluence/pages/viewpage.action?pageId%3D148603332) under Windows.

-   **[SecureCRT](http://kb.mit.edu/confluence/display/istcontrib/SecureCRT%2B%2Band%2BSecureFX%2B%2Bfor%2BWindows%2B-%2BInstallation%2BInstructions) (windows):** Windows ssh terminal with port forwarding
-   **putty (windows):** Windows ssh terminal with port forwarding
-   **SecureShell:** 
-   **[x2go](http://wiki.x2go.org/doku.php) (all):** remote desktop access
-   **[XWIN32](http://www.starnet.com/xwin32/):** X11 emulator also supports ssh connections
    Uses same `ppk` format for keys as putty.
    See IS&T for the[ license key](https://downloads.mit.edu/released/xwin32/xwin32-2014/xwin32-2014readme2016.txt). Make sure that you get
    release 53 required for working ssh key
    support. IS&T is currently providing release 47. You
    can get the latest release from the [vendor](http://www.starnet.com/xwin32/).

# Working with the file system

-   nfs storage. Long term inexpensive storage, expandable.
    -   Group specific, eg:
        
        `/net/eofdata-data005/psfclab001/<username>` 50 TB
        
        This volume is automounted so you have to `cd` into it
        explicitly to see it.
    -   other storage can be used in serial runs :  1TB quota
        
        `/pool001/`

-   home directory. Backed up with TSM at MIT.
    
    `/home/<username>` 100 GB quota

-   parallel filesystem. Run you parallel codes here. note the
    name. 
    
    lustre : /nobackup/<username>
-   file transfers
    -   scp ('nix)
    
    -   sftp ('nix)
    
    -   sshfs ('nix)
    
    -   winSCP (windows)
    
    -   cifs (windows): possible but not up as a service. brings in
        username and domain mapping issues.
    
    -   x2go (all platforms) ::buggy, requires `fuse` membership
    
    -   bash under windows (WSL available with windows 10)
        enables scp, sshfs. beta version only appears to work with
        dsa keys.

# Finding software with Environment Modules

Provide a clean way of managing multiple compiler/MPI
combinations, software version and dependencies. They modify
search paths and other environmental variables in a user's
shell so the executables and libraries associated with a
module are found.
-   **`module avail`:** list all modules currently available on the system
-   **`module show`:** show what environment a module loads
-   **`module add/unload`:** add/remove a module from a user's environment
-   **`module list`:** list what modules are loaded
-   **`module purge`:** remove all loaded modules

Module names follow a convention of `software/version`

A research group can create their own set of modules and
expose them to uses with `module add`. On engaging, this is
done in `/home/software/<group>/`. This can also be done in a
user's own home directory.

# Running jobs

-   SLURM not PBS
    
    The Simple LinUx Resource Manager (SLURM) is
    used on engaging. If you used the Nersc systems you will be
    familiar with it.
-   Partitions 
    
    Partitions are what job queues are called in
    SLURM. Jobs submitted to run
-   Example Script
-   Getting an interactive job, one node
    
        srun -p sb.q -I -N 1 -c 1 --pty -t 0-00:05 /bin/bash
        salloc -N 1 -n 16 -p sched_any_quicktest --time=0:15:00 --exclusive
    
    where `sb.q` is the partition you want to use. Note quicktest
    has a 15min limit.
-   Other trivia
    -   can ssh to a node if you have a job using it.
    
    -   web pages in ~/public\_html appear as <http://engaging-web.mit.edu/~jcwright>

# Demos

-   Log in using x2go
    Uses ssh key and passphrase
-   Retrieve demo script from github, edit and run it.
    Uses git, vim or emacs or gedit, slurm batch system, c and
    fortran compilers
-   python demo
-   matlab
-   parallel task farm
    slurm job arrays, multiplexing across nodes (queues)
-   parallel executable, c and fortran pi
-   slurm, restarting jobs for long jobs
-   slurm reservations
-   Dropbox
-   julia

# More info

-   sloan engaging documentation at [<https://wikis.mit.edu/confluence/display/sloanrc/Engaging+Platform>](https://wikis.mit.edu/confluence/display/sloanrc/Engaging%2BPlatform)
    requires MIT certificates. Info on engaging usage in general
    as well as custom Sloan tools and modules.
-   PSFC engaging info and local account request <http://www-internal.psfc.mit.edu/computers/cluster/cluster.html>
