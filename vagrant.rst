Using Vagrant to make reproducible development and test environments
####################################################################

:date: 2013-05-19 16:00
:tags: development
:category: blog
:slug: using-vagrant
:author: jfsantos

Working with research-grade software is hard sometimes. We all have
heard before (more than once, probably) the "it worked perfectly on my
computer" story. The problem is that setting up a development
environment takes a lot of time, and usually you'll end up forgetting
which libraries and pieces of software you needed to install to get
things running... and if you cannot figure out what did you have to do
to get it working, let alone your colleagues that may need to use your
code someday!

One simple way to try to mitigate this problem is to use virtual
machines (VMs) as development and test environments. By developing
your code on a VM, you can later distribute it instead of distributing
your code only. I know that's not how serious people develop software,
but let's be honest here: many research-grade software is developed on
a "code and forget" paradigm. As soon as you publish your
paper/thesis/whatever, you'll forget about it. Then, you will update
your operating system, hardware, libraries. Then, months or years
later, you will come up with a wonderful idea and think "I already
have software for doing half of that!" just to find out that it does
not work anymore... That's not nice.

Of course, developing and distributing software using VMs has other
advantages. For collaborative projects, you can make sure everybody is
working with the same versions of the libraries and
compilers/interpreters. Also, it is a way of having access to another
operating system such as Linux (if, like me, you are stuck with
Windows for the time being).

One way of following this approach would be to have a "base" virtual
machine and then, every time you start a new software project, you do
this:

1. Make a copy from the base environment.
2. Install everything that you need on that copy, manually.
3. Develop your software, tests, etc.
4. Package the result and distribute (or archive) it.

However, making copies and installing everything by hand is boring, so
I chose to use an amazing tool called Vagrant to do a lot of this
boring work for me. It is able to download base virtual machines (for
example, a barebones Ubuntu VM), configure them, and install software
on it automatically. All you need to do is write a couple of
configuration files.

Setting up Vagrant on Windows
-----------------------------

In order to set up and properly use Vagrant on Windows, you will need
to install it and also VirtualBox, which is the default VM
provider. It is also recommended that you install Git, which comes
with a SSH client for Windows (that you can use to access the VM
instead of using the default VirtualBox interface). You can find the
installers on the following URLs:

* Vagrant http://downloads.vagrantup.com/tags/v1.2.2
* VirtualBox http://www.virtualbox.org/wiki/Downloads
* Git http://www.git-scm.com/downloads

By default, Vagrant will keep the downloaded base VMs at your home
folder (``%USERPROFILE%\.vagrant.d``), but you can choose another
folder by setting the environment variable ``%VAGRANT_HOME%`` on
Windows. Also, VirtualBox will store your VMs at
``%USERPROFILE%\VirtualBox VMs``, but you can change it by opening
VirtualBox preferences and changing the default VM folder. As both
folders will end up storing a lot of data depending on how many VMs
you use, it may be interesting to change both before starting to use
the approach presented here.

Example VM
----------

I wrote a simple configuration file to use as an example for this
post. The machine configuration is all done on this file (which is
called *Vagrantfile*) except for the software installation, which is
done using a simple shell script. As you can see below, the
configuration is very simple. To test it, just download both files,
fire up a Git Bash session, go to this directory and run ``vagrant
up``. This will start up the virtual machine and install all the
required software on it.

.. code-block:: ruby
  # -*- mode: ruby -*-
  # vi: set ft=ruby :

  Vagrant::Config.run do |config|
    # All Vagrant configuration is done here. The most common configuration
    # options are documented and commented below. For a complete reference,
    # please see the online documentation at vagrantup.com.

    # Every Vagrant virtual environment requires a box to build off of.
    config.vm.box = "precise32"

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system.
    config.vm.box_url = "http://files.vagrantup.com/precise32.box"

    # Amount of RAM on the VM
    config.vm.customize ["modifyvm", :id, "--memory", 512] # 512 MB RAM

    # Boot with a GUI so you can see the screen. (Default is headless)
    # config.vm.boot_mode = :gui

    # Assign this VM to a host-only network IP, allowing you to access it
    # via the IP. Host-only networks can talk to the host machine as well as
    # any other machines on the same network, but cannot be accessed (through this
    # network interface) by any external networks.
    # config.vm.network :hostonly, "192.168.33.10"

    # Assign this VM to a bridged network, allowing you to connect directly to a
    # network using the host's network device. This makes the VM appear as another
    # physical device on your network.
    config.vm.network :bridged

    # Forward a port from the guest to the host, which allows for outside
    # computers to access the VM, whereas host only networking does not.
    config.vm.forward_port 8000, 8000

    # Share an additional folder to the guest VM. The first argument is
    # an identifier, the second is the path on the guest to mount the
    # folder, and the third is the path on the host to the actual folder.
    # config.vm.share_folder "v-data", "/vagrant_data", "../data"

    # Enable provisioning with a shell script. Add the sequence of
    # commands you want to run to provision the VM to provision.sh 
    config.vm.provision :shell, :path => "provision.sh"
  end

For this example, we will simply add commands to install GNU Octave on
the VM to the file ``provision.sh``:

.. code-block:: bash

    export DEBIAN_FRONTEND=noninteractive
    apt-get update > /dev/null
    apt-get -y install octave


Accessing the VM, transferring files between it and the host
------------------------------------------------------------

The command ``vagrant up`` leaves the machine running after the
setup. To access it, just run ``ssh -p 2222 vagrant@localhost`` (the
password is *vagrant*). You can also access the VM using VirtualBox's
interface. In case you need graphical output or audio, this is the
simplest way of accessing it (you should add ``config.vm.boot_mode =
gui`` to your Vagrantfile, too). Otherwise, SSH will work fine.

Another magic trick done by Vagrant is to set up shared folders, so
sending files to the VM (and getting them from the VM) is easy. The
folder where you store the configuration files is mounted with
read-write access on the VM at ``/vagrant``.

To shut down the VM, use the commands ``vagrant halt`` or ``vagrant
suspend`` (the latter will save the machine state, which will be
reloaded on the next time you start it).

Final considerations
--------------------

That's it, basically. You can use `these files`_ as a starting point to
design your own virtual machines. There are more complex ways of
provisioning VMs, using tools such as Chef and Puppet, but that's out
of the scope of this blog post. For an excellent example using Puppet,
check out this `Github project`_ (which, in fact, is the project that
inspired me to use VMs for software development in my research!). It
sets up a VM with statistics and numerical libraries for Python.

.. _these files: https://bitbucket.org/jfsantos/vagrant-example
.. _Github project: https://github.com/gavinln/stats_py_vm
