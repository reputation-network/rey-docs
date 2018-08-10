.. index:: ! permissions
.. _permissions:

Permissions
===========

.. _write_permission_set:

Write permission set
--------------------

A sample write permission set looks as follows:

.. code:: javascript

  {
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "writePermissions": [ ... ]
    "signature": {
      "r": "0xd442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "0xee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "0x1d"
    }
  }

The individual :ref:`write permissions <write_permission>` have been ommitted from the JSON object above. Each of the write permission should have a different subject address equal to each of the
`derived child keys <https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#Extended_keys>`_ from the write permission set's subject address (which is the subject's main public key).

.. _write_permission:

Write permission
----------------

A sample write permission looks as follows:

.. code:: javascript

  {
    "writer": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "signature": {
      "r": "0xd442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "0xee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "0x1d"
    }
  }

.. _read_permission:

Read permission
---------------

A sample read permission looks as follows:

.. code:: javascript

  {
    "reader": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "source": "0x88032398beab20017e61064af3c7c8bd38f4c968",
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "expiration": "2314320941",
    "signature": {
      "r": "0xd442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "0xee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "0x1d"
    }
  }
