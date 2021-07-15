===========================
openwrt-openwisp-monitoring
===========================

.. image:: https://github.com/openwisp/openwrt-openwisp-monitoring/workflows/OpenWRT%20OPENWISP%20MONITORING%20CI%20Build/badge.svg?branch=master
   :target: https://github.com/openwisp/openwrt-openwisp-monitoring/actions?query=OpenWRT+OPENWISP+MONITORING+CI+Build%22
   :alt: CI build status

.. image:: https://coveralls.io/repos/github/openwisp/openwrt-openwisp-monitoring/badge.svg
   :target: https://coveralls.io/github/openwisp/openwrt-openwisp-monitoring
   :alt: Test Coverage

.. image:: http://img.shields.io/github/release/openwisp/openwrt-openwisp-monitoring.svg
   :target: https://github.com/openwisp/openwrt-openwisp-monitoring/releases

.. image:: https://img.shields.io/gitter/room/nwjs/nw.js.svg?style=flat-square
   :target: https://gitter.im/openwisp/general
   :alt: support chat

------------

`OpenWRT <https://openwrt.org/>`_ configuration agent for 
`OpenWISP Monitoring <https://github.com/openwisp/openwisp-monitoring>`_.

**Want to help OpenWISP?** `Find out how to help us grow here
<http://openwisp.io/docs/general/help-us.html>`_.

.. image:: http://netjsonconfig.openwisp.org/en/latest/_images/openwisp.org.svg
  :target: http://openwisp.org

.. contents:: **Table of Contents**:
 :backlinks: none
 :depth: 3

Configuration options
---------------------

UCI configuration options must go in ``/etc/config/monitoring``.

- ``monitored_interfaces``: interfaces that needs to be monitored, defaults to ``*`` for all interfaces.
- ``interval``: time after which device data should be sent to server, defaults to ``300``.
- ``verbose_mode``: can be used to get verbose output in case of error for easy `debugging <#debugging>`__, defaults to 0.

Compiling openwrt-openwisp-monitoring
-------------------------------------

There are 2 packages for *openwisp-netjson-monitoring*:

- **netjson-monitoring**: provides NetJSON Device Monitoring output
- **openwisp-monitoring**: depends on **netjson-monitoring** and `openwisp-config <https://github.com/openwisp/openwisp-config>`_

There are four variants of openwisp-monitoring:

- **openwisp-monitoring-openssl**: depends on *openwisp-config-openssl* and *netjson-monitoring*
- **openwisp-monitoring-mbedtls**: depends on *openwisp-config-mbedtls* and *netjson-monitoring*
- **openwisp-monitoring-wolfssl**: depends on *openwisp-config-wolfssl* and *netjson-monitoring*
- **openwisp-monitoring-nossl**: depends on *openwisp-config-nossl* and *netjson-monitoring*

The following procedure illustrates how to compile all variants of *openwisp-monitoring*, *netjson-monitoring* and their dependencies:

.. code-block:: shell

    git clone https://git.openwrt.org/openwrt/openwrt.git
    cd openwrt
    git checkout openwrt-21.02

    # configure feeds
    echo "src-git monitoring https://github.com/openwisp/openwrt-openwisp-monitoring.git" > feeds.conf
    cat feeds.conf.default >> feeds.conf
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    echo "CONFIG_PACKAGE_netjson-monitoring=y" >> .config
    echo "CONFIG_PACKAGE_openwisp-monitoring-mbedtls=y" >> .config
    echo "CONFIG_PACKAGE_openwisp-monitoring-nossl=y" >> .config
    echo "CONFIG_PACKAGE_openwisp-monitoring-openssl=y" >> .config
    echo "CONFIG_PACKAGE_openwisp-monitoring-wolfssl=y" >> .config    
    make defconfig
    make tools/install
    make toolchain/install
    make package/openwrt-openwisp-monitoring/compile

The compiled packages will go in ``bin/packages/*/openwisp``.

Alternatively, you can configure your build interactively with ``make menuconfig``, in this case
you will need to select the *openwisp-monitoring* variant and *netjson-monitoring* by going to ``Administration > admin > openwisp``:

.. code-block:: shell

    git clone https://git.openwrt.org/openwrt/openwrt.git
    cd openwrt
    git checkout openwrt-21.02

    # configure feeds
    echo "src-git openwisp https://github.com/openwisp/openwisp-monitoring.git" > feeds.conf
    cat feeds.conf.default >> feeds.conf
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make menuconfig
    # go to Base system, then select rpcd
    # go to Administration > admin > openwisp and select the packages you need interactively
    make tools/install
    make toolchain/install
    make package/openwrt-openwisp-monitoring/compile

Once installed *openwisp-monitoring* needs to be configured (see `Configuration options`_)
and then started with::

    /etc/init.d/openwisp_monitoring restart

Debugging
---------

Debugging *openwisp-monitoring package* can be easily done by using the ``logread`` command::

    logread

Use grep to filter out any other log message::

    logread | grep monitoring

If you are in that doubt openwisp-monitoring is running at all or not, you can check with::

    ps | grep monitoring

You should see something like::

    2713 root      1224 S    /bin/sh /usr/sbin/openwisp_monitoring --url http://192.168.1.195:8000 ...

You can inspect the version of openwisp-monitoring currently installed with::

    openwisp_monitoring --version

Run tests
---------

To run the unit tests, you must install the required dependencies first; to do this, you can take
a look at the `install-dev.sh <https://github.com/openwisp/openwisp-config/blob/master/install-dev.sh>`_ script.


Install test requirements::

    sudo ./install-dev.sh

Run quality assurance tests with::

    #install openwisp-utils QA tools first
    pip install openwisp-utils[qa]

    #run QA checks before committing code
    ./run-qa-checks

You can run all unit tests by launching the dedicated script::

    ./runtests

Alternatively, you can run specific tests, e.g.::

    cd openwrt-openwisp-monitoring/tests/
    lua test_utils.lua -v

Contributing
------------

Please read the `OpenWISP contributing guidelines
<http://openwisp.io/docs/developer/contributing.html>`_.

Changelog
---------

See `CHANGELOG <https://github.com/openwisp/openwrt-openwisp-monitoring/blob/master/CHANGELOG.rst>`_.

License
-------

See `LICENSE <https://github.com/openwisp/openwrt-openwisp-monitoring/blob/master/LICENSE>`_.

Support
-------

See `OpenWISP Support Channels <http://openwisp.org/support.html>`_.
