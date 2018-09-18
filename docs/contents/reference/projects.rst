.. index:: ! projects
.. _projects:

Projects
========

REY's software is splitted across different projects that are available in several Github repositories. Here's a list of all of them.


rey-docs
--------

This is REY's documentation (i.e., what you're reading now), which is deployed at `Read the Docs <readthedocs.org>`_.

* Github repository: `reputation-network/rey-docs <http://github.com/reputation-network/rey-docs>`_


rey-cli
-------

This is the command-line interface for REY. It's the recommended way of using all the different projects and tools, as it facilitates managing different development environments and using the supplied docker images.

* Github repository: `reputation-network/rey-cli <http://github.com/reputation-network/rey-cli>`_


rey-cmd
-------

This project is a set of scripts. They are the one-off commands which are executed from ``rey-cli`` to deploy a manifest file, fund a channel, run a REY app, etc. They are stored in a docker image to reduce the dependencies when running them from ``rey-cli``.

* Github repository: `reputation-network/rey-cmd <http://github.com/reputation-network/rey-cmd>`_
* Docker image: `reputationnetwork/cmd <https://hub.docker.com/r/reputationnetwork/cmd>`_


rey-sdk-js
----------

This project is the JavaScript SDK for REY. It enables performing all of REY's actions, such as running apps or signing permissions, using a JavaScript interface.

* Github repository: `reputation-network/rey-sdk-js <http://github.com/reputation-network/rey-sdk-js>`_


rey-gatekeeper
--------------

This project is the :ref:`gatekeeper <gatekeepr>`, a REY app proxy that deals with the REY protocol to simplify building REY apps. The gatekeeper is used in the :ref:`tutorial <tutorial>` section.

* Github repository: `reputation-network/rey-gatekeeper <http://github.com/reputation-network/rey-gatekeeper>`_
* Docker image: `reputationnetwork/gatekeeper <https://hub.docker.com/r/reputationnetwork/gatekeeper>`_


rey-contracts
-------------

This project contains the smart contracts for the registry and the transaction tracking. They are deployed in a local blockchain node that is stored in a docker image. This simplifies development, as this blockchain node can be easily instantiated using ``rey-cli dev node``.

* Github repository: `reputation-network/rey-contracts <http://github.com/reputation-network/rey-contracts>`_
* Docker image: `reputationnetwork/node <https://hub.docker.com/r/reputationnetwork/node>`_
