# DMCS Computing Tips

All tips (so far) assume that you're using macOS/Linux.

## Contents

* [Simplify Amarel access with ssh keys](easy_amarel.md)
* [Working with jupyter on Amarel](jupyter_amarel.md) (including the XROMS kernel)
* [ROMS stuff]() (doesn't exist yet...)


### Misc

* [Installing Octant with conda](octant.md) The package [octant](https://github.com/hetland/octant.git) was created by Rob Hetland for use with ROMS. It is obsolete but (perhaps?) still useful. 


### Presentation

Install `pandoc`, (e.g. `conda install -c conda-forge pandoc`) to compile the presentation:

```bash
pandoc -t beamer -s presentation.txt -o presentation.pdf
```