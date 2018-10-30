.. index:: ! schema
.. _schema:

Schema
======

A schema is the expected structure of an app's output. REY apps return JSON output which, by definition, is a combination of arrays and key-value objects.

An app's schema is present in the app's :ref:`manifest file <manifest>` so that the subject commits themselves to a specific output of the app when building a read permission.

Schemas are checked by verifiers when data transfers happen between an app and its client. The verifier validates that the output's structure matches the one the read permission was built for.

Examples
--------

Defining a schema for an app is quite simple. Let's assume we're building an app that returns the favourite city of a person:

.. code:: javascript

  /* App sample output */
  { "city": "Madrid" }

The schema of this output will just be a JSON object with ``city`` as a key, and the maximum number of bytes of its value. Different cities have different lengths, so with this we're setting up a cap, so that, e.g., a JPG image can't be serialized and disguised as a city. The objective is to avoid unsolicited data leaks in an app's output. Thus, the schema will be:

.. code:: javascript

  /* App schema */
  { "city": 30 }

With this, we've set a limit of 30 bytes for the value of ``city`` key. As the string ``"Madrid"`` has a JSON-stringified length of 8 bytes, we're within the 30 bytes limit, which means the app's output is valid for this schema.

Let's consider a more complex example. Now the app returns more detailed information about the city, incluing the country to disambiguate between, e.g., Madrid, Spain, and Madrid, USA.

.. code:: javascript

  /* App sample output */
  { "city": { "name": "Madrid", "country": "Spain" } }

For this type of output, the same approach would apply. We'll assume countries will have a maximum JSON-stringified length of 30 bytes. The app schema would be:

.. code:: javascript

  /* App schema */
  { "city": { "name": 30, "country": 30 } }

Finally, let's assume an app returns a list of up to 10 favourite cities. One sample output would be:

.. code:: javascript

  /* App sample output */
  { "cities": [ { "name": "Madrid", "country": "Spain" },
                { "name": "Valencia", "country": "Spain" },
                { "name": "San Francisco", "country": "USA" } ] }

To build the schema, we'll replace values with the maximum expected JSON-stringified length again. For the array, two elements are used: the first one is the expected schema of each item, and the last one is the maximum length of the array. As said, we'll only support a maximum of 10 favourite cities, so the schema is as follows:

.. code:: javascript

  /* App schema */
  { "cities": [ { "name": 30, "country": 30 }, 10 ] }

It can be observed that there are no specific type checks. The objective of schema checks is not doing type checks, but to prevent data leaks. Therefore, the amount of allowed data is what only matters, as both numbers or strings can be disguised as different data types by using different encodings.

An advanced user would be able to observe that, given the app schema shown before, the app in question would be able to return a maximum of 10 x (30 + 30) = 600 bytes of data (including an overhead of double quotes). This measure is useful for the user to weight the amount of trust in the app and the amount of risk in the schema in order to decide whether to use the app or not.

Syntax
------

More formally, the syntax used to define a schema is shown here in Backus-Naur Form (BNF):

.. code::

  <schema>  ::= <object> | <array> | <number>;
  <object>  ::= '{' '}' | '{' <members> '}';
  <members> ::= <pair> | <pair> ',' <members>;
  <pair>    ::= '"' <string> '"' ':' <number>;
  <array>   ::= '[' <schema> ',' <number> ']';

Verification function
---------------------

The function used by verifiers to check that an app's output fits its schema is the next one:

.. code::

  function checkSchema(data, schema) {
    if (typeof(schema) == 'number') {
      if (JSON.stringify(data).length > schema) {
        return false;
      }
    } else if (Array.isArray(schema)) {
      if (!Array.isArray(data)) {
        return false;
      }
      if (data.length > schema[schema.length - 1]) {
        return false;
      }
      for (let item of data) {
        if (!checkSchema(item, schema[0])) {
          return false;
        }
      }
    } else {
      if (typeof(data) != 'object') {
        return false;
      }
      for (let key in data) {
        if (Object.keys(schema).indexOf(key) == -1) {
          return false;
        }
        if (!checkSchema(data[key], schema[key])) {
          return false;
        }
      }
    }
    return true;
  };

Encryption
----------

Verifiers work on encrypted data. This means that they cannot just apply all of the above on raw data as is shown.

Let's consider again the following app output:

.. code:: javascript

  /* App sample output */
  { "city": "Madrid" }

To know what a verifier will be able to see, let's encrypt it using `rey-sdk-js <http://github.com/reputation-network/rey-sdk-js>`_ and some random key pair:

.. code::

  $ node
  > const REY = require("rey-sdk");
  > let key = new REY.Utils.EncryptionKey()
  > key.createPair();
  > key.encrypt({ "city": "Madrid" });
  { city: 'cFwfPaP3E/4tcryywWYEDN7go+pi1uTpA7jy7clI17KKO/nO0YuZ5vS3i7Ea9n/y3LOF4cajYQOAQt/lBwDMsA==' }

As you can see, the string ``"Madrid"`` becomes ``"cFwfPaP3E/4tcryywWYEDN7go+pi1uTpA7jy7clI17KKO/nO0YuZ5vS3i7Ea9n/y3LOF4cajYQOAQt/lBwDMsA=="``. This is an encrypted, Base64-encoded version of the string "Madrid". Trying with a longer string produces a similar result:

.. code::

  > key.encrypt({ "city": "San Francisco" });
  { city: 'Dmr8kPZYYO9k3pQBhSRbp64bP2+fuOET7HcDONjXecFzvc9s77C6P2H/xpLrww9ucjyDkH+YKljjepqor28ynQ==' }

The string ``"San Francisco"`` produces another Base64 string which has the same length as the previous one. As ``"San Francisco"`` is longer than ``"Madrid"``, this means that the encryption algorithm hides the real length of the unencrypted data.

The ``pkcs1`` encryption algorithm being used provides an output whose length is a multiple of 64 bytes for every 22 bytes, which, after converting it to Base64, becomes `even longer <https://en.wikipedia.org/wiki/Base64>`_.

In order to estimate the length of the original, unencrypted data, the verifier reverse engineers the above formula. This lets verifiers estimate how many minimum bytes are actually being sent and detect those cases where there's a clear excess of information, compared to the schema.

As verifiers reverse engineer the unencrypted data length, encryption doesn't change the way schemas should be defined. Nevertheless, it's important to notice that a verifier cannot really make a difference between an unencrypted length of 14 and a length of 16 (as both would produce Base64 strings that are equally long). However, verifiers would spot a leak if the unencrypted length being transmitted is, e.g., 80, as it would produce a much longer encrypted string.
