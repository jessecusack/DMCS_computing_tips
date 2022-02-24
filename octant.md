# Installing octant

This package was created by Rob Hetland for use with ROMS. It is obsolete (uses python 2.7) but (perhaps?) still useful. 


First, copy paste the following into a file called `environment.yml`:

    name: octant
    channels:
    - conda-forge
    - defaults
    dependencies:
    - python=2.7.*
    - matplotlib
    - netCDF4
    - numpy
    - pip
    - ipykernel
    - pyproj
    - jupyter

Run the following in a terminal to create the `octant` conda environment.

```bash
conda env create -f environment.yml
```

Clone the octant github repository.

```bash
git clone https://github.com/hetland/octant.git
```

Now edit the fortran code. I had to change line 369 in the file `octant/octant/src/iso.f` from,

```
      real*8 a, b, c, d, dydx, e, f, h, xx, yy
```

to,

```
      real*8 a, b, c, d, dydx(1), e, f, h, xx, yy
```

for the installation to work.

Next, install octant using pip. (You need a fortran compiler like `gfortran` which can be installed with homebrew.) 

```bash
cd octant
conda activate octant
pip install -e .
```

The `-e` flag means it is editable, you can go and change stuff in octant. 

# Notebooks (optional)

If you create a kernel for the `octant` environment, you can then tell a notebook to use this kernel. 

Create the kernel using:

```bash
conda activate octant
python -m ipykernel install --user --name octant
```
