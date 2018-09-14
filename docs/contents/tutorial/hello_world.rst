.. index:: ! hello_world
.. _hello_world:

Hello world app
===============

Our first REY app will simply return a magic number that is calculated out of the subject's public key. The steps that are involved are:

- Start a blockchain node.
- Implement the app's logic in a service.
- Start the gatekeeper to make the service available in a secure way.
- Publish the app in the registry.


Requirements
------------

Make sure to have followed the :ref:`installation <installation>` instructions.


Start a blockchain node
-----------------------

A blockchain node is required to interact with REY's smart contract. For development purposes, we'll use a private blockchain node to avoid worrying about gas costs when interacting with the smart contract.

A docker image with REY's registry and smart contract already published is available, so simply run:

.. code::

  $ rey-cli dev node

This will launch the :ref:`development environment <development>` blockchain node with a port open to RPC connections. So far, we just need to know that the account with address ``0x88032398beab20017e61064af3c7c8bd38f4c968`` has funds and is available to be used. As said, the smart contracts are already deployed and have the following addresses:

- Main smart contract address: ``0x76C19376b275A5d77858c6F6d5322311eEb92cf5``

- Registry address: ``0x556ED3bEaF6b3dDCb1562d3F30f79bF86fFC05B9``

App logic
---------

We'll build a simple Ruby service that computes the magic number given a subject's public key. Let's write a file called ``helloworld.rb``:

.. code:: ruby

  require 'sinatra'
  require 'sinatra/json'
  require 'base64'

  set :port, 8080

  USERNAME, PASSWORD = (ENV['AUTHENTICATION'] || 'user:password').split(':')
  MANIFEST = {
    version: '1.0',
    name: 'Hello World',
    description: 'Returns a magic number',
    address: '0x88032398beab20017e61064af3c7c8bd38f4c968',
    app_url: 'http://localhost:8081/data',
    app_reward: 0,
    app_dependencies: []
  }.freeze
  APP_SEED = (MANIFEST[:address] + ENV['SECRET_SALT'].to_s).to_i(16).freeze

  use Rack::Auth::Basic, "Protected Area" do |username, password|
    username == USERNAME && password == PASSWORD
  end

  def parse_subject_header(headers)
    Base64.decode64(headers['HTTP_X_PERMISSION_SUBJECT'] || 'null').gsub(/\A"|"\Z/, '')
  end

  get '/manifest' do
    json MANIFEST
  end

  get '/data' do
    subject_seed = parse_subject_header(request.env).to_i(16)
    json data: Random.new(APP_SEED + subject_seed).rand
  end

The previous script requires the `Ruby language <http://ruby-lang.org>`_ and the `Sinatra <http://sinatrarb.com>`_ library (``gem install sinatra sinatra-contrib``) and can be run with:

.. code::

  $ ruby helloworld.rb

This will launch a server that listens on port 8080 and has two endpoints:

- ``/manifest``: Returns the following manifest file that is used to provide basic information about the app:

.. code:: javascript

  {
    "version": "1.0",
    "name": "Hello World",
    "description": "Returns a magic number",
    "address": "0x88032398beab20017e61064af3c7c8bd38f4c968",
    "app_url": "http://localhost:8081/data",
    "app_reward": 0,
    "app_dependencies": []
  }

As you can see, we're using the address ``0x88032398beab20017e61064af3c7c8bd38f4c968`` to identify the app. This address was mentioned before, as it's one of the accounts that are funded and ready to use in the development blockchain node. A similar process would be required in a production environment (i.e., obtaining an account and funding it).

- ``/data``: Returns the actual output of the app (a magic number).

As you can see, there's no kind of permission check in the service. This is left to REY's Gatekeeper.

Launch gatekeeper
-----------------

REY's Gatekeeper is a proxy that implements most of REY's protocol to facilitate building REY apps. The Ruby service built previously does not have any kind of permission check, as this task is delegated to REY's Gatekeeper, which can fulfil the task with little configuration.

To run the gatekeeper, simply use:

.. code::

  $ rey-cli dev gatekeeper -e TARGET_URL=http://user:password@localhost:8080 -e MANIFEST_URL=http://user:password@localhost:8080/manifest -e APP_ADDRESS=0x88032398beab20017e61064af3c7c8bd38f4c968

It requires some parameters to specify where to find the manifest, the app's endpoint, and the app's address.

Notice that the Ruby service is not publicly accessible (it requires HTTP authentication). It does not make any kind of access check, so only the gatekeeper should be publicly accessible. This is why the app's manifest file has port number 8081 as ``app_url``: app clients should query the gatekeeper, while the Ruby service requires HTTP authentication that only the gatekeeper should know.

Publishing the app
------------------

The app needs to be published in REY's registry so that others can find it just by its public key. The registry associates a public key with its manifest URL.

You can publish the app's manifest with:

.. code::

  $ rey-cli dev cmd publish-manifest 0x88032398beab20017e61064af3c7c8bd38f4c968 http://localhost:8081/manifest

Remember that the manifest URL needs to be gatekeeper's one, as that's the one that does not require authentication. Gatekeeper will proxy the request to the manifest provided by the Ruby service.

Reading the app
---------------

You can now query your app for data, but first you need to have a blockchain identity. For simplicity we will use one of the already available idenitities (also known as accounts) on the development node, whose address is ``0x60cb2204f342dd35bf5a328a03d86dd71d4372ec``.

Also, in order to access the app's data we need an app params structure signed by the account you are querying data for (subject) and by the one querying it (reader), in this example both subject and reader are the same. We can generate a tokenized version of the said app params as follows:

.. code::

  $ rey-cli dev cmd generate-token 0x88032398beab20017e61064af3c7c8bd38f4c968 0x60cb2204f342dd35bf5a328a03d86dd71d4372ec

This token can be later used to read data that the app has about ``0x60cb2204f342dd35bf5a328a03d86dd71d4372ec`` with the following command:

.. code::

  $ rey-cli dev cmd read-app 0x88032398beab20017e61064af3c7c8bd38f4c968 <...App Params Token...?
 
