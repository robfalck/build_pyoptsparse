# build_pyoptsparse

This script package is intended to help get pyoptsparse working more easily with the optional SNOPT dependency.

Originally, this script was written to overcome the complexities of building pyOptSparse and IPOPT, but improvements to pyoptsparse and the reliance on cyipopt eliminated this need.

As a result, this package no longer supports full compilation of pyoptsparse. Instead, users should install pyoptsparse and cyipopt from conda-forge, which will provide a working implementation of IPOPT.

Users who still need SNOPT integration should take the following steps:

1. Activate your virtual environment.
2. Install pyoptsparse and cyipopt from conda-forge. 
3. Install this package. It currently must be installed directly from github:

```bash
python -m pip install git+https://github.com/OpenMDAO/build_pyoptsparse.git
```

4. Build and install the SNOPT module for pyoptsparse using your licensed SNOPT source files or dynamic library.

```bash
python -m build_pyoptsparse.snopt_module /path/to/snopt/fortran/src
```

or 

```bash
python -m build_pyoptsparse.snopt_module --snopt-lib /path/to/libsnopt7.so
```

For now, the existing `python -m build_pyoptsparse` command remains but issues a noisy deprecation warning by default, with an option to bypass it using `python -m build_pyoptsparse --ignore-dep`.

## Troubleshooting

### Multiple definition errors when building SNOPT from source
When building the SNOPT module from source via

```bash
python -m build_pyoptsparse.snopt_module /path/to/snopt/fortran/src
```

you may encounter many `multiple definition of <subroutine>` errors that look like

```bash
snopt.cpython-314-x86_64-linux-gnu.so.p/_tmp_snopt-build-a8lmuwuo_source_sn27lu90.f.o: In function `lu1fac_':
/tmp/snopt-build-a8lmuwuo/source/sn27lu90.f:59: multiple definition of `lu1fac_'
snopt.cpython-314-x86_64-linux-gnu.so.p/_tmp_snopt-build-a8lmuwuo_source_sn27lu.f.o:/tmp/snopt-build-a8lmuwuo/source/sn27lu.f:89: first defined here
snopt.cpython-314-x86_64-linux-gnu.so.p/_tmp_snopt-build-a8lmuwuo_source_sn27lu90.f.o: In function `lu1mcp_':
/tmp/snopt-build-a8lmuwuo/source/sn27lu90.f:2628: multiple definition of `lu1mcp_'
snopt.cpython-314-x86_64-linux-gnu.so.p/_tmp_snopt-build-a8lmuwuo_source_sn27lu.f.o:/tmp/snopt-build-a8lmuwuo/source/sn27lu.f:2750: first defined here
```

You can fix this by removing the files `sn27lu77.f`, `sn27lu90.f`, and `snopth.f` from the SNOPT source directory:

```bash
> rm SNOPT\ src\ 7.7.1/sn27lu77.f SNOPT\ src\ 7.7.1/sn27lu90.f SNOPT\ src\ 7.7.1/snopth.f 
```

and then rerunning 

```bash
python -m build_pyoptsparse.snopt_module /path/to/snopt/fortran/src
```
