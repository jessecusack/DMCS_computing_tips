# Jupyter on Amarel


## Setting up conda and jupyter lab

There are two main ways of using `jupyter lab` on Amarel, 1) via your own installation of `conda` or 2) via the pre-installed `conda`. There is also an 'on demand' web interface for using jupyter notebooks, but this has less features than jupyter lab. 

1. You can install [miniconda](https://docs.conda.io/en/latest/miniconda.html) in your home directory (so long at least 10 GB of storage). This is beneficial because you can then heavily customise and upgrade your environment to suit your needs. 

2.  1. The preinstalled conda distribution is `anaconda`. You can load it using `module load anaconda`. 
    2. The DMCS specific version of conda can be loaded as `module load xroms`, but you need access to the DMCS partition. 


More detailed instructions for 1. are below, note that using [ssh keys](easy_amarel.md) makes everything a little faster and easier! 

### Installing [Miniconda](https://docs.conda.io/en/latest/miniconda.html) in your home directory

Log into the Amarel. To download Miniconda, run:

```bash
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

To install Miniconda, run:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

And follow the prompts to install it in your home directory (this usually just means following the default options).

As part of the installation, you may be asked for permission to modify your terminal configuration (the `.bashrc` file that lives in your home directory). This is recommended, so that every time you log into Amarel, you will have access to the `conda` commands automatically.

Upon successful installation, I would recommend setting `conda-forge` as the default channel package searches. I find that it has a more complete list of packages than the standard Anaconda channel. To do so, run the following in the terminal:

```bash
conda config --add channels conda-forge
conda config --set channel_priority strict
```

### Install jupyter lab

From the login terminal of Amarel run:

```bash
conda activate base
conda install -c conda-forge jupyterlab
```

Follow any prompts. 

It is good practise to set a server password. You'll then use it to access the remote session. It can be relatively simple because most of your security comes from the use of SSH. 

```bash
jupyter server password
```

Don't forget to store it somewhere! (like a password manager)

## Using jupyter lab remotely

This is how I would start a jupyter lab session on Amarel.

### The easy way with `jupyter-forward`

The package [jupyter forward](https://github.com/NCAR/jupyter-forward) makes starting jupyter lab on a remote machine an easy 1-command process. For this to work, you need to have 1) installed Miniconda on Amarel 2) installed jupyter lab on Amarel and 3) installed `jupyter-forward` on your own computer. 

1. Follow the instructions above to install Miniconda on Amarel. 

2. Follow the instructions above to install jupyter lab and set a password.

3. From a terminal on your own computer run:

```bash
conda activate base
conda install -c conda-forge jupyter-forward
```

To use `jupyter-forward` to launch a remote jupyter lab session, run a command like this on your own computer (replacing username appropriately):

```bash
jupyter-forward --port=8898 --launch-command="srun --partition=main --mem=8G --time=00:30:00" [username]@amarel.rutgers.edu
```

If you add the option `--conda-env=base` then the base environment will be activated prior to starting jupyter lab. This can be useful if you have installed your own version of jupyter lab in a separate enviorment and want to use that. 

You may be prompted for your Amarel password (which ideally would not happen with ssh keys, but it might be a small bug with `jupyter-forward`)

The command does the following behind the scenes:
* log into Amarel
* start a SLURM job using the command you specify
* launch jupyter lab as part of the job
* forward the jupyter lab port to your local computer

Eventually, a browswer tab on your computer will open with your remote jupyter lab session (URL: `localhost:8898`). You may be prompted for your jupyter server password. All the above can be done manually too, as explained below. 

### The manual way

First, of course, log in to Amarel (not forgetting to start the university VPN first): 

    ssh amarel
    
For the above to work, I set up an ssh key-pair with amarel and edited my local `~/.ssh/config` file appropriately. 

Within Amarel, start a `tmux` session, which will stay running even if you are disconnected.

```bash
tmux new -s jlab
```
    
If `tmux` is not available, you might need to load it with `module load tmux` or use `screen` instead. Next, start an interactive session using the SLURM `srun` command.
    
```bash
srun --partition=main -n 1 --mem=8G --time=00:30:00 --pty bash
```

Above I requested a single compute node with 8 GB of memory for 8 hours. This can obviously be adjusted to suit the need. After a moment we should be assigned a node. We need to know the `hostname` of the node we're on e.g.

```bash
echo $(hostname)
```
    
Copy the result of the above command somewhere, we'll need it later. Next, start up jupyter lab, navigating to a particular project folder if necessary e.g.

```bash
cd ~/some_project
jupyter lab --no-browser --port=8897 --ip="$(hostname)"
```
    
After which I hit `ctrl + b` followed immediately by `d` to disconnect from the tmux session.
    
On my personal computer I run:

```bash
ssh -L 8897:[hostname]:8897 -N amarel
```

where `[hostname]` is replaced by the output of `echo $(hostname)` from the Amarel session. The command establishes a connection between port 8897 on the Amarel compute node to the same port on your computer (they don't have to be the same, but it is easier to remember). 

Jupyter lab can then be accessed in a browser on you personal computer from `localhost:8897` (pop it straight into the URL bar). I highly recommend making use of jupyter lab's [workspaces](https://jupyterlab.readthedocs.io/en/stable/user/urls.html) feature to avoid a cluttered working environment if you have several projects.

## Using the XROMS kernel

Deactivate (temporarily) your home installation of conda.

```bash
conda deactivate
```

Load the xroms installation.

```bash
module load xroms
```

Install the xroms kernel.   

```bash
python -m ipykernel install --user --name xroms
```

Unload xroms.

```bash
module unload xroms
```

Now you can set your notebooks to make use of the `xroms` kernel or you can use it via the terminal with `conda activate xroms`, without needing to load the module. 