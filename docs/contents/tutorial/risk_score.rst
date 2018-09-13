.. index:: ! risk_score
.. _risk_score:

Risk score app
==============

The sample risk score app is similar to the hello world one but has an app dependency. It gathers data from one app to build a risk score out of them.

In general, a risk score would involve some computation out several data sources. In this case, we'll simply extract the magic number generated in the :ref:`hello world example <hello_world>` and multiply it by 2. The steps that are involved are:

- Start a blockchain node.
- Run hello world app.
- Implement the app's logic in a service.
- Start the gatekeeper to make the service available in a secure way.
- Publish the app in the registry.


Requirements
------------

Make sure to have followed the :ref:`installation <installation>` instructions.

Also, you should have already built the :ref:`hello world app <hello_world>`, as it is used as a dependency in the risk score app.


Start a blockchain node
-----------------------

A blockchain node is required to interact with REY's smart contract. For development purposes, we'll use a private blockchain node to avoid worrying about gas costs when interacting with the smart contract.

A docker image with REY's registry and smart contract already published is available, so simply run:

.. code::

  $ rey-cli dev node

This will launch the :ref:`development environment <development>` blockchain node with a port open to RPC connections. So far, we just need to know that the accounts ``0x88032398beab20017e61064af3c7c8bd38f4c968`` and ``0x6d644c57247de51da20797f14dceedfbc4ef6561`` have funds and are available to be used. As said, the smart contracts are already deployed and have the following addresses:

- Main smart contract address: ``0x76C19376b275A5d77858c6F6d5322311eEb92cf5``

- Registry address: ``0x556ED3bEaF6b3dDCb1562d3F30f79bF86fFC05B9``

Run hello world app
-------------------

We need the hello world app up and running. In general, each app would be run by their own company, so each would use different blockchain nodes (hosted in each company's premises), with their own accounts.

However, for this example, we'll have both apps (hello world and risk score) share the same blockchain node. This means that, in order to start the hello world app, we'll just need to launch its own gatekeeper and its ``server.rb`` file.

To run hello world's main service:

.. code::

  $ ruby helloworld.rb

And to run hello world's gatekeeper:

.. code::

  $ rey-cli dev gatekeeper -e TARGET=http://user:password@localhost:8080/data MANIFEST=http://localhost:8080/manifest APP_ADDRESS=0x88032398beab20017e61064af3c7c8bd38f4c968

App logic
---------

We'll build a service in Node.js that makes some computation out of the magic number that the Hello World app provides. Let's write a file called ``server.js``:

.. code:: javascript

  const express = require('express');
  const auth = require('http-auth');
  const app = express();

  const [username, password] = (process.env.AUTHENTICATION || 'user:password').split(':');
  const manifest = {
    "version": "1.0",
    "name": "Risk Score",
    "description": "Returns a risk score based on your data",
    "address": "0x6d644c57247de51da20797f14dceedfbc4ef6561",
    "app_url": "http://localhost:8083/data",
    "app_reward": 0,
    "app_dependencies": ['0x88032398beab20017e61064af3c7c8bd38f4c968'],
  };
  const basic = auth.basic({ realm: "Web." }, (u, p, callback) => callback(u === username && p === password));
  const wrap = fn => (...args) => fn(...args).catch(args[2]);

  app.get('/manifest', auth.connect(basic), (req, res) => res.send(manifest));

  app.get('/data', auth.connect(basic), wrap(async (req, res) => {
    const value = await parseInt(Math.random() * 10000);
    res.send({ "data": value * 2 });
  }));

  app.listen(8082);


The previous script requires `Node.JS <http://nodejs.org>`_ and its dependencies can be installed with (``npm install express http-auth --save``). It can be run with:

.. code::

  $ node server.js

This will launch a server that listens on port 8082 and has two endpoints:

- ``/manifest``: Returns the following manifest file that is used to provide basic information about the app:

.. code:: javascript

  {
    "version": "1.0",
    "name": "Risk Score",
    "description": "Returns a risk score based on your data",
    "address": "0x6d644c57247de51da20797f14dceedfbc4ef6561",
    "app_url": "http://localhost:8083/data",
    "app_reward": 0,
    "app_dependencies": ['0x88032398beab20017e61064af3c7c8bd38f4c968']
  }

As you can see, we're using the address ``0x6d644c57247de51da20797f14dceedfbc4ef6561`` to identify the app. This address was mentioned before, as it's one of the accounts that are funded and ready to use in the development blockchain node, and it's different from hello world's address. Also, note that the dependency of hello world app is stated by including hello world app's address in the ``app_dependencies`` list.

- ``/data``: Returns the actual output of the app.

Launch gatekeeper
-----------------

REY's Gatekeeper is a proxy that implements most of REY's protocol to facilitate building REY apps. The Ruby service built previously does not have any kind of permission check, as this task is delegated to REY's Gatekeeper, which can fulfil the task with little configuration.

To run the gatekeeper, simply use:

.. code::

  $ rey-cli dev gatekeeper -p 8083 -e TARGET=http://user:password@localhost:8082/data -e MANIFEST=http://user:password@localhost:8082/manifest -e APP_ADDRESS=0x6d644c57247de51da20797f14dceedfbc4ef6561

It requires some parameters to specify where to find the manifest, the app's endpoint, and the app's address. Note that this is risk score's gatekeeper, so we need to specify a port to prevent overlapping with hello world's gatekeeper port.

Publishing the app
------------------

The app needs to be published in REY's registry so that others can find it just by its public key. The registry associates a public key with its manifest URL.

You can publish the app's manifest with:

.. code::

  $ rey-cli dev cmd publish-manifest 0x6d644c57247de51da20797f14dceedfbc4ef6561 http://localhost:8083/manifest
