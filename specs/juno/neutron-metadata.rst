..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==========================================
Allow association of meta data for core resources  
==========================================

Include the URL of your launchpad blueprint:

https://blueprints.launchpad.net/neutron/+spec/example

This blueprint proposes to add a new attribute to the core resources -
networks, subnets and ports.  The attribute, metadata, is intended to save
meta-data for the resource as per the providers requirements.

Problem description
===================
Currently the core resources do not have any associated metadata but there
are many instances where the orchestration layer could use the information
to make intelligent decision.  The metadata attribute can have any value
that is deemed necessary to operate the network.  E.g. an operator may
want to access networks with certain properties which can be encoded in
the metadata field.

It is proposed below that a metadata attribute is added to the core 
resources which contains the serialized JSON data.  The meta-data is
interpreted by users and possibly orchestration layer and not by the plugins.
It is opaque to the plugins.

Proposed change
===============

We propose that the following is updated:

* neutron/api/v2/attributes.py

  RESOURCE_ATTRIBUTE_MAP = {
      NETWORKS: {
          'id': {'allow_post': False, 'allow_put': False,
          ...
          'metadata' : {...details ...} 
      },

      SUBNETS: {
          ...
          'metadata' : {...details ...} 
      },


      PORTS: {
          ...
          'metadata' : {...details ...} 
      },

* migration
  
  add an alembic migration script to populate the field with default (empty value)

* neutronclient

  update client to allow creating and updating the values in metadata.

Alternatives
------------

Other ways to solve the problem is to extend the attributes of the resources
and have a separate table to store the metadata.  However, this is something
that a lot of operators are doing and it should not be required to write 
an extension for such a basic funcationality.  Having it in the core also
avoids duplcation.  


Data model impact
-----------------

This is going to require updating the schema for networks and ports.  An alembic
migration script will populate the new field with a default value.  This is an
optional attribute.

REST API impact
---------------

No new APIs are needed.  The following attribute is added to the networks
and ports table. So the APIs for the resources will be impacted to
accommodate the changes in attribute map. 

+----------+-------+---------+---------+------------+------------------+
|Attribute |Type   |Access   |Default  |Validation/ |Description       |
|Name      |       |         |Value    |Conversion  |                  |
+==========+=======+=========+=========+============+==================+
|metadata  |string |RW, all  |''       |N/A         |operator-specific |
|          |       |         |         |            |meta-data         |
+----------+-------+---------+---------+------------+------------------+


* Example use case including typical API samples for both data supplied
  by the caller and the response


Security impact
---------------

N/A

Notifications impact
--------------------

N/A

Other end user impact
---------------------

N/A

Performance Impact
------------------

None

Other deployer impact
---------------------

None

Developer impact
----------------

None


Implementation
==============

Assignee(s)
-----------

Primary assignee:
  <launchpad-id or None>

Other contributors:
  <launchpad-id or None>

Work Items
----------

Work items or tasks -- break the feature up into the things that need to be
done to implement it. Those parts might end up being done by different people,
but we're mostly trying to understand the timeline for implementation.


Dependencies
============

None


Testing
=======

New unit tests will be written and existing ones will be modified to make
sure the addition of new attribute is well tested.


Documentation Impact
====================

A new attribute is added to core resources - network, subnet and port.  So the
documentation should be updated to indicate the existence of a new attribute,
same impace on neutronclient.


References
==========

None
