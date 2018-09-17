.. index:: ! gatekeepr
.. _gatekeepr:

Gatekeeper
==========

REY's `gatekeeper <http://github.com/reputation-network/rey-gatekeeper>`_ is a proxy to a target server that provides the actual functionality of a REY app. Using the gatekeeper is the easiest way to publish an app in REY.

The main idea is to abstract app developers from REY's protocol to just focus on the app's business logic.

Take, for example, an app that returns purchase data about people in an e-commerce. The gatekeeper would be the server that is available to the outer world. It'd process permissions and perform cashouts, and will forward valid requests to the target server that implements the actual business logic and returns the output.

Installation
------------

To launch gatekeeper, simply use ``rey-cli``:

.. code::

  $ rey-cli dev gatekeeper -e TARGET=http://127.0.0.1:9400/data -e MANIFEST=http://127.0.0.1:9400/manifest -e APP_ADDRESS=0x88032398beab20017e61064af3c7c8bd38f4c968

Configuration
-------------

The gatekeeper is configured using the following environment variables:

- ``APP_ADDRESS``: The public key of the app. It's expected that the private key is stored in the blockchain node.
- ``TARGET``: Specifies the endpoint URL of the target server (i.e., the server that provides the actual implementation of the app). It must include any needed path or basic authentication credentials for the server.
- ``MANIFEST``: Specifies where to find the manifest of the app. It must include any needed path or basic authentication credentials for the server.
- ``DEPENDENCIES``: Comma-separated list of any other apps (as public keys) that might be used by this app. With this, the gatekeeper would reject those calls that do not include read permissions to use these apps.
