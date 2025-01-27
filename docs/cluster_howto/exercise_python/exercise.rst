Exercise: running python in the cluster 
***************************************

In this exercise, you will learn how to run Python script in the cluster, using `Anaconda <https://anaconda.org>`_ and the `conda environment <https://conda.io/docs/user-guide/tasks/manage-environments.html>`_.

Preparation
===========

Follow the steps below to download :download:`the prepared Python scripts <python_exercise.tgz>`.

.. code-block:: bash

    $ wget https://github.com/Donders-Institute/hpc-wiki-v2/raw/master/docs/cluster_howto/exercise_python/python_exercise.tgz
    $ tar xvzf python_exercise.tgz
    $ ls
    example4d.nii.gz  nibabel_example.py

Let's run the python script, and you should expect some errors as this script requires a python module called `nibabel <http://nipy.org/packages/nibabel/index.html>`_.
    
.. code-block:: bash
    
    $ python nibabel_example.py
    Traceback (most recent call last):
      File "nibabel_example.py", line 3, in <module>
        import nibabel as nib
    ImportError: No module named nibabel

Task 1: Conda environment 
=========================

Load the anaconda module using the command below:

.. code-block:: bash

    $ module load anaconda2/4.3.0

, and check which python executable is used, e.g.

.. code-block:: bash

    $ which python
    /opt/anaconda2/4.3.0/bin/python

While Anaconda provides a bundle of ready-to-use python packages for data analysis, the conda environment is useful in two perspectives:

#. It creates isolations between python projects so that requirements and package dependencies in one environment do not spoil other environments.

#. It allows uses to install packages without administrative permission.

After the anaconda module is loaded, use the command below to create a conda environment called ``demo``, and have the ``pip``, ``jupyter`` and ``numpy`` packages installed rightaway.

.. code-block:: bash

    $ conda create --name demo pip jupyter numpy

At the end of the creation, example commands for activating and deactivating the environment will be given on the terminal.  To activate the environment we just created, do:

.. code-block:: bash

    $ source activate demo

After that you will see changes on the shell prompt.  For example, the name ``demo`` is shown on the terminal prompt.

Now check which ``python`` or ``pip`` program you will be using:

.. code-block:: bash

    $ which python
    ~/.conda/envs/demo/bin/python
    
    $ which pip
    ~/.conda/envs/demo/bin/pip
    
You see that the location of the ``python`` and ``pip`` program is now under your home directory under a conda environment directory we have created.

The setting in the shell for the conda environment will be transferred with the job you submitted to the cluster.  You could check that by starting an interactive job, and checking the locations of the ``python`` and ``pip`` programs.  They should still be pointed to your home directory under the conda environment.

.. code-block:: bash

    $ qsub -I -l 'walltime=00:20:00,mem=1gb'
    
    $ which python
    ~/.conda/envs/demo/bin/python
    
    $ which pip
    ~/.conda/envs/demo/bin/pip
    
.. tip::
    You may also firstly submit a job then enter the conda environment after the job start.  This may be handy when the conda environment is only needed within the scope of the job, or you want to switch between conda environment for different jobs.

To deactive the environment, do:

.. code-block:: bash

    $ source deactivate demo

.. tip::
    To deactivate the conda environment, you may also close the terminal in which the conda environment is loaded.

Task 2: Python packages 
=======================

Let's activate the conda environment we just created in Task 1.

.. code-block:: bash

    $ source activate demo

When you are in a conda environment, you may install your own packages in your environment if the ``pip`` package is available in the environment.  Using the following command to check wether the ``pip`` is available in the environment:

.. code-block:: bash

    $ which pip
    ~/.conda/envs/demo/bin/pip

The output of the command above should be a path started with ``~/.conda``.

Try to install a package called `nibabel <http://nipy.org/packages/nibabel/index.html>`_ in your conda environment, using the command below:

.. code-block:: bash

    $ pip install nibabel

.. Note::
    The conda environment is created and installed in your home directory under the path ``$HOME/.conda/envs``.  Environments are organised in different subfolders.  When you install new packages in an environment, relevant files will also be created in its own subfolder.  Be aware of the fact that conda environments do take space from the quota of your home directory.

Once the installation is done, let's run the python script in the downloaded tarball again, and it should work.

.. code-block:: bash

    $ python nibabel_example.py
    (128, 96, 24, 2)
    
Task 3: Jupyter notebook
========================

Make sure you are in the conda environment we created in task 1; otherwise, do the following commands:

.. code-block:: bash

    $ source activate demo

`Jupyter notebook <http://jupyter.org>`_ is a web application for creating and sharing documents containing live (Python) codes.

In order to run the live python codes within a conda environment (so that you can access to all python libraries installed in your conda environment), the package ``jupyter`` should also be installed in the conda environment.  Use the following methods to check it.

.. code-block:: bash

    $ conda list | grep jupyter
    jupyter                   1.0.0                    py27_3  
    jupyter_client            5.1.0                    py27_0  
    jupyter_console           5.2.0                    py27_0  
    jupyter_core              4.3.0                    py27_0  


If you don't see jupyter related packages in your conda environment, run the following command to install it

.. code-block:: bash

    $ conda install jupyter

Within the conda environment, simply run the command ``jupyter-notebook`` to start the Jupyter notebook.

Try to run the python script ``nibabel_example.py`` again in the notebook. It should just work.

Task 4: Spyder
==============

`Spyder <https://www.spyder-ide.org/>`_ is one of the integrated development environment (IDE) for Python projects.  It is part of the Anaconda package.

If you just want to make use of the Spyder IDE without the need of loading specific Python modules from your own conda environment, you could simply run the following command on a cluster access node within a VNC session:

.. code-block:: bash

    $ spyder

You will encounter a graphical dialog through which you can select the Spyder from a specific Anaconda version.  The wrapper then submits a job to the cluster to launch the specific spyder version on a computer node.

If you want to use specific modules installed in a conda environment, you have to install your own Spyder in the same conda environment.  Using the ``demo`` conda environment as an example, here are steps to follow:

Make sure you are in the conda environment we created in task 1; otherwise, do the following commands:

.. code-block:: bash

    $ source activate demo

Install the Spyder package, using the ``conda install`` command:

.. important::
    **DO NOT** install spyder from ``pip install``.  The spyder installed via ``pip`` doesn't take care of library dependancies and therefore it is very likely to be broken.

.. code-block:: bash

    $ conda install spyder

Submit an interactive job with your required resource, e.g.:

.. code-block:: bash

    $ qsub -I -l walltime=1:00:00,mem=4gb

Under the shell prompt of the interactive job, run the following commands to start Spyder:

.. code-block:: bash

    $ source activate demo
    $ spyder

You could now check within the Spyder IDE whether the ``nibabel`` Python module we installed earlier is still avaiable. For instance, **Open** the file ``nibabel_example.py`` in Spyder, and press the ``F5`` key on the keyboard (or select the **Run** on the menu). This should give the result in the IPython console (at the right-bottom of the Spyder IDE).
