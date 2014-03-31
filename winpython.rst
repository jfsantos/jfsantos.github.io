WinPython: a portable scientific Python distribution for Windows
################################################################

:date: 2013-06-25 21:00
:tags: development
:category: blog
:slug: winpython
:author: jfsantos

Recently I developed a `piece of software`_ for administering speech
intelligibility listening tests and discovered a small issue: I am not
an administrator of the computer that I am going to use for the tests,
so I cannot install any software. Since the computer is running
Windows, there is no standard Python distribution there. I already
knew `Portable Python`_, which is (surprise!) a portable Python
distribution that can be run from a USB storage device. However, the
first version of my code was using PyAudio, which was not included in
this distribution. Since this module uses binary wrappers to
PortAudio, installation is not as straightforward as copying a bunch
of .py files, and I could not find out how to install it to my USB
drive... but then, WinPython came to the rescue!

.. _`piece of software`: http://bitbucket.org/jfsantos/sitest
.. _`Portable Python`: http://portablepython.com

`WinPython <https://code.google.com/p/winpython/>`_ is a complete
NumPy/SciPy portable development environment, which also includes an
IDE (`Spyder <https://code.google.com/p/spyderlib/>`_), my favorite
version control system (Mercurial) and Qt tools for GUI
development. However, what made me like it so much is the WinPython
Package Manager (`WPPM
<https://code.google.com/p/winpython/wiki/WPPM>`_), which allows one
to install any packages that can be installed with *easy_install*,
*pip*, or *distutils* standard installers. The latter is specially
interesting because *distutils* installers include binary
dependencies, so you do not need a C/C++ compiler. If scientific
computing is your thing, maybe the library you need is just a visit to
`Christoph Gohlke's page
<http://www.lfd.uci.edu/~gohlke/pythonlibs/>`_ away (which was my case
with PyAudio).

Another interesting feature is the possibility of converting your
portable distribution to a more "conventional" Python installation on
Windows by adding some keys to the registry (this is also done through
the WinPython Control Panel). Also, both Python 2.7 and Python 3.3 are
available, with support to 32 and 64 bit Windows XP/7/8.

