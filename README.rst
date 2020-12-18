.. This README is meant for consumption by humans and pypi. Pypi can render rst files so please do not use Sphinx features.
   If you want to learn more about writing documentation, please check out: http://docs.plone.org/about/documentation_styleguide.html
   This text does not appear on pypi or github. It is a comment.

.. image::
   https://img.shields.io/badge/%20imports-isort-%231674b1?style=flat&labelColor=ef8336
       :target: https://pycqa.github.io/isort/

===================
visaplan.zope.reldb
===================

This package provides the configuration of a `Data source name`_ (DSN),
e.g. for use with SQLAlchemy_.

For the heavy lifting of integration of database transactions of your
relational database (like PostgreSQL_ or whatever) with the ZODB_, you'll
likely want to use something like zope.sqlalchemy_, which in turn requires
sqlalchemy_ to talk to the database.  However, zope.sqlalchemy_ doesn't offer a
configuration method for the DSN.  This package offers to store this `DSN` in
the Zope configuration file (``parts/clientN/etc/zope.conf``).

The idea is: You might have several instances of your Zope (for production,
testing, development ...), and every now and then you replicate the productive
data.  With the DSN stored in the ZODB, you'd have this information replicated
as well, which might
*make your test and/or development instances write to your productive database.*

No, you don't want this to happen.

By the way: both the normal ZODB_ configuration and RelStorage_  store
their information in that very ``zope.conf`` file.
Our idea can't be so bad, then.


Features
========

- Stores a DSN string in the Zope configuration and provides a `get_dsn`
  function.
- With SQLAlchemy_ installed,

  - creates an Engine_ and a DBSession,
    and
  - provides an optional `.legacy.SQLWrapper` `context manager`_ class
    which sports a few simple
    `insert`, `update`, `delete`, `select` and `query` methods

- With zope.sqlalchemy_ installed as well, registers that DBSession
  for the transaction machinery integration.


Installation
============

Add the package to your product requirements somehow;
since we'll need to add a product configuration as well,
you'll most likely add it to your `eggs` value
in your buildout script (`buildout.cfg`)::


    [buildout]
    ...

    eggs =
        your.fancy.product
        visaplan.zope.reldb_

Add a product configuration there as well, containing your data source name::

    [buildout]
    ...

    [instance_base]
    ...
    zope-conf-additional =
        <product-config reldb>
        dsn postgresql+psycopg2://localhost/mydb
        </product-config>

(We follow the convention used by the UnifiedInstaller_ here; your section(s)
might be named differently.)

After running ``bin/buildout`` and restarting your Zope instance,
the product configuration should have been added to your `zope.conf` file(s),
and `your.fancy.product` can read the DSN string by calling
`visaplan.zope.reldb.get_dsn` (*or*, if you have SQLAlchemy_,
directly use `visaplan.zope.reldb.engine.engine`).


Remark
------

"But, can't my product simply do all this by itself?!"

Sure. Having this as a package helps / helped us to switch a whole bunch of
packages from usage of Zope database adapters (stored in the ZODB) to
sqlalchemy (with a DSN configured in the ``zope.conf`` file(s)).


Contribute
==========

- Issue Tracker: https://github.com/visaplan/zope.reldb/issues
- Source Code: https://github.com/visaplan/zope.reldb


Support
=======

If you are having issues, please let us know;
please use the `issue tracker`_ mentioned above.


License
=======

The project is licensed under the GPLv2.

Futher reading
==============

* The `SQLAlchemy documentation`_ about `Database URLs`_

.. _`context manager`: https://www.python.org/dev/peps/pep-0343/#specification-the-with-statement
.. _`Database URLs`: https://docs.sqlalchemy.org/en/latest/core/engines.html#database-urls
.. _`data source name`: https://en.wikipedia.org/wiki/Data_source_name
.. _Engine: https://docs.sqlalchemy.org/en/13/core/connections.html#sqlalchemy.engine.Engine
.. _`issue tracker`: https://github.com/visaplan/zope.reldb/issues
.. _PostgreSQL: https://www.postgresql.org
.. _RelStorage: https://pypi.org/project/relstorage
.. _`SQLAlchemy documentation`: https://docs.sqlalchemy.org
.. _sqlalchemy: https://pypi.org/project/sqlalchemy
.. _UnifiedInstaller: https://github.com/plone/Installers-UnifiedInstaller#installation
.. _visaplan.zope.reldb: https://pypi.org/project/visaplan.zope.reldb
.. _ZODB: https://en.wikipedia.org/wiki/Zope_Object_Database
.. _zope.sqlalchemy: ://pypi.org/project/zope.sqlalchemy

.. vim: tw=79 cc=+1 sw=4 sts=4 si et
