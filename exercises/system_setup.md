![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Setting up your system

This exercise walks you through the steps for setting up your system such that you can run and process turbulent flow simulations. In particular, there are three tools we will rely on:

- **OpenFOAM** is an open-source simulation tool employing the finite volume method for solving transport equations
- **ParaView** is an open-source visualization tool for scientific data
- **Python** is a scripting language and ecosystem that comes with a variety of useful (scientific) libraries

The following paragraphs provide information on how to set up these dependencies on both Windows and Linux. However, note that performing simulations on Windows comes with some performance penalty.

## Requirements

Ideally, your system should have the following properties:

- 4 or more physical CPU cores
- 16 GB or more memory (RAM)
- a recent operating system like Windows 10/11 or Ubuntu 20.04/22.04 (and similar)

Don't worry if your hardware does not fullfil the above requirements. The archived simulation data will be provided, too.

## Installing OpenFOAM

The OpenFOAM version used in the course is OpenFOAM-**v2212** from *www.openfoam.com*. Older versions or other branches may not support all the required functionality. [Here](https://www.openfoam.com/news/main-news/openfoam-v2212) you find an overview of installation options.

For Windows users, it is recommended to use *Windows Subsystem for Linux* (WSL) with Ubuntu. You may find the following two tutorials helpful:

- [How to install OpenFOAM in Windows 11 and Ubuntu](https://www.youtube.com/watch?v=CeEJS1eT9NE)
- [How to install OpenFOAM in Windows 10 and Ubuntu](https://www.youtube.com/watch?v=w0bBOWlVlvA)

## Installing ParaView

In case you already have a version of ParaView later than 5.0, you are good to go. Otherwise, go to the [ParaView download page](https://www.paraview.org/download/), select the latest available version, choose your operating system, and download the installer. On Windows, ParaView should **not** be installed inside WSL. At the time of writing, suitable download links for ParaView 5.11 are:

- Windows: [ParaView-5.11.1-Windows-Python3.9-msvc2017-AMD64.msi](https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.11&type=binary&os=Windows&downloadFile=ParaView-5.11.1-Windows-Python3.9-msvc2017-AMD64.msi)
- Linux: [ParaView-5.11.0-MPI-Linux-Python3.9-x86_64.tar.gz](https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.11&type=binary&os=Linux&downloadFile=ParaView-5.11.0-MPI-Linux-Python3.9-x86_64.tar.gz)

In case you are not familiar with the set up of pre-compiled binaries on Linux, [this video tutorial](https://www.youtube.com/watch?v=tWEGjWD8d2M) might be helpful.

## Python

On Linux-based operating systems, Python is typically pre-installed. Any version of Python 3 will be fine for this course.

For Windows, you can download the latest installer form the [official website](https://www.python.org/downloads/windows/). At the time of writing, the latest version is [Windows installer (64-bit)](https://www.python.org/ftp/python/3.10.11/python-3.10.11-amd64.exe).