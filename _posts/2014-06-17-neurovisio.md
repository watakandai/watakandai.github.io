---
title: "Installing NEURON with python and Neuronvisio"
categories:
  - computing
tags:
  - NEURON
  - python
use_math: false
published: true
---
Since it took some time, I’m going to describe the steps I took to install NEURON with support for python and the 3D visualization tool neuronvisio. I’m running Ubuntu 14.04, python 2.7.

First, I installed the neuron python package found at http://neuralensemble.org/people/eilifmuller/software.html. Note that this installs NEURON 7.1, which is not the latest version.
I had to install also libreadline-dev to get nrnivmodl (the hoc compiler) working:

    sudo apt-get install libreadline-dev

Then install the prerequisites for neuronvisio:

    sudo apt-get install python-qt4 python-matplotlib python-setuptools python-tables mayavi2 python-pip

Install neuronvisio

    cd ~/python/
    git clone git://github.com/mattions/neuronvisio.git
    python setup.py install

Add ~/python to your $PYTHONPATH, add ~/python/neuronvisio/bin/neuronvisio (or links thereto) to path
The final change (the most strange, and the that’s making me write this down) is as follows. Following steps 1-5 should get NEURON and neuronvisio working in python. I could run simulations, plot the 3D structures, etc. The one thing I was unable to do was to select segments in the 3D visualization window. I would receive the error:

    Pick() takes exactly 4 arguments (2 given)

The workaround I found was to edit the following file in mayavi:

    sudo vim /usr/lib/python2.7/dist-packages/mayavi/core/mouse_pick_dispatcher.py

So that the line changes from and to:
    - 168: picker.pick((x, y, 0), self.scene.scene.renderer)
    + 168: picker.pick(x, y, 0, self.scene.scene.renderer)

It’s annoying that such a strange workaround was necessary. However, a similar fix was suggested here (https://github.com/enthought/mayavi/issues/21), so perhaps I wasn’t alone.
