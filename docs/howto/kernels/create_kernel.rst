.. SPDX-License-Identifier: CC-BY-SA-4.0

Create a Custom Kernel with ukpack
==================================

This guide explains how to create a custom Linux kernel package using `ukpack`.

Prerequisites
-------------

Ensure you have the necessary dependencies installed:

.. prompt:: bash $ auto

    $ sudo apt-get update
    $ sudo apt-get install git wget devscripts

Custom Kernel Tree
------------------

To create a kernel package a Git repository containing a custom kernel tree is needed. The
repository should be based on an upstream Linux kernel version, which will be referred to
as ``<kernel_version>`` in the following steps and should contain any necessary modifications.

Clone Required Repositories
---------------------------

First, clone the `ukpack` repository:

.. prompt:: bash $ auto

    $ git clone https://kernel.ubuntu.com/forgejo/esmil/ukpack.git

Then, download the upstream Linux kernel source from which the custom kernel is based:

.. prompt:: bash $ auto

    $ wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-<kernel_version>.tar.xz

Next, clone the custom kernel repository:

.. prompt:: bash $ auto

    $ git clone https://github.com/your_username/your_custom_kernel.git
    $ cd your_custom_kernel/

Sync with Upstream Kernel
-------------------------

If the custom kernel tree is based on the Linux stable branch, you can add the upstream repository as a remote and fetch a specific tag:

.. prompt:: bash $ auto

    $ git remote add linux-stable https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
    $ git fetch --depth 1 linux-stable tag v<kernel_version>

Create Kernel Package Configuration
-----------------------------------

Inside the directory of your custom kernel repository (`your_custom_kernel`), create a `your_kernel.toml` file with the following content:

.. code:: text

    your_kernel (<kernel_version>-<local_version>) noble; urgency=medium
     * Initial packaging
     -- Your Name <your_email@example.com> Wed, DD MMM YYYY HH:MM:SS +TZ
    ---
    arch = "<your_architecture>"
    config = "arch/<your_architecture>/configs/your_defconfig"
    orig = "v<kernel_version>"
    [pkg.source]
    Maintainer = "your_email@example.com"

Build the Kernel Package
------------------------

Create an output directory outside your kernel tree and run `ukpack`:

.. prompt:: bash $ auto

    $ mkdir ../ukpack.output/
    $ ../ukpack/ukpack -o ../linux-<kernel_version>.tar.xz -t . -d ../ukpack.output/ your_kernel.toml

Sign the Package
----------------

Change into the output directory and sign the package:

.. prompt:: bash $ auto

    $ cd ../ukpack.output
    $ debsign *.changes

Next Steps
----------

After signing, you can proceed with testing or uploading the package.
