=============================================
Upgrading to PHP 5.6 on RedHat 7 and Centos 7
=============================================

You should, almost, always upgrade to the latest version of PHP, if and where possible. 
And if you’re on a version of PHP older than 5.6 you need to upgrade.
This guide steps you through upgrading your installation of PHP to version 5.6 or 7.0 if you’re on RedHat or Centos 7.

.. note:: 
   To know more about registering and subscribing a system to the Red Hat Customer Portal using the Red Hat Subscription-Manager, please refer to `the official documentation`_. 

Upgrading to PHP 5.6
--------------------

.. note::
   You should really be upgrading to PHP 7, as version 5.6 is `no longer actively supported`_, and security support ends on 31 Dec, 2018. 

You will first require to subscribe to the Red Hat Software Collections channel repository to download and install the PHP56 package in RHEL 7

.. code-block:: console
   
   subscription-manager repos --enable rhel-server-rhscl-7-rpms

Then proceed by installing the required PHP 5.6 modules

.. code-block:: console

   yum install rh-php56 rh-php56-php rh-php56-php-gd rh-php56-php-mbstring rh-php56-php-mysqlnd rh-php56-php-intl rh-php56-php-ldap

Enable PHP 5.6 system-wide

.. code-block:: console

   cp /opt/rh/rh-php56/enable /etc/profile.d/rh-php56.sh source /opt/rh/rh-php56/enable

Disable loading the old PHP 5.4 Apache modules by changing their names

.. code-block:: console

   mv /etc/httpd/conf.d/php.conf /etc/httpd/conf.d/php54.off
   mv /etc/httpd/conf.modules.d/10-php.conf /etc/httpd/conf.modules.d/10-php54.off

Copy the PHP 5.6 Apache modules into place

.. code-block:: console

   cp /opt/rh/httpd24/root/etc/httpd/conf.d/rh-php56-php.conf /etc/httpd/conf.d/
   cp /opt/rh/httpd24/root/etc/httpd/conf.modules.d/10-rh-php56-php.conf /etc/httpd/conf.modules.d/
   cp /opt/rh/httpd24/root/etc/httpd/modules/librh-php56-php5.so /etc/httpd/modules/

Restart Apache

.. code-block:: console

   service httpd restart

Upgrading to PHP 7.0
--------------------

You will first require to subscribe to the Red Hat Software Collections channel repository to download and install the PHP56 package in RHEL 7

.. code-block:: console
   
   subscription-manager repos --enable rhel-server-rhscl-7-rpms

Then proceed by installing the required PHP 5.6 modules

.. code-block:: console

   yum install rh-php70 rh-php70-php rh-php70-php-gd rh-php70-php-mbstring rh-php70-php-mysqlnd rh-php70-php-intl rh-php70-php-ldap

Enable PHP 5.6 system-wide

.. code-block:: console

   cp /opt/rh/rh-php70/enable /etc/profile.d/rh-php70.sh source /opt/rh/rh-php70/enable

Disable loading the old PHP 5.6 Apache modules by changing their names

.. code-block:: console

   mv /etc/httpd/conf.d/php.conf /etc/httpd/conf.d/php56.off
   mv /etc/httpd/conf.modules.d/10-php.conf /etc/httpd/conf.modules.d/10-php56.off

Copy the PHP 5.6 Apache modules into place

.. code-block:: console

   cp /opt/rh/httpd24/root/etc/httpd/conf.d/rh-php70-php.conf /etc/httpd/conf.d/
   cp /opt/rh/httpd24/root/etc/httpd/conf.modules.d/15-rh-php70-php.conf /etc/httpd/conf.modules.d/
   cp /opt/rh/httpd24/root/etc/httpd/modules/librh-php70-php7.so /etc/httpd/modules/

Restart Apache

.. code-block:: console

   service httpd restart

Installing Redis Support
------------------------

To add the Redis client extension, run the commands below. 

.. code-block:: console
   
   yum install rh-php70-php-devel
   pecl install redis 

Then, when the installation add a configuration file to enable the extension, using the following commands.

.. code-block:: console

   cat << EOF > /etc/opt/rh/rh-php70/php.d/20-redis.ini
   ; Redis php extention
   extension=redis.so
   EOF

Installing OCI8 Client Support
------------------------------

First, you need to download ``oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm`` and ``oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm`` `from Oracle`_.
After you’ve done that, install them using the following command

::

   rpm --install oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm \
     oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm

With that done, you’re now ready to install the OCI8 extension. 
First, add a configuration file for it, by using the command below. 

.. code-block:: console
   
   cat << EOF > /etc/opt/rh/rh-php70/php.d/20-oci8.ini
   ; Oracle Instant Client Shared Object
   extension=oci8.so
   EOF
   
Then, build it for PHP 7 by running the following command, providing: ``instantclient,/usr/lib/oracle/12.2/client64/lib`` when requested.

.. code-block:: console

   pecl install oci8

.. Links
   
.. _no longer actively supported: https://secure.php.net/supported-versions.php
.. _from Oracle: http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html
.. _the official documentation: https://access.redhat.com/solutions/253273
