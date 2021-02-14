=============
pysonofflanr3
=============


.. image:: https://img.shields.io/pypi/v/pysonofflanr3.svg
    :target: https://pypi.python.org/pypi/pysonofflanr3
    :alt: Latest PyPi Release

.. image:: https://img.shields.io/pypi/pyversions/pysonofflanr3.svg?style=flat
    :target: https://pypi.python.org/pypi/pysonofflanr3
    :alt: Supported Python Versions

.. image:: https://img.shields.io/travis/mattsaxon/pysonofflan.svg
    :target: https://travis-ci.org/mattsaxon/pysonofflan
    :alt: Build Status

.. image:: https://readthedocs.org/projects/pysonofflanr3/badge/?version=latest
    :target: https://pysonofflanr3.readthedocs.io/
    :alt: Documentation Status

.. image:: https://coveralls.io/repos/github/mattsaxon/pysonofflan/badge.svg
    :target: https://coveralls.io/github/mattsaxon/pysonofflan
    :alt: Code Coverage

.. image:: https://img.shields.io/pypi/wheel/pysonofflanr3.svg
    :target: https://pypi.org/project/pysonofflanr3/#files
    :alt: Has Wheel Package
   
.. image:: https://pyup.io/repos/github/mattsaxon/pysonofflan/shield.svg
    :target: https://pyup.io/repos/github/mattsaxon/pysonofflan/
    :alt: Updates

.. image:: https://pyup.io/repos/github/mattsaxon/pysonofflan/python-3-shield.svg
    :target: https://pyup.io/repos/github/mattsaxon/pysonofflan/
    :alt: Python 3

.. image:: https://www.buymeacoffee.com/assets/img/guidelines/download-assets-sm-2.svg
    :target: https://www.buymeacoffee.com/XTOsBAc
    :alt: Buy Me A Coffee

Control Sonoff devices running original firmware, in LAN mode.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
DISCLAIMER - put no effort in as its no longer being maintained, could try pull request on original if it was worth it

I have modified this unmaintained library to add in --outlet flag for the commandline

I have also modified:
-added self.client.close_connection() in sonoffdevice.shutdown_event_loop()
-added self.service_browser.cancel() in SonoffLANModeClient.close_connection()
these modifications were required to close zombie zeroconf-servicebrowser threads as a new one is created each time an action is called


my calling code is:

SonoffSwitch(host=hostin,api_key=key, outlet=outletin, callback_after_update=callback_xx)

async def callback_on(device):
    if device.basic_info is not None:        
        if device.is_on:
            device.shutdown_event_loop() #then calls this second
        else:
            await device.turn_on() #calls this first


async def callback_off(device):
    if device.basic_info:        
        if not device.is_on:
            device.shutdown_event_loop()
        else:
            await device.turn_off()



alternativly could use this:
import pysonofflanr3.cli
#config = {'host':'xxxxx', 'device_id':'xxxxx', 'api_key':'xxxxx', 'outlet':'xxxxx'}
pysonofflanr3.cli.switch_device(config, None, "on")
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx












To control Sonoff switches running the V3+ Itead firmware (tested on 3.0, 3.0.1, 3.1.0, 3.3.0, 3.4.0, 3.5.0), locally (LAN mode).

# No longer maintained!
This component is no longer being maintained, instead for Home Assistant users, use https://github.com/AlexxIT/SonoffLAN, which is more functional and stable. Any future contributions I make will be to this repo.

Unfortunately @AlexxIT has not (yet) made his component callable outside of Home Assistant, but since I'm not using this component, I will not be maintaining it. Sorry.

**This will only work for Sonoff devices running V3+ of the stock (Itead / eWeLink) firmware. For users of V1.8.0 - V2.6.1, please use**  `PySonoffLAN <https://pypi.org/project/pysonofflan/>`_


This module provides a way to interface with Sonoff smart home devices,
such as smart switches (e.g. Sonoff Basic), plugs (e.g. Sonoff S20),
and wall switches (e.g. Sonoff Touch), when these devices are in LAN Mode.

LAN Mode is a feature introduced by manufacturer Itead, to allow operation
locally when their servers are unavailable.
Further details can be found in the `eWeLink LAN Mode guide`__.

__ https://help.ewelink.cc/hc/en-us/articles/360007134171-LAN-Mode-Tutorial

Since mid 2018, the firmware Itead have shipped with most Sonoff devices
has provided this feature, allowing devices to be controlled directly
on the local network using a WebSocket connection on port 8081.

Features
--------

* Discover all devices on local network
* Read device state
* Switch device ON/OFF
* Listen for state changes announced by the device (e.g. by physical switch)
* Activate inching/momentary device, with variable ON time (e.g. 1s)

Documentation
------------------

* Documentation: https://pysonofflanr3.readthedocs.io.

Install
------------------
::

    $ pip install pysonofflanr3

Command-Line Usage
------------------
::

    Usage: pysonofflanr3 [OPTIONS] COMMAND [ARGS]...

      A cli tool for controlling Sonoff Smart Switches/Plugs in LAN Mode.

    Options:
      --host TEXT          IP address or hostname of the device to connect to.
      --device_id TEXT     Device ID of the device to connect to.
      --inching TEXT       Number of seconds of "on" time if this is an
                           Inching/Momentary switch.
      -l, --level LVL  Either CRITICAL, ERROR, WARNING, INFO or DEBUG
      --help               Show this message and exit.
      --api_key KEY        Needed for devices not in DIY mode. See https://pysonofflanr3.readthedocs.io/encryption.html
      
    Commands:
      discover  Discover devices in the network
      listen    Connect to device, print state and repeat
      off       Turn the device off.
      on        Turn the device on.
      state     Connect to device and print current state.

Usage Example
=======================
::

    $ pysonofflanr3 discover
    2019-01-31 00:45:32,074 - info: Attempting to discover Sonoff LAN Mode devices on the local network, please wait...
    2019-01-31 00:46:24,007 - info: Found Sonoff LAN Mode device at IP 192.168.0.77

    $ pysonofflanr3 --host 192.168.0.77 state
    2019-01-31 00:41:34,931 - info: Initialising SonoffSwitch with host 192.168.0.77
    2019-01-31 00:41:35,016 - info: == Device: 10006866e9 (192.168.0.77) ==
    2019-01-31 00:41:35,016 - info: State: OFF

    $ pysonofflanr3 --host 192.168.0.77 on
    2019-01-31 00:49:40,334 - info: Initialising SonoffSwitch with host 192.168.0.77
    2019-01-31 00:49:40,508 - info:
    2019-01-31 00:49:40,508 - info: Initial state:
    2019-01-31 00:49:40,508 - info: == Device: 10006866e9 (192.168.0.77) ==
    2019-01-31 00:49:40,508 - info: State: OFF
    2019-01-31 00:49:40,508 - info:
    2019-01-31 00:49:40,508 - info: New state:
    2019-01-31 00:49:40,508 - info: == Device: 10006866e9 (192.168.0.77) ==
    2019-01-31 00:49:40,508 - info: State: ON

Library Usage
------------------

All common, shared functionality is available through :code:`SonoffSwitch` class::

    x = SonoffSwitch("192.168.1.50")

Upon instantiating the SonoffSwitch class, a connection is
initiated and device state is populated, but no further action is taken.

For most use cases, you'll want to make use of the :code:`callback_after_update`
parameter to do something with the device after a connection has been
initialised, for example::

    async def print_state_callback(device):
        if device.basic_info is not None:
            print("ON" if device.is_on else "OFF")
            device.shutdown_event_loop()

    SonoffSwitch(
        host="192.168.1.50",
        callback_after_update=print_state_callback
    )

This example simply connects to the device, prints whether it is currently
"ON" or "OFF", then closes the connection. Note, the callback must be
asynchronous.

Module-specific errors are raised as Exceptions, and are expected
to be handled by the user of the library.

License
-------

* Free software: MIT license

Credits
-------

This package was created with Cookiecutter_ and the `audreyr/cookiecutter-pypackage`_ project template.

.. _Cookiecutter: https://github.com/audreyr/cookiecutter
.. _`audreyr/cookiecutter-pypackage`: https://github.com/audreyr/cookiecutter-pypackage

[ ~ Dependencies scanned by PyUp.io ~ ]
