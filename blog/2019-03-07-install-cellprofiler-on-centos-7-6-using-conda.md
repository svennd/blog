---
title: Install CellProfiler on Centos 7.6 using conda.
author: svennd

date: 2019-03-07T14:08:48+00:00
url: /install-cellprofiler-on-centos-7-6-using-conda/
thumbnail: /img/2020/05/jonny-caspari-kplYftwYOCY-unsplash-scaled.jpg
categories:
  - Linux
tags:
  - cellprofiler
  - centos
  - conda

---
[CellProfiler][1] is not an easy to install tool; or perhaps I was clumsy on the first attempt (building from source) but I could not get it to work properly on a Linux machine; After another attempt using miniconda, I managed to get it running. This is just a documentation of how I got it working. In case I have to do it again. By no means am I an expert on the case.

### Dependencies

I don't know if these are all the dependencies but at some point I had to install them.

<pre>yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel bzip2 mariadb-devel libstdc++-devel gcc-c++ gtk2 ImageMagick ImageMagick-devel</pre>

Another issue is that the installation requires is libjbig.so.0 which can not be found, however [jbigkit-libs][2] provides libjbig.so.2.0; which can be soft-linked and it will then work;

<pre>cd /usr/lib64/
ln -s libjbig.so.2.0 libjbig.so.0
</pre>

### Install Miniconda

Get the latest version for Linux 64bit (or [other][3]) and make it executable; On Centos python 2.7 is default, and I believe it is required for CellProfiler.

<pre>wget https://repo.anaconda.com/miniconda/Miniconda2-latest-Linux-x86_64.sh
chmod 755 Miniconda2-latest-Linux-x86_64.sh</pre>

### Install CellProfiler

Finally we are ready to try the installation; Create a directory "cellprofiler" (not specific)

<pre>mkdir cellprofiler
cd cellprofiler
touch environment.yml</pre>

_environment.yml_ should contain (using nano, vi, vim, emacs,...)

<pre># run: conda env create -f environment.yml
# run: conda env update -f environment.yml
# run: conda env remove -n cellprofiler
name: cellprofiler
# in order of priority: highest (top) to lowest (bottom)
channels:
  - anaconda
  - goodman # mysql-python for mac
  - bioconda
  - cyclus # java-jdk for windows
  - conda-forge # libxml2 for windows
  - BjornFJohansson # wxpython for linux
dependencies:
  - appdirs
  - boto3
  - cython
  - h5py
  - ipywidgets
  - java-jdk
  - joblib
  - jupyter
  - libtiff
  - libxml2
  - libxslt
  - lxml
  - packaging
  - pillow
  - pip
  - python=2
  - pyzmq=15.3.0
  - mahotas
  - matplotlib!=2.1.0,&gt;2.0.0
  - mysqlclient
  - numpy
  - raven
  - requests
  - scikit-image&gt;=0.13
  - scikit-learn
  - scipy
  - sphinx
  - tifffile
  - wxpython=3.0.2.0
  - pip:
    - cellh5
    - centrosome
    - inflect
    - prokaryote==2.4.0
    - javabridge==1.0.15
    - python-bioformats==1.4.0
    - git+https://github.com/CellProfiler/CellProfiler.git@master</pre>

[source][4]

After this, make the environment; using :  (will take a while)

<pre>conda env create -f environment.yml</pre>

While debugging you can also use : (to update)

<pre>conda env update -f environment.yml</pre>

This kinda works but generates two warnings that don't seem to impact the tools (but perhaps I haven't use specific functions that depend on these)

<pre>cellprofiler 3.1.8 has requirement prokaryote==2.4.1, but you'll have prokaryote 2.4.0 which is incompatible.
cellprofiler 3.1.8 has requirement python-bioformats==1.5.2, but you'll have python-bioformats 1.4.0 which is incompatible.</pre>

Once that is finished, we can activate & run.

<pre>conda activate cellprofiler
cellprofiler</pre>

Since this is on a server, I also needed to allow [X11 forwarding][5] over ssh;

[![](/img/2019/03/cellprofiler-300x175.png)][6]

 [1]: https://cellprofiler.org/
 [2]: https://centos.pkgs.org/7/centos-x86_64/jbigkit-libs-2.0-11.el7.x86_64.rpm.html
 [3]: https://conda.io/en/latest/miniconda.html
 [4]: https://github.com/CellProfiler/CellProfiler/wiki/Conda-Installation
 [5]: https://wiki.centos.org/HowTos/Xming
 [6]: /img/2019/03/cellprofiler.png