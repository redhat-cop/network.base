=====================================
Network Base Collection Release Notes
=====================================

.. contents:: Topics


v3.0.0
======

Bugfixes
--------

- provide correct loop var name when running gather task.

Documentation Changes
---------------------

- Update README.md with data_store structure changes and scm enablement.

v2.1.0
======

v2.0.0
======

Major Changes
-------------

- Create inventory based on hostname

Minor Changes
-------------

- Add feature which support detect and remediate actions.
- provide collection prefix with resource name.

Bugfixes
--------

- Fix issue in Detect action.
- Fix state of detect and remediate operation when overridden is not supported by any RMs.

Documentation Changes
---------------------

- Fix docs issues in README.
- Update collection installation section.
- Update instllation dcumentations and workflow.

v1.2.1
======

Bugfixes
--------

- fix runtime dynamic network os resource module invokation.

v1.2.0
======

Release Summary
---------------

Re-releasing v1.1.0 with updated version tag and fixed URLs for issues and repository in galaxy.yml.

v1.1.0
======

Minor Changes
-------------

- fix linting issues and remove integrated health checks.

v1.0.0
======

Release Summary
---------------

Releasing v1.0.0 of the Ansible network.base collection that enables ansible network validated content to gather, persist, deploy facts.
