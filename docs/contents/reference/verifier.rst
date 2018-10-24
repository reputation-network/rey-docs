.. index:: ! verifier
.. _verifier:

Verifier
========

REY's verifier is a proxy to REY apps that is an important part of REY's architecture.

Detailed information about it can be found at REY's `tech paper <https://reputation.network/papers/tech>`_. In essence, the verifier acts as a proxy to certify that the data transference between client and app conforms to a schema that has been accepted by the subject when signing the read permission. Thus, the verifier prevents data leaks in the system while being unable to view the actual data, which is transmitted in an encrypted way from REY apps to clients.

From the development point of view, a verifier needs to be running so that apps are queried through it by clients.

Running a verifier
------------------

To launch a verifier, simply use ``rey-cli``:

.. code::

  $ rey-cli dev verifier -e VERIFIER_ADDRESS=0x44f1d336e4fdf189d2dadd963763883582c45312

This will launch a verifier at ``localhost:8082`` with the given blockchain address. However, this verifier needs to be published in the registry, so we'll need to run:

.. code::

  $ rey-cli dev cmd publish-manifest 0x44f1d336e4fdf189d2dadd963763883582c45312 http://localhost:8082/manifest

Once that's done, apps can be queried through this verifier with:

.. code::

  $ rey-cli dev cmd read-app <APP_ADDRESS> <SUBJECT_ADDRESS> 0x44f1d336e4fdf189d2dadd963763883582c45312

Configuration
-------------

The verifier can be configured using the following environment variables:

- ``VERIFIER_ADDRESS``: The address of this verifier instance.
- ``LOG_LEVEL``: Minimum log level, defaults to ``info``.
- ``SECURED_PATH``: Path where the verifier's functionality is exposed, defaults to ``/data``.
- ``VERIFIER_FEE``: The fee (in parts per million) that the verifier will get out of each cashed out transaction.
- ``MANIFEST_PATH``: The path where the manifest file will be published. Defaults to ``/manifest``.
- ``MANIFEST_VERSION``: The manifest's version.
- ``MANIFEST_NAME``: The manifest's public name.
- ``MANIFEST_DESCRIPTION``: The manifest's public description.
- ``MANIFEST_HOMEPAGE_URL``: The manifest's home page URL.
- ``MANIFEST_PICTURE_URL``:  The manifest's public picture URL.
- ``VERIFIER_URL``:  The manifest's verifier URL. Defaults to ``http://localhost:8082/data``.
- ``VERIFIER_ACCOUNT_PASSWORD``: The verifier account's password that is used in the blockchain node. The account is used to sign transactions for the target apps.
