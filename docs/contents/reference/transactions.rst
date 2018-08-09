.. index:: ! transactions
.. _transactions:

Transactions
============

A transaction is simply a :ref:`request <request>` and a :ref:`proof <proof>`, signed by a verifier, and looks as follows:

.. code:: javascript

  {
    "writer": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "signature": {
      "r": "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "1d"
    }
  }

.. _request:

Request
-------

A request consists of a :ref:`session <session>` and a :ref:`read permission <read_permission>` and looks as follows:

.. code:: javascript

  {
    "readPermission": { ... },
    "session": { ... },
    "signature": {
      "r": "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "1d"
    }
  }

A request must be signed by the actor making the call to the REY app specified as source in the included read permission.

.. _proof:

Proof
-----

A proof consists of a :ref:`session <session>` and a :ref:`write permission <write_permission>` and looks as follows:

.. code:: javascript

  {
    "writePermission": { ... },
    "session": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "signature": {
      "r": "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "1d"
    }
  }

A proof must be signed by the write permission's source.

.. _session:

Session
-------

A sample session looks as follows:

.. code:: javascript

  {
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "verifier": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "fee": "10", /* in parts per million */
    "nonce": "75482967549",
    "signature": {
      "r": "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "1d"
    }
  }
