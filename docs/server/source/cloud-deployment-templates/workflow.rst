Overview
========

This page summarizes the steps *we* go through
to set up a production BigchainDB cluster.
We are constantly improving them.
You can modify them to suit your needs.


Things the Managing Organization Must Do First
----------------------------------------------


1. Set Up a Self-Signed Certificate Authority
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We use SSL/TLS and self-signed certificates
for MongoDB authentication (and message encryption).
The certificates are signed by the organization managing the cluster.
If your organization already has a process
for signing certificates
(i.e. an internal self-signed certificate authority [CA]),
then you can skip this step.
Otherwise, your organization must
:ref:`set up its own self-signed certificate authority <How to Set Up a Self-Signed Certificate Authority>`.


2. Register a Domain and Get an SSL Certificate for It
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The BigchainDB APIs (HTTP API and WebSocket API) should be served using TLS,
so the organization running the cluster
should choose an FQDN for their API (e.g. api.organization-x.com),
register the domain name,
and buy an SSL/TLS certificate for the FQDN.


Things Each Node Operator Must Do
---------------------------------

☐ Decide on, or find out some names of the things in your node:

#. Name of the MongoDB instance (``mdb-instance-*``)
#. Name of the BigchainDB instance (``bdb-instance-*``)
#. Name of the NGINX instance (``ngx-instance-*``)
#. Name of the MongoDB monitoring agent instance (``mdb-mon-instance-*``)
#. Name of the MongoDB backup agent instance (``mdb-bak-instance-*``)


☐ Every node in a BigchainDB cluster needs its own
BigchainDB keypair (i.e. a public key and corresponding private key).
You can generate a BigchainDB keypair for your node, for example,
using the `BigchainDB Python Driver <http://docs.bigchaindb.com/projects/py-driver/en/latest/index.html>`_.

.. code:: python
        
   from bigchaindb_driver.crypto import generate_keypair
   print(generate_keypair())


☐ Share your BigchaindB *public* key with all the other nodes
in the BigchainDB cluster.
Don't share your private key.


☐ Get the BigchainDB public keys of all the other nodes in the cluster.
That list of public keys is known as the BigchainDB "keyring."


☐ Ask the managing organization
for the FQDN used to serve the BigchainDB APIs
and for a copy of the associated SSL/TLS certificate.


☐ If the cluster uses 3scale for API authentication, monitoring and billing,
you must ask the managing organization for all relevant 3scale credentials.


☐ If the cluster uses MongoDB Cloud Manager for monitoring and backup,
you must ask the managing organization for the ``Agent Api Key``.


☐ Generate four keys and corresponding certificate signing requests (CSRs):

#. Server Certificate (a.k.a. Member Certificate) for the MongoDB instance
#. Client Certificate for BigchainDB Server to identify itself to MongoDB
#. Client Certificate for MongoDB Monitoring Agent to identify itself to MongoDB
#. Client Certificate for MongoDB Backup Agent to identify itself to MongoDB
#. CRL for the infrastructure to not accept revoked certificates.

Ask the managing organization to use its self-signed CA to sign those certificates.

For help, see the pages:

* :ref:`How to Generate a Server Certificate for MongoDB`
* :ref:`How to Generate a Client Certificate for MongoDB`


☐ :doc:`Deploy a Kubernetes cluster on Azure <template-kubernetes-azure>`.


☐ Create the Kubernetes Configuration for this node. 
We will use Kubernetes ConfigMaps and Secrets to hold all the information
gathered above.


☐ Deploy your BigchainDB node on your Kubernetes cluster.

TODO: Links to instructions for first-node-in-cluster or second-or-later-node-in-cluster
