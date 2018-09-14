.. index:: ! environments
.. _environments:

Environments
============

REY apps can be built locally in a development environment before being published on the testnet environment. This section describes the differences between both.

When using ``rey-cli``, you can easily switch from one environment to another by changing the first argument (``dev`` or ``test``). You can also set a ``custom`` environment if you're running your own node, and specify your RPC URL and contract addresses.

.. _development:

Development
-----------

Before making our app available to everyone, we'll be building it inside our own development environment. To achieve this, the simplest way is to run your own private node with REY's smart contract and registry already deployed. There's a Docker image that provides a ``geth`` instance that has both smart contracts already deployed and ready to use at the following addresses:

- Main smart contract address: ``0x76C19376b275A5d77858c6F6d5322311eEb92cf5``.

- Registry address: ``0x556ED3bEaF6b3dDCb1562d3F30f79bF86fFC05B9``.

To start the node, make sure to have `Docker <http://docker.com>`_ properly installed, then run:

.. code::

  $ rey-cli dev node

This will launch the geth node with an open port to connect via RPC and interact with it. The following accounts have funds and are available to be used in the node:

- ``0x31bb9d47bc8bf6422ff7dcd2ff53bc90f8f7b009``
- ``0x88032398beab20017e61064af3c7c8bd38f4c968``
- ``0xc25b4ff9eb6f52392eef1e103daacc7519795f01``
- ``0x6d644c57247de51da20797f14dceedfbc4ef6561``
- ``0xe370c47450427a2baa9bff3557bf574162f3ca54``
- ``0xefdd1029b00e1add52c478f85c00c1011a347128``
- ``0x60cb2204f342dd35bf5a328a03d86dd71d4372ec``
- ``0x6224d471b8590de463d27b067174b566b4b0b041``
- ``0x128ab682efe2a1ec3970d374d23a7f249fb9e8df``
- ``0x44f1d336e4fdf189d2dadd963763883582c45312``

.. note::

  The development blockchain node has built-in accounts that have no password. When running REY commands, simply enter a blank password when prompted.

The :ref:`tutorial <tutorial>` shows how to interact with the registry and smart contract to publish a hello world app on the development environment.

.. _testnet:

Testnet
-------

The testnet enables your REY app to work outside your own development environment (i.e., your laptop or computer). It is available at Rinkeby Ethereum Testnet with the following addresses:

- Main smart contract address: ``0x6224d471b8590de463d27b067174b566b4b0b041``.

- Registry address: ``0xc25b4ff9eb6f52392eef1e103daacc7519795f01``.
