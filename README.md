Repo Manifest for the PX4 Project
=================================
This repository provides Repo manifests to setup the development environment
for the PX4 project.

The PX4 project [1] is an open-source and open-hardware project to create
a high-end autopilot system.  Includes a suite of software used to run and
manage autopilots and vehicles.

[1] https://pixhawk.org/

Repo is a tool that enables the management of many git repositories given a 
single *manifest* file.  Tell repo to fetch a manifest from this repository and
it will fetch the git repositories specified in the manifest and, by doing so,
setup a development environment for you!

Getting Started
---------------
**1.  Install Repo.**

Download the Repo script:

    $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > repo

Make it executable:

    $ chmod a+x repo

Move it on to your system path:

    $ sudo mv repo /usr/local/bin/

If it is correctly installed, you should see a Usage message when invoked
with the help flag.

    $ repo --help

**2.  Initialize a Repo client.**

Create an empty directory to hold your working files:

    $ mkdir px4
    $ cd px4

Tell Repo where to find the manifest:

    $ repo init -u git://github.com/aerocore/manifest.git 

A successful initialization will end with a message stating that Repo is
initialized in your working directory. Your directory should now
contain a .repo directory where repo control files such as the manifest are
stored but you should not need to touch this directory.

------------------------------------------------------------------------------
**Note:**

You can use the **-b** switch to specify the branch of the repository to use.
We develop on the **dev** branch and filter stable changes down to **master**
branch.

The **-m** switch selects the manifest file (default is *default.xml*).
Our default.xml on **master** is designed to be stable as it *pins*
particular commits.

To test out the bleeding edge, type:

    $ repo init -u git://github.com/aerocore/manifest.git -b dev
    $ repo sync

To get back to the known stable version, type:

    $ repo init -u git://github.com/aerocore/manifest.git -b master
    $ repo sync

To learn more about repo, look at http://source.android.com/source/version-control.html 

------------------------------------------------------------------------------

**3.  Fetch all the repositories.**

    $ repo sync

**4. Fetch a Toolchain.**

To build for the Cortex-M4 processor on AeroCore, you need a bare-metal ARM
toolchain.  We grab a pre-built toolchain and install into our *tmp* directory.

    $ cd build
    $ ./get-toolchain.sh
    $ cd ..

**5.  Initialize the PX4 Development Environment.**

    $ source source-me.sh

Now we should have the compiler on our path.  We can test:

    $ arm-none-eabi-gcc -v

You should see information about the version of the compiler as output.

**6.  Build.** 

Start by building a utility library for our processor:

    $ make -C libopencm3 lib

Next, build the bootloader [OPTIONAL]:

    $ make -C bootloader

After the build completes successfully, there will be *px4aerocore_bl.bin*
binary in the *bootloader* directory.  We can also build the Nuttx operating
system.

    $ make -C firmware archives

The final step is wrapping the Nuttx archive with our board-specific code.

    $ make -C firmware aerocore_default

If everything goes well, you should have a aerocore_default.bin binary NuttX
image in the *firmware/Images* directory. As you make changes for your board,
you'll want to recompile with this last step.

If you run into problems, the most likely candidate is missing software
packages.  Check out https://pixhawk.org/dev/minimal_build_env for the list of
required packages for your operating system.

**7. Flash your board:**

Now you just have to write the binaries you've built to your hardware. Write
the firmware into flash memory:

    $ make aerocore_default upload 

Hooray, you are done!

Staying Up to Date
------------------
To pick up the latest changes for all source repositories, run:

    $ repo sync

Enter the PX4 Project development environment:

    $ source source-me.sh

    If you forget to setup these environment variables prior to developing,
    **make** will complain that it can't find gcc-none-armebi- on the path.
    Don't try to install anything---just run the above command.

You can then rebuild as before.

Customize
---------
Sooner or later, you'll want to customize some aspect of this environment
either adding by more code, picking up some upstream patches, or tweaking
NuttX. To this end, you'll want to customize the Repo manifest to point
at different repositories and branches.

Clone this repository (or fork it on github):

    $ git clone git://github.com/aerocore/manifest.git

Make your changes (and contribute them back if they are generally useful), and
then re-initialize your repo client

    $ repo init -u <file:///path/to/your/git/repository.git>

