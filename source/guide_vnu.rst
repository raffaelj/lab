.. highlight:: console

.. author:: Raffael Jesche <https://www.rlj.me>

.. tag:: lang-java
.. tag:: html5
.. tag:: validation

.. sidebar:: Logo

  .. image:: _static/images/vnu.png
      :align: center

########
v.Nu
########

.. tag_list::

The Nu Html Checker (v.Nu) helps you catch unintended mistakes in your HTML, CSS, and SVG. It enables you to batch-check documents from the command line and from other scripts/apps, and to deploy your own instance of the checker as a service (like validator.w3.org/nu).

----

.. note:: For this guide you should be familiar with the basic concepts of

  * :manual_anchor:`supervisord <daemons-supervisord>`
  * :manual_anchor:`web backends <web-backends>`

.. note:: Recommended reading to follow along and go beyond this guide:

  * `Official v.Nu Manual <https://validator.github.io/validator/>`_
  * `Official v.Nu JAR guide <https://validator.github.io/validator/#web-based-checking>`_

License
=======

v.Nu is released under the `MIT License <https://github.com/validator/validator/blob/master/LICENSE>`_.

Prerequisites
=============

We're using Java version 8.

::

 [isabell@stardust ~]$ java -version
 openjdk version "1.8.0_242"
 OpenJDK Runtime Environment (build 1.8.0_242-b08)
 OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)
 [isabell@stardust ~]$

Your URL needs to be setup:

.. include:: includes/web-domain-list.rst


Installation
============

Download and extract ZIP archive
-----------------

Check the v.Nu_ website or the `GitHub repo`_ for the `latest release`_ and copy the download link to the JAR ZIP file. Use ``wget`` to download it. Replace the URL with the one you just copied.

.. _v.Nu: https://validator.github.io/validator/
.. _latest release: https://github.com/validator/validator/releases/latest
.. _Github repo: https://github.com/validator/validator

::

 [isabell@stardust ~]$ wget https://github.com/validator/validator/releases/download/20.3.16/vnu.jar_20.3.16.zip
 --2020-04-04 17:53:17--  https://github.com/validator/validator/releases/download/20.3.16/vnu.jar_20.3.16.zip
 Resolving github.com (github.com)... 140.82.118.4
 Connecting to github.com (github.com)|140.82.118.4|:443... connected.
 HTTP request sent, awaiting response... 302 Found
 Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/1133930/f8327380-6797-11ea-80e8-a237f4c66f41?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200404%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200404T155318Z&X-Amz-Expires=300&X-Amz-Signature=b5b9906b778982b7563a32cf02bd3cecfe42a435e14458b7147e694eaeb73a8a&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dvnu.jar_20.3.16.zip&response-content-type=application%2Foctet-stream [following]
 --2020-04-04 17:53:18--  https://github-production-release-asset-2e65be.s3.amazonaws.com/1133930/f8327380-6797-11ea-80e8-a237f4c66f41?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200404%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200404T155318Z&X-Amz-Expires=300&X-Amz-Signature=b5b9906b778982b7563a32cf02bd3cecfe42a435e14458b7147e694eaeb73a8a&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dvnu.jar_20.3.16.zip&response-content-type=application%2Foctet-stream
 Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.177.187
 Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.177.187|:443... connected.
 HTTP request sent, awaiting response... 200 OK
 Length: 29000590 (28M) [application/octet-stream]
 Saving to: ‘vnu.jar_20.3.16.zip’

 100%[============================================================>] 29,000,590  14.7MB/s   in 1.9s

 2020-04-04 17:53:20 (14.7 MB/s) - ‘vnu.jar_20.3.16.zip’ saved [29000590/29000590]

 [isabell@stardust ~]$

Unzip the archive to ``~/vnu`` folder and then delete it. Replace the version in the file name with the one you downloaded.

::

 [isabell@stardust ~]$ unzip vnu.jar_20.3.16.zip -d vnu
 Archive:  vnu.jar_20.3.16.zip
    creating: vnu/dist/
  extracting: vnu/dist/index.html
  extracting: vnu/dist/LICENSE
  extracting: vnu/dist/CHANGELOG.md
  extracting: vnu/dist/README.md
  extracting: vnu/dist/vnu.jar
 [isabell@stardust ~]$ rm vnu.jar_20.3.16.zip
 [isabell@stardust ~]$

At this point you would already be able to run v.Nu, but you wouldn't be able to connect to it and it would not run as a service.

Configuration
==============

Install service
---------------

Create the service file ``~/etc/services.d/vnu.ini`` and fill it with the following snippet and replace ``<port>`` with your port number, e. g.: 8080:

::

 [program:vnu]
 directory:%(ENV_HOME)s/vnu/dist
 command=java -cp vnu.jar -Dnu.validator.servlet.bind-address=0.0.0.0 nu.validator.servlet.Main <port>

.. include:: includes/supervisord.rst

Your v.Nu is now up and running as a service.

Finally we'll setup our connection to the rest of the world.

Setup Web backend
-----------------

.. include:: includes/web-backend.rst

.. note::

    If you want to set the web backend to ``/nu``, you have to set the ``--remove-prefix`` flag while setting up the web backend and you have to redirect ``/nu`` to ``/nu/`` with a trailing slash. Otherwise calling the url ``https://isabell.uber.space/nu`` would result in a Jetty 404 error. Calling ``https://isabell.uber.space/nu/`` directly works fine.

Updates
=======

To jump to a new version just replace the old ``vnu.jar`` with the new version in ``~/vnu/dist/``.

.. author_list::
