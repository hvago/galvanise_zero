brief and probably inadequate build instructions
================================================

ggp-zero uses tensorflow/keras/numpy, which are not supported/does not work with pypy.

ggplib install instructions uses pypy, which is nice for the speed boost when it comes to building
some of the large propnets.  Once the propnets are built, they are cached in locally.  These can be
accessed via python2 without the speed hit.

The best idea is to run perftest.sh in ggplib before going any further (as will cache breakthrough/reversi)


I would highly recommend using pip and controlling your own pip installs for tensorflow, especially
if using combination of CPU/GPU.  Tested with tensorflow 1.4.1.  1.5 is coming out soon, so will
likely break a few things.

For CUDA installation (if using GPU), you are on your own. :) tensorflow 1.4.1 is CUDA 8, whereas
tensorflow 1.5 is CUDA 9.  You'll also need cuddnn, which requires signing up to nVidia, and if you
are using AWS is an exterme pain to get installed.  I haven't got it to work as yet.

Note: if using CPU for training, then you really want to compile tensorflow from sources.  This is
complicated and takes ages, but it is worth it as runs twice as fast (AVX instructions).  These
instructions are for the binaries they provide.


    # INSTALL:
    sudo apt install python python-dev

    # where you want to install tensorflow etc
    mkdir ~/bin/install

    # install tensorflow as per https://www.tensorflow.org/install/install_linux
    virtualenv --system-site-packages ~/bin/install/py2_tf_cpu
    virtualenv --system-site-packages ~/bin/install/py2_tf_gpu

    # instructions pretty much same for both
    . ~/bin/install/py2_tf_cpu/bin/activate

    pip install twisted cffi pytest tabulate greenlet
    pip install numpy future

    # choose one of:
    pip install --upgrade tensorflow
    # pip install --upgrade tensorflow-gpu

    pip install keras h5py


    # COMMON SETUP
    # put these in shell script/bash alias or something.  I use two bash alias, and switch between CPU/GPU.

    alias ggpzerocpu='export GGPLIB_PATH=~/ggplib && . ~/ggp-zero/bin/setup.sh && . ~/bin/install/py2_tf_cpu/bin/activate'
    alias ggpzerogpu='export GGPLIB_PATH=~/ggplib && . ~/ggp-zero/bin/setup.sh && . ~/bin/install/py2_tf_gpu/bin/activate'


    # TRAIN:

    # for training server:
    cd ggp-zero/src/ggpzero/distributed
    python server.py somefile.conf

    # ctrc-c and edit/configure somefile.conf
    python server.py somefile.conf

    # for training worker:
    cd ggp-zero/src/ggpzero/distributed
    python worker.py worker.conf

    # ctrc-c and edit/configure worker.conf
    python worker.py worker.conf


    # TO RUN MODEL

    cd ggp-zero/src/ggpzero/player
    python puctplayer.py <port> <gen> <optional_config>

    # eg:
    python puctplayer.py 9147 v6_40


    # DATA DIRECTORY:
    # i am running my data directory on a seperate drive.  So I use a symbolic link.  But you need
    # to ensure something like exists for each game

    mkdir -p ggp-zero/data/breakthrough/models
    mkdir -p ggp-zero/data/breakthrough/weights

    # helpers:
    # inside ggp-zero/tmp there are some example confs.  reversi.conf and breakthrough.conf can be
    # used as templates for server.py
    # work0.conf can be used as a template for worker.py
    # copy breakthrough_v5_84.json to the models directory above
    # copy breakthrough_v5_84.h5 to the weights directory above
    # then can run as per above.

    python puctplayer.py 9147 v5_84 test