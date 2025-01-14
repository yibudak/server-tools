========================
Deterministic Session GC
========================

.. 
   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
   !! This file is generated by oca-gen-addon-readme !!
   !! changes will be overwritten.                   !!
   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
   !! source digest: sha256:ab1dd5a935975cca20328a0b2da6cfd070a82ab80de230226dcf911615ae7e1f
   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

.. |badge1| image:: https://img.shields.io/badge/maturity-Beta-yellow.png
    :target: https://odoo-community.org/page/development-status
    :alt: Beta
.. |badge2| image:: https://img.shields.io/badge/licence-AGPL--3-blue.png
    :target: http://www.gnu.org/licenses/agpl-3.0-standalone.html
    :alt: License: AGPL-3
.. |badge3| image:: https://img.shields.io/badge/github-OCA%2Fserver--tools-lightgray.png?logo=github
    :target: https://github.com/OCA/server-tools/tree/12.0/base_deterministic_session_gc
    :alt: OCA/server-tools
.. |badge4| image:: https://img.shields.io/badge/weblate-Translate%20me-F47D42.png
    :target: https://translation.odoo-community.org/projects/server-tools-12-0/server-tools-12-0-base_deterministic_session_gc
    :alt: Translate me on Weblate
.. |badge5| image:: https://img.shields.io/badge/runboat-Try%20me-875A7B.png
    :target: https://runboat.odoo-community.org/builds?repo=OCA/server-tools&target_branch=12.0
    :alt: Try me on Runboat

|badge1| |badge2| |badge3| |badge4| |badge5|

Whenever a new request is processed by Odoo, this statement is evaluated:
`random.random() < 0.001` [1_]

.. _1: https://github.com/odoo/odoo/blob/a0a11fd5e2d78e5fc0d1503275adade570fe0d42/odoo/http.py#L1192

1 time out of 1000 in average, it will return `True` and trigger the
Garbage Collection of sessions: sessions that have
not been active for more than 1 week will be deleted [2_]

.. _2: https://github.com/odoo/odoo/blob/a0a11fd5e2d78e5fc0d1503275adade570fe0d42/odoo/http.py#L1193

This random approach can become a problem in some contexts.

On a highly visited Odoo website, many sessions will be created.
Going through all of them will take some time, especially on a slow/loaded FS.
The Garbage Collection happening randomly, your users will report
you cases of Odoo being randomly slow on random actions. They won't be able
to reproduce these cases and you won't be able to trace them in your
odoo logs neither, because calls' response time doesn't include the time Odoo
spent on the Garbage Collection.

Moreover, on a heavily loaded server, better be in a position to
control when does the Garbage Collection happen. One might want to run it once
per night only for example.

That's why we created this module:

- to disable the default random Garbage Collection
- to enable administrators to replace it by a deterministic approach:

  - either by using the included Scheduled Action;
  - or by calling the added public method
    `ir.autovacuum:gc_sessions()` remotely

**Table of contents**

.. contents::
   :local:

Installation
============

You need to load this module server-wide:

* By starting Odoo with ``--load=web,base_deterministic_session_gc``

* Or by updating its configuration file:

.. code-block:: ini

  [options]
  (...)
  server_wide_modules = web,base_deterministic_session_gc

You also need to install it in your database
if you want to use the provided deterministic approach.

Configuration
=============

You can change the session expiry delay in the Odoo configuration file:

.. code-block:: ini

  [options]
  (...)
  ; 1 day = 60*60*24 seconds
  session_expiry_delay = 86400

Default value is 7 days.

Bug Tracker
===========

Bugs are tracked on `GitHub Issues <https://github.com/OCA/server-tools/issues>`_.
In case of trouble, please check there if your issue has already been reported.
If you spotted it first, help us to smash it by providing a detailed and welcomed
`feedback <https://github.com/OCA/server-tools/issues/new?body=module:%20base_deterministic_session_gc%0Aversion:%2012.0%0A%0A**Steps%20to%20reproduce**%0A-%20...%0A%0A**Current%20behavior**%0A%0A**Expected%20behavior**>`_.

Do not contact contributors directly about support or help with technical issues.

Credits
=======

Authors
~~~~~~~

* Trobz

Contributors
~~~~~~~~~~~~

* Nils Hamerlinck <nils@trobz.com>

Maintainers
~~~~~~~~~~~

This module is maintained by the OCA.

.. image:: https://odoo-community.org/logo.png
   :alt: Odoo Community Association
   :target: https://odoo-community.org

OCA, or the Odoo Community Association, is a nonprofit organization whose
mission is to support the collaborative development of Odoo features and
promote its widespread use.

This module is part of the `OCA/server-tools <https://github.com/OCA/server-tools/tree/12.0/base_deterministic_session_gc>`_ project on GitHub.

You are welcome to contribute. To learn how please visit https://odoo-community.org/page/Contribute.
