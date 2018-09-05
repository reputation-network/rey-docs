.. index:: ! hello_world
.. _hello_world:

Hello world app
===============

Our first REY app will simply return a magic number that is calculated out of the subject's public key. The steps involved are:

- Start a blockchain node.
- Implement the app's logic in a service.
- Start the gatekeeper to make the service available in a secure way.
- Publish the app in the registry.

Requirements
------------

Make sure to have `Docker <http://docker.com>`_ properly installed.


Start a blockchain node
-----------------------

A blockchain node is required to interact with REY's smart contract. For development purposes, we'll use a private blockchain node to avoid worrying about gas costs when interacting with the smart contract.

A docker image with REY's registry and smart contract already published is available, so simply run:

.. code::

  $ docker run reputationnetwork:node -p 8545:8545

This will launch the geth node with an open port to connect via RPC and interact with it. So far, we just need to know that the account with address ``0x88032398beab20017e61064af3c7c8bd38f4c968`` has funds and is available to be used. As said, the smart contracts are already deployed:

- Main smart contract address: ``0x6224d471b8590de463d27b067174b566b4b0b041``

- Registry address: ``0xc25b4ff9eb6f52392eef1e103daacc7519795f01``


App logic
---------

We'll build a simple Ruby service that computes the magic number given a subject's public key:

.. code:: ruby

  require 'base64'
  require 'json'
  require 'webrick'

  manifest = JSON.parse File.read('./rey-manifest.json')
  app_seed = manifest['address'].to_i(16) + (ENV['SECRET_SALT'] || '').to_i(16)

  def parse_subject_header(req)
      value = req.header['x-permission-subject'].first || ""
      Base64.decode64(value).to_i(16)
  end

  WEBrick::HTTPServer.new(:Port => 8080).tap {|srv|
    srv.mount_proc('/manifest') do |req, res|
      res.body = JSON.fast_generate(manifest)
    end
    srv.mount_proc('/data') do |req, res|
      subject = parse_subject_header(req)
      res.body = JSON.fast_generate(data: Random.new(app_seed + subject).rand)
    end
  }.start

The service has two endpoints:

- ``/manifest``: Returns the following manifest file that is used to provide basic information about the app:

.. code:: javascript

  {
    "version": "1.0",
    "name": "Hello World",
    "description": "Returns a magic number",
    "address": "",
    "app_url": "http://localhost:8081/data",
    "app_reward": 0,
    "app_dependencies": []
  }

- ``/data``: Returns the actual output of the app (a magic number).

As you can see, there's no kind of permission check in the service. This is left to REY's Gatekeeper.

Launch gatekeeper
-----------------

REY's Gatekeeper is a proxy that implements most of REY's protocol to facilitate building REY apps. The previous service does not have any kind of permission check, as this task is delegated to REY's Gatekeeper, which can fulfil the task with little configuration.

To run the gatekeeper, simply use:

.. code::

  $ docker run -p 10000:8081 -e TARGET=http://localhost:8080/data MANIFEST=http://localhost:8080/manifest NODE=http://localhost:8545 reputationnetwork:gatekeeper

It requires some parameters to specify where to find the manifest, the app's endpoint, and the blockchain node. It's also required to redirect the port 10000 to the desired port that will be used to publish the app.

Notice that the Ruby service should not be publicly accessible. It does not make any kind of access check, so only the gatekeeper should be publicly accessible. This is why the app's manifest file has port number 8081 as ``app_url``: app clients should query the gatekeeper, while the Ruby service should not be publicly reachable.

Publishing the app
------------------

The app needs to be published in REY's registry so that others can find it just by its public key. The registry associates a public key with its manifest URL.

