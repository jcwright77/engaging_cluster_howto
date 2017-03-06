# Quick start to the engaging cluster for the impatient

A brief introduction to the engaging cluster for the NSE and PSFC groups. 

  These slides, related source code and recipes may be found at
  github repository https://github.com/jcwright77/engaging_cluster_howto.git 

The author may be contacted at `jcwright@mit.edu`.

# What we have
-   100 (psfc) + 32 (nse) + 4 (baglietto) nodes, 4352 cores
    Centos 7 , 2x16 cores Intel Xeon 2.1 GHz, 128 GB RAM
    
-   nfs storage. Long term inexpensive storage, expandable. TSM backup.
    -   Group specific, eg:
        
        `/net/eofe-data005/psfclab001/<username>` 50 TB
        
        This volume is automounted so you have to `cd` into it
        explicitly to see it.
    -   other storage that can be used in serial runs :  1TB quota
        
        `/pool001/`

-   home directory. Backed up with TSM at MIT.
    
    `/home/username` 100 GB quota

-   parallel filesystem.  Run your parallel codes here. Note the
    name. 
    
    lustre : /nobackup1/username . 1 PetaByte of storage for engaging
    
# How to log in

- Apply for an account under "MGHPCC info" at http://computers.psfc.mit.edu (form requires PSFC credentials). Our nodes are in the sched_mit_psfc partition.
- An email from `engaging-admin@techsquare.com` will confirm your account when ready. Upon form submission your browser will download your private ssh key. (We will eventually automate this step for you in your cmodws acccounts.)
- Use `ssh` (linux, macs), Putty/XWin-32/Secure-CRT (windows) to connect.

# How to run a job
- Engaging uses the SLURM resource manager (as does NERSC).
-   Partitions: **`sched_mit_psfc`**,`sched_mit_nse`, `sched_mit_emiliob`

-   Common slurm commands
    - sbatch :: submit a batch job
    - squeue -u username :: show a users job status
    - scancel  :: kill a job
    - scontrol show partition :: list partitions to which you have access
    - scontrol show jobid `#` :: info on job
    - sinfo -a :: show all partition names, runtimes and available nodes
    - salloc :: request a set of nodes in a partition
      `salloc --gres=gpu:1 -N 1 -n 16 -p sched_system_all --time=1:00:00 --exclusive`
      You must exit from an `salloc` session. `srun` and `mpirun` within an allocation will use the allocated cores automatically.
      
    - srun :: run a program on allocated processors, optionally, also requests the allocation if needed.
    
    - sacct :: detailed information on usage

# SLURM Recipes
  -   Start a job
  
        sbatch job.slurm
  
	job.slurm:
```
#!/bin/bash
# Number of nodes
#SBATCH -N 32
# Number of processor core (32*32=1024, psfc, mit and emiliob nodes have 32 cores per node)
#SBATCH -n 1024
# specify how long your job needs. Be HONEST, it affects how long the job may wait for its turn.
#SBATCH --time=0:04:00
# which partition or queue the jobs runs in
#SBATCH -p sched_mit_psfc
#customize the name of the stderr/stdout file. %j is the job number
#SBATCH -o cpi_nse-%j.out

#load default system modules
. /etc/profile.d/modules.sh

#load modules your job depends on. 
module purge #full control over environment
module load intel
module load impi

#I like to echo the running environment
env

#Finally, the command to execute. 
#The job starts in the directory it was submitted from.

mpirun ./fpi
```
  -   Getting an interactive job
    
        `srun -p sched_mit_psfc -I -N 1 -c 1 --pty -t 0-00:05 /bin/bash`
         `-N "num nodes" -c "cpus-per-task"`
	
      	`srun -p  sched_mit_psfc  -I --tasks-per-node=4 -N 4  --pty -t 0-2:05 bash`
        	gives an interactive job with 4 nodes x 4 cpus per node = 16 cores.
	
  -   Request 16 cores on a node
        `salloc -N 1 -n 16 -p sched_any_quicktest --time=0:15:00 --exclusive`
        
  -   Request a specific node, 32 cores, and forward X11 for remote display
        #x11 forwarding to a specific node, may take a moment to first load
        `srun -w node552 -N 1 -n 32 -p sched_mit_nse --time=1:00:00 --x11=first --pty /bin/bash`
        
  - How much memory is or did my job use
         `sacct -o MaxRSS -j JOBID`

# How to load software packages
Finding software with Environment Modules

Provide a clean way of managing multiple compiler/MPI
combinations, software versions and dependencies. Modules modify
search paths and other environmental variables in a user's
shell so the executables and libraries associated with a
module are found.

We are installing libraries and widely used scientific codes in their own modules.

+ Common commands
  -   **`module avail`:** list all modules currently available on the system
  -   **`module show`:** show what environment a module loads
  -   **`module add/unload`:** add/remove a module from a user's environment
  -   **`module list`:** list what modules are loaded
  -   **`module purge`:** remove all loaded modules
  -   **`module use =path=`:** add a new search path to modules

+ PSFC specific modules
  - `module use /home/software/psfc/modulefiles`
Module names follow a convention of `software/version`
```
[jcwright@eofe7 ~]$ module use /home/software/psfc/modulefiles #enable psfc specific modules
[jcwright@eofe7 ~]$ module add psfc/config 

---------------------------------- /home/software/psfc/modulefiles/ ------------------------------------
----------------------- /home/software/psfc/modulefiles ------------------------
psfc/atlas/gcc-4.8.4/3.10.3 psfc/mkl/17
psfc/config                 psfc/pgplot/5.2.2
psfc/fftw/2.1.5             psfc/pymfem_donotuse
psfc/fftw/3.3.5             psfc/python/2.7-modules
psfc/fftw/intel17/2.1.5     psfc/python/3.5-modules
psfc/hypre/2.11.1           psfc/totalview/2016.07.22
psfc/metis/intel-17/5.1.0
---------------------------------- /home/software/modulefiles ------------------------------------------
...
```
+ Setup for compiling with intel
```
module load intel
module load impi
module load psfc/mkl
```
# Getting help

- For more details and solutions, see the README.md file on github.

    https://github.com/jcwright77/engaging_cluster_howto/blob/master/README.md
    
- PSFC help page and account request form
    http://computers.psfc.mit.edu

- How do I compile this, do we have this library or program installed, etc:

    email:engaging@psfc.mit.edu
    
- I think my account/this node/the file system is messed up:

    email:engaging-admin@techsquare.com
