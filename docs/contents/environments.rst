.. index:: ! environments
.. _environments:

Environments
============

REY apps can be built locally in a development environment before being published on the testnet environment. This section describes the differences between both.

.. _development:

Development
-----------

Before making our app available to everyone, we'll be building it inside our own development environment. To achieve this, the simplest way is to run your own private node with REY's smart contract and registry already deployed. There's a Docker image that provides a ``geth`` instance that has both smart contracts already deployed and ready to use at the following addresses:

- Main smart contract address: ``0x6224d471b8590de463d27b067174b566b4b0b041``

- Registry address: ``0xc25b4ff9eb6f52392eef1e103daacc7519795f01``

To start the node, make sure to have `docker <http://docker.com>`_ properly installed, then run:

.. code::

  $ docker run reputationnetwork:node -p 4567:4567

This will launch the geth node with an open port to connect via RPC and interact with it. The account with address ``0x88032398beab20017e61064af3c7c8bd38f4c968`` has funds and is available to be used.

The :ref:`tutorial <tutorial>` shows how to interact with the registry and smart contract to publish a hello world app on the development environment.

.. _testnet:

Testnet
-------

The testnet enables your REY app to work outside your own development environment (i.e., your laptop or computer). It is available at Rinkeby Ethereum Testnet with the following addresses:

- Main smart contract address: ``0x6224d471b8590de463d27b067174b566b4b0b041``

- Registry address: ``0xc25b4ff9eb6f52392eef1e103daacc7519795f01``