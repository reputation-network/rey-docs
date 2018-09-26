.. index:: ! registry
.. _registry:

Registry
========

The registry enables apps to specify where to find their manifest file that shows what the app is about and how to run it. The registry is a smart contract whose data will typically need to be updated only once to specify the manifest URL.

.. _manifest:

Manifest file
-------------

A sample manifest file looks as follows:

.. code:: javascript

  { "version": "1.0",
    "name": "Super Scoring App",
    "description": "This app provides great risk scores",
    "homepage_url": "http://example.com/super-scoring-app",
    "picture_url": "http://example.com/logo-square.png",
    "address": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "app_url": "http://api.example.com/super-scoring-app/data",
    "app_reward": 10,
    "app_schema": "{\"score\":5}",
    "app_dependencies": [
      "0x31bb9d47bc8bf6422ff7dcd2ff53bc90f8f7b009",
      "0x88032398beab20017e61064af3c7c8bd38f4c968"
    ]
  }

The manifest file must be serialized as JSON and has the following fields:

- Version: a version number of the app.

- Name: a human-readable name for the actor, which will be used in permission dialogs.

- Description: a human-readable description for the actor, which will be used in permission dialogs along with the name.

- Homepage URL: a URL where a user can get more information about the app, opt-in, and opt-out.

- Picture URL: a square picture that represents the actor, which will be used in permission dialogs along with the name and description.

- Address: the public key of the app.

- Verifier URL: a URL where to expect verifier functionality. If the actor is not a verifier, it can be omitted.

- Verifier fee: the expected fee for the verification service, in parts per million. If omitted, zero fee is assumed.

- App URL: a URL where to expect app functionality. If the actor is not an app, it can be omitted.

- App reward: the expected reward for the app's service. If omitted, zero reward is assumed.

- App schema: the schema to be used by the app when returning its output.

- App dependencies: a list of address for whom the app needs read permissions to properly run.
