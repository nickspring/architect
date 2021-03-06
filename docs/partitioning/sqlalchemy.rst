SQLAlchemy
==========

Requirements
------------

* `SQLAlchemy <http://www.sqlalchemy.org>`_ >= 0.8.0

Configuration
-------------

Define the class which will represent the partitioned table as usual and `create <http://docs.sqlalchemy
.org/en/latest/core/metadata.html#sqlalchemy.schema.MetaData.create_all>`_ a table for it using
``MetaData.create_all()``. After that make the following changes to the class:

1. In the file where the class is defined add the following import statement:

.. code-block:: python

    from architect.orms.sqlalchemy.mixins import PartitionableMixin

2. Add ``PartitionableMixin`` to the class, to do that change:

.. code-block:: python

    class YourClassName(Base):

to:

.. code-block:: python

    class YourClassName(PartitionableMixin, Base):

3. Add a ``PartitionableMeta`` class to the class with a few settings (keep in mind that this is just
   an example configuration, you have to enter values which represent your exact needs, see below):

.. code-block:: python

    class YourClassName(PartitionableMixin, Base):
        class PartitionableMeta:
            partition_type = 'range'
            partition_subtype = 'date'
            partition_range = 'month'
            partition_column = 'added'

4. Lastly initialize some database stuff, to do that execute the following command:

.. code-block:: bash

    $ architect partition --module path.to.the.class.module --connection dialect://user:pass@host/database

Now, when a new record will be inserted, a value from ``added`` column will be used to determine into
what partition the data should be saved. Keep in mind that if new partitioned models are added or any
settings are changed in existing partitioned models, the command from step 4 should be rerun, otherwise
the database won't know about this changes.

Available settings
------------------

Class settings
~~~~~~~~~~~~~~

All the following class settings should be defined inside class's ``PartitionableMeta`` class:

partition_type
++++++++++++++

Partition type that will be used on the class, currently accepts the following:

* range

partition_subtype
+++++++++++++++++

Partition subtype that will be used on the class, currently used only when ``partition_type`` is set to
``range`` and accepts the following:

* date

partition_range
+++++++++++++++

How often a new partition will be created, currently accepts the following:

* day
* week
* month
* year

partition_column
++++++++++++++++

Column, which value will be used to determine which partition record belongs to
