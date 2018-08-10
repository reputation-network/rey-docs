.. index:: ! gatekeeper
.. _gatekeeper:

Gatekeeper
==========

The gatekeeper is the easiest way to publish an app in REY. It acts as a proxy to a server that provides the actual functionality of an app.

The main idea is to abstract app developers from REY's protocol and just focussing on the app's business logic.

Take, for example, an app returns purchases data about people in an e-commerce. The gatekeeper would be the server that is available to the outer world. It'd process permissions and perform checkouts, and will forward valid requests to the target server that implements the actual business logic and returns the output.

Installation
------------

To launch gatekeeper, simply clone and run the Docker image:

.. code::

  $ git clone git@bitbucket.org:traity/rey-gatekeeper.git
  $ docker build -t rey-gatekeeper .
  $ docker run -it -p 10000:8080 -e TARGET=http://127.0.0.1:9400 --pid=host rey-gatekeeper


Configuration
-------------

The gatekeeper is configured using the following environment variables:

- ``TARGET``: Specifies the endpoint URL of the target server (i.e., the server that provides the actual implementation of the app). It must include any needed path or basic authentication credentials for the server.
- ``DEPENDENCIES``: Comma-separated list of any other apps (as public keys) that might be used by this app. With this, the gatekeeper would reject those calls that do not include read permissions to use these apps.
