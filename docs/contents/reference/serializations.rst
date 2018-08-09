.. index:: ! serializations
.. _serializations:

Serializations
==============

There are different ways of representing objects in REY.

Take the following read permission as example:

.. code:: javascript

  {
    "reader": "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "source": "0x88032398beab20017e61064af3c7c8bd38f4c968",
    "subject": "0x6224d471b8590de463d27b067174b566b4b0b041",
    "expiration": "2314320941",
    "signature": {
      "r": "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "s": "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "v": "1d"
    }
  }

When submitting it to the smart contract, it's internally transformed to an array format instead of using objects:

.. code:: javascript

  [
    "0xc25b4ff9eb6f52392eef1e103daacc7519795f01",
    "0x88032398beab20017e61064af3c7c8bd38f4c968",
    "0x6224d471b8590de463d27b067174b566b4b0b041",
    "2314320941",
    [
      "d442661375322973148b808f4bcb3fedffab5d5855acf2e7e2c37745676f5a23",
      "ee1026f612fbf69e0195e1cef5d29a3fe65f7a2575031bb1818150b090df509c",
      "1d"
    ]
  ]

Additionally, when submitting JSON objects, JWT is used as standard for HTTP services, while ABI is used when submitting data to the smart contract.

As a result, there are two possible serializations that are used within REY:

- JSON objects with key and values, serialized as a non-signed JWT. This is used in traditional HTTP services.
- JSON arrays with only values, serialized as tightly packed ABI data. This is used when submitting data to the smart contract.

Note that big numbers are always expressed as strings to prevent precision issues in JavaScript.
