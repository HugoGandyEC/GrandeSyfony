.. index::
   single: Installation

Installing and Configuring Symfony
==================================

The goal of this chapter is to get you up and running with a working application
built on top of Symfony. Fortunately, Symfony offers "distributions", which
are functional Symfony "starter" projects that you can download and begin
developing in immediately.

.. tip::

    If you're looking for instructions on how best to create a new project
    and store it via source control, see `Using Source Control`_.

Downloading a Symfony2 Distribution
-----------------------------------

.. tip::

    First, check that you have installed and configured a Web server (such
    as Apache) with PHP 5.3.2 or higher. For more information on Symfony2
    requirements, see the :doc:`requirements reference</reference/requirements>`.

Symfony2 packages "distributions", which are fully-functional applications
that include the Symfony2 core libraries, a selection of useful bundles, a
sensible directory structure and some default configuration. When you download
a Symfony2 distribution, you're downloading a functional application skeleton
that can be used immediately to begin developing your application.

Start by visiting the Symfony2 download page at `http://symfony.com/download`_.
On this page, you'll see the *Symfony Standard Edition*, which is the main
Symfony2 distribution. Here, you'll need to make two choices:

* Download either a ``.tgz`` or ``.zip`` archive - both are equivalent, download
  whatever you're more comfortable using;

* Download the distribution with or without vendors. If you have `Git`_ installed
  on your computer, you should download Symfony2 "without vendors", as it
  adds a bit more flexibility when including third-party/vendor libraries.

Download one of the archives somewhere under your local web server's root
directory and unpack it. From a UNIX command line, this can be done with
one of the following commands (replacing ``###`` with your actual filename):

.. code-block:: bash

    # for .tgz file
    $ tar zxvf Symfony_Standard_Vendors_2.0.###.tgz

    # for a .zip file
    $ unzip Symfony_Standard_Vendors_2.0.###.zip

When you're finished, you should have a ``Symfony/`` directory that looks
something like this:

.. code-block:: text

    www/ <- your web server directory (sometimes named htdocs or public)
        Symfony/ <- the unpacked archive
            app/
                cache/
                config/
                logs/
            src/
                ...
            vendor/
                ...
            web/
                app.php
                ...

.. note::

    You can easily override the default directory structure. See
    :doc:`/cookbook/configuration/override_dir_structure` for more
    information.

All public files and the front controller that handles incoming requests in
a Symfony2 application live in the ``Symfony/web/`` directory. So, assuming
you unpacked the archive into your web server's or virtual host's document root,
your application's URLs will start with ``http://localhost/Symfony/web/``.
To get nice and short URLs you should point the document root of your web
server or virtual host to the ``Symfony/web/`` directory. Though this is not
required for development it is recommended when your application goes into
production as all system and configuration files become inaccessible to clients.
For information on configuring your specific web server document root, see
the following documentation: `Apache`_ | `Nginx`_ .

.. note::

    The following examples assume you don't touch the document root settings
    so all URLs start with ``http://localhost/Symfony/web/``

Updating Vendors
~~~~~~~~~~~~~~~~

Finally, if you downloaded the archive "without vendors", install the vendors
by running the following command from the command line:

.. code-block:: bash

    $ php bin/vendors install

This command downloads all of the necessary vendor libraries - including
Symfony itself - into the ``vendor/`` directory. For more information on
how third-party vendor libraries are managed inside Symfony2, see
":ref:`cookbook-managing-vendor-libraries`".

Configuration and Setup
~~~~~~~~~~~~~~~~~~~~~~~

At this point, all of the needed third-party libraries now live in the ``vendor/``
directory. You also have a default application setup in ``app/`` and some
sample code inside the ``src/`` directory.

Symfony2 comes with a visual server configuration tester to help make sure
your Web server and PHP are configured to use Symfony. Use the following URL
to check your configuration:

.. code-block:: text

    http://localhost/Symfony/web/config.php

If there are any issues, correct them now before moving on.

.. sidebar:: Setting up Permissions

    One common issue is that the ``app/cache`` and ``app/logs`` directories
    must be writable both by the web server and the command line user. On
    a UNIX system, if your web server user is different from your command
    line user, you can run the following commands just once in your project
    to ensure that permissions will be setup properly.

    **Note that not all web servers run as the user** ``www-data`` as in the examples
    below. Instead, check which user *your* web server is being run as and
    use it in place of ``www-data``.

    On a UNIX system, this can be done with one of the following commands:

    .. code-block:: bash
    
        $ ps aux | grep httpd

    or

    .. code-block:: bash

        $ ps aux | grep apache

    **1. Using ACL on a system that supports chmod +a**

    Many systems allow you to use the ``chmod +a`` command. Try this first,
    and if you get an error - try the next method. Be sure to replace ``www-data``
    with your web server user on the first ``chmod`` command:

    .. code-block:: bash

        $ rm -rf app/cache/*
        $ rm -rf app/logs/*

        $ sudo chmod +a "www-data allow delete,write,append,file_inherit,directory_inherit" app/cache app/logs
        $ sudo chmod +a "`whoami` allow delete,write,append,file_inherit,directory_inherit" app/cache app/logs
    
    **2. Using Acl on a system that does not support chmod +a**

    Some systems don't support ``chmod +a``, but do support another utility
    called ``setfacl``. You may need to `enable ACL support`_ on your partition
    and install setfacl before using it (as is the case with Ubuntu), like
    so:

    .. code-block:: bash

        $ sudo setfacl -R -m u:www-data:rwX -m u:`whoami`:rwX app/cache app/logs
        $ sudo setfacl -dR -m u:www-data:rwx -m u:`whoami`:rwx app/cache app/logs

    **3. Without using ACL**

    If you don't have access to changing the ACL of the directories, you will
    need to change the umask so that the cache and log directories will
    be group-writable or world-writable (depending if the web server user
    and the command line user are in the same group or not). To achieve
    this, put the following line at the beginning of the ``app/console``,
    ``web/app.php`` and ``web/app_dev.php`` files::

        umask(0002); // This will let the permissions be 0775

        // or

        umask(0000); // This will let the permissions be 0777

    Note that using the ACL is recommended when you have access to them
    on your server because changing the umask is not thread-safe.

When everything is fine, click on "Go to the Welcome page" to request your
first "real" Symfony2 webpage:

.. code-block:: text

    http://localhost/Symfony/web/app_dev.php/

Symfony2 should welcome and congratulate you for your hard work so far!

.. image:: /images/quick_tour/welcome.jpg

.. tip::
    
    To get nice and short urls you should point the document root of your 
    webserver or virtual host to the ``Symfony/web/`` directory. Though 
    this is not required for development it is recommended at the time your 
    application goes into production as all system and configuration files
    become inaccessible to clients then. For information on configuring 
    your specific web server document root, read
    :doc:`/cookbook/configuration/web_server_configuration`
    or consult the official documentation of your webserver:
    `Apache`_ | `Nginx`_ .

Beginning Development
---------------------

Now that you have a fully-functional Symfony2 application, you can begin
development! Your distribution may contain some sample code - check the
``README.md`` file included with the distribution (open it as a text file)
to learn about what sample code was included with your distribution.

If you're new to Symfony, check out ":doc:`page_creation`", where you'll
learn how to create pages, change configuration, and do everything else you'll
need in your new application.

.. note::

    If you want to remove the sample code from your distribution, take a look
    at this cookbook article: ":doc:`/cookbook/bundles/remove`"

Using Source Control
--------------------

If you're using a version control system like ``Git`` or ``Subversion``, you
can setup your version control system and begin committing your project to
it as normal. The Symfony Standard edition *is* the starting point for your
new project.

For specific instructions on how best to setup your project to be stored
in git, see :doc:`/cookbook/workflow/new_project_git`.

Ignoring the ``vendor/`` Directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you've downloaded the archive *without vendors*, you can safely ignore
the entire ``vendor/`` directory and not commit it to source control. With
``Git``, this is done by creating and adding the following to a ``.gitignore``
file:

.. code-block:: text

    vendor/

Now, the vendor directory won't be committed to source control. This is fine
(actually, it's great!) because when someone else clones or checks out the
project, he/she can simply run the ``php bin/vendors install`` script to
download all the necessary vendor libraries.

.. _`enable ACL support`: https://help.ubuntu.com/community/FilePermissionsACLs
.. _`http://symfony.com/download`: http://symfony.com/download
.. _`Git`: http://git-scm.com/
.. _`GitHub Bootcamp`: http://help.github.com/set-up-git-redirect
.. _`Apache`: http://httpd.apache.org/docs/current/mod/core.html#documentroot
.. _`Nginx`: http://wiki.nginx.org/Symfony
