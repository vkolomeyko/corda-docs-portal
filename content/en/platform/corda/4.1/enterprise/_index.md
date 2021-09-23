---
aliases:
- /releases/4.1/index.html
date: '2020-01-08T09:59:25Z'
menu:
  versions:
    weight: 35
project: corda
section_menu: corda-enterprise-4-1
title: Corda Enterprise 4.1
version: '4.1'
cordaType: 'Enterprise'
---

# Introduction to Corda

A Corda Network is a peer-to-peer network of [Nodes](corda-nodes-index.md), each representing a party on the network.
These Nodes run Corda applications [(CorDapps)](building-a-cordapp-index.md), and transact between Nodes using public or
confidential identities.

When one or more Nodes are involved in a transaction, the transaction must be notarised. [Notaries](running-a-notary.md) are a specialised type
of Node that provides uniqueness consensus by attesting that, for a given transaction, it has not already signed other
transactions that consumes any of the proposed transaction’s input states.

For Corda Enterprise 4.1 release notes, see the [Corda Enterprise 4.1 Release Notes](release-notes-enterprise.md).

For Corda (open source) 4.1 release notes, see the [Corda Release Notes](../../corda-os/4.1/release-notes.md).

## Corda Enterprise

Corda Enterprise is a commercial edition of the Corda platform, specifically optimised to meet the privacy, security and
throughput demands of modern day business. Corda Enterprise is interoperable and compatible with Corda open source and
is designed for organisations with exacting requirements around quality of service and the network infrastructure in
which they operate.

Corda Enterprise contains all the core Corda functionality, but also includes the [Corda Firewall](corda-firewall-component.md),
support for high-availability Node and Notary deployments, and compatibility with hardware security modules [(HSMs)](cryptoservice-configuration.md).