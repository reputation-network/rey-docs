.. index:: ! introduction

Introduction
============

The Reputation Network (or simply REY) enables safe, traceable personal data processing for a wide range of applications such as alternative risk scoring.


Interaction
-----------

As a user, making use of REY implies having control over your personal data when enabling third parties make use of it.

It differs from other approaches in that it respects the federated architecture of the Internet, where data are scattered across different, heterogeneous sources. Blockchain is used as decentralized ledger for keeping track of data usage.

In order to use a REY app, a user needs to follow the next steps:

- Download `Metamask <https://metamask.io>`_ to create a decentralized identity, which simply consists of a pair of cryptographic keys.

- Submit write permissions to trusted websites where you have personal data. This will let them share data about you in REY to those readers you proactively accept. To achieve this, you just need to browse REY-enabled sites that would guide you and request your browser extension to build and sign the write permission using your private key.

- Then you're ready to use a REY app by providing the read permissions it could require. For example, the "Your entropy" sample app provides a measure of the amount of information you have in your Traity profile. To make it work, it requires your read permission to use the app, and your read permission for the app to read your Traity data.

- You can browse your exchanges of data at `reputation.network <https://reputation.network>`_. That's a blockchain explorer for REY that shows how, when, and by whom were your permissions used.

.. note::
    There is a `tech paper that discusses the Reputation Network design details <https://reputation.network/papers/tech>`_ in depth. For a conceptual description, the former paper is the reference to check.

Sample apps
-----------

In the :ref:`tutorial` section you can find the quick start guide to create your first Reputation Network app.

Some sample apps have been already built as demo for developers. They use Traity as data source and do a computation on its data. They have been deployed and ready to use without cost:

- Your risk score
- Your Traity reputation
- Your popularity
- Your pics
- Your entropy
- Your grammar score
- Your life expectancy
- Your public key score
- Your horoscope
- Your biorhythms
- Your birthdate
- Adult or not?
- Your interests
