.. image:: /_themes/akeneo/static/behat-logo.png
   :width: 150
   :alt: Behat logo
   :target: http://behat.org/

Setup Behat
===========

The PIM comes with a set of Behat scenarios: https://github.com/akeneo/pim-community-dev/tree/master/features

These scenarios allow to:

* describe the PIM features and the expected behavior for a real user
* ensure there is no regression from functional point of view during the development cycle


Install Behat
-------------
You can install Behat dependencies with composer (on pim-community-dev).

.. code-block:: bash

  $ php composer.phar install --dev


Install Selenium Server
-----------------------
Download Selenium server 2.38 `here`_.

.. _here: http://docs.seleniumhq.org/download/


Install Firefox 26.0
--------------------
In order to use Selenium RC, you must actually install `firefox 26.0`_.

.. _firefox 26.0: http://ftp.mozilla.org/pub/mozilla.org/firefox/releases/26.0/


Create a VirtualHost
--------------------
To be sure to test in an environment as close as possible to a production environment,
we need to define a specific VirtualHost with production RewriteRule (some limitations
of Oro Platform does not allow to have production environment with the boostrap file
included in the URL):

.. code-block:: apache

    <VirtualHost *:80>
        ServerName akeneo-pim-behat.local

        RewriteEngine On

        DocumentRoot /home/akeneo/pim//web
        <Directory /home/akeneo/pim/web>
            Options Indexes FollowSymLinks MultiViews
            AllowOverride None
            Order allow,deny
            allow from all
        </Directory>

        RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f
        RewriteRule ^(.*) %{DOCUMENT_ROOT}/app_behat.php [QSA,L]

        ErrorLog ${APACHE_LOG_DIR}/akeneo-pim-behat_error.log

        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/akeneo-pim-behat_access.log combined

    </VirtualHost>

.. note::

    Do not forget to update your ``/etc/hosts`` file to include the VirtualHost hostname:

.. code-block:: bash

    127.0.0.1   akeneo-pim-behat.local


Configure Behat
---------------

Setup the test environment, begin by copy and update the app/config/parameters_test.yml, then run:

.. code-block:: bash

    $ ./install.sh all test

Then copy behat.yml.dist to behat.yml, edit base_url parameter to match your host:

.. code-block:: yaml

    default:
        ...
        extensions:
            Behat\MinkExtension\Extension:
                ...
                base_url: http://akeneo-pim.local/app_behat.php/


Run features
------------

You can now launch Selenium by issuing the following command:

.. code-block:: bash

  $ java -jar selenium-server-standalone-2.33.0.jar


Feature tests can be run by issuing the following command:

.. code-block:: bash

  > ~/git/pim-community-dev$ ./bin/behat

More details and options are available on http://behat.org/

