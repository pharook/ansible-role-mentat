.. _section-role-mentat:

Role **mentat**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/mentat>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-mentat>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-mentat>`__

Ansible role for convenient installation of the `Mentat IDS and SIEM system <https://mentat.cesnet.cz/>`__.

This role attempts to keep the things as simple as possible and performs only
basic installation of the system. All of the modules need to be configured manually
including the web interface.

**Table of Contents:**

* :ref:`section-role-mentat-installation`
* :ref:`section-role-mentat-dependencies`
* :ref:`section-role-mentat-usage`
* :ref:`section-role-mentat-variables`
* :ref:`section-role-mentat-files`
* :ref:`section-role-mentat-author`

This role is part of the `MSMS <https://github.com/honzamach/msms>`__ package.
Some common features are documented in its :ref:`manual <section-manual>`.


.. _section-role-mentat-installation:

Installation
--------------------------------------------------------------------------------

To install the role `honzamach.mentat <https://galaxy.ansible.com/honzamach/mentat>`__
from `Ansible Galaxy <https://galaxy.ansible.com/>`__ please use variation of
following command::

    ansible-galaxy install honzamach.mentat

To install the role directly from `GitHub <https://github.com>`__ by cloning the
`ansible-role-mentat <https://github.com/honzamach/ansible-role-mentat>`__
repository please use variation of following command::

    git clone https://github.com/honzamach/ansible-role-mentat.git honzamach.mentat

Currently the advantage of using direct Git cloning is the ability to easily update
the role when new version comes out.


.. _section-role-mentat-dependencies:

Dependencies
--------------------------------------------------------------------------------

This role is dependent on following roles:

* :ref:`postgresql <section-role-postgresql>`
* :ref:`geoip <section-role-geoip>`

No other roles have dependency on this role.


.. _section-role-mentat-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    # EITHER: Add your server to 'servers_development' group to install 'development' package suite:
    [servers_development]
    your-server

    # OR: Add your server to 'servers_production' group to install 'production' package suite:
    [servers_production]
    your-server

    [servers_mentat]
    your-server

Example content of role playbook file ``role_playbook.yml``::

    - hosts: servers_mentat
      remote_user: root
      roles:
        - role: honzamach.mentat
      tags:
        - role-mentat

Example usage::

    # Run everything:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml

    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml --extra-vars '{"hm_mentat__skip_cleanup":true}'

    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml --extra-vars '{"hm_mentat__apt_force_update":"yes"}'


.. _section-role-mentat-variables:

Configuration variables
--------------------------------------------------------------------------------


Internal role variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: hm_mentat__user

    Name of the UNIX system user for Mentat system.

    * *Datatype:* ``string``
    * *Default:* ``mentat``

.. envvar:: hm_mentat__group

    Name of the UNIX system group for Mentat system.

    * *Datatype:* ``string``
    * *Default:* ``mentat``

.. envvar:: hm_mentat__package_repository_url

    Base URL to package repository.

    * *Datatype:* ``string``
    * *Default:* ``https://alchemist.cesnet.cz``

.. envvar:: hm_mentat__suite

    Enforce which package suite to install on target servers no matter the membership
    in groups ``servers_production``, ``servers_demo`` and ``servers_development``.

    * *Datatype:* ``string``
    * *Default:* ``null``

.. envvar:: hm_mentat__install_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST be present on target system. Any package on this list will be installed on
    target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_mentat__install_packages:
          debian:
            apt:
              - mentat-ng
              - ...

.. envvar:: hm_mentat_do_cleanup

    Do system cleanup (flag).

    * *Datatype:* ``boolean``
    * *Default:* ``false``

.. envvar:: hm_mentat__apt_force_update

    Force APT cache update before installing any packages ('yes','no').

    * *Datatype:* ``string``
    * *Default:* ``no``

.. envvar:: hm_mentat__python_venv

  Location for custom Mentat`s Python virtual environment.

  * *Type:* ``string``
  * *Default:* ``"/var/mentat/venv"``

.. envvar:: hm_mentat__server_admin

  Enforce the ServerAdmin Apache2 configuration.

  * *Type:* ``string``
  * *Default:* ``"webmaster@{{ ansible_fqdn }}"``

.. envvar:: hm_mentat__check_queue_size

    Monitoring configuration setting for checking queue size in the *incoming* directory.

    * *Datatype:* ``dict``
    * *Default:* ``{'w': 5000, 'c': 10000}``

.. envvar:: hm_mentat__check_queue_dirs

    Monitoring configuration setting for checking queue size in other than *incoming*
    directories.

    * *Datatype:* ``dict``
    * *Default:* ``{'w': 100, 'c': 1000}``

.. envvar:: hm_mentat__deprecated_files

    List of deprecated files and folders that may be stil present after previous
    versions of Mentat system. These will be removed to keep the system tidy.

    * *Datatype:* ``list of strings``
    * *Default:* (please see YAML file ``defaults/main.yml``)


Foreign variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`hm_monitored__service_name`

    Name of the NRPE service in case the server is in **servers_monitored**
    group and the playbook is automagically configuring monitoring of the Mentat
    system.

Built-in Ansible variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: ansible_lsb['codename']

    Debian distribution codename is used for :ref:`template customization <section-overview-role-customize-templates>`
    feature.

.. envvar:: group_names

    See section *Group memberships* below for details.


Group memberships
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* **servers_production** or **servers_development** or **servers_testing**

  I like to use certain groups for dividing servers according to the service
  level. Currently following levels are recognized:

  * servers_development
  * servers_production
  * servers_testing

  This role in particular currently recognizes only ``servers_development`` and
  ``servers_production`` groups. You may use membership in aforementioned groups
  to choose which package suite (*development* or *production*) will be installed
  on target host.

* **servers_monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the Mentat system.

* **servers_commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the Mentat system.


.. _section-role-mentat-files:

Managed files
--------------------------------------------------------------------------------

.. note::

    This role supports the :ref:`template customization <section-overview-role-customize-templates>` feature.

This role manages content of following files on target system:

* ``/etc/nagios/nrpe.d/mentat.cfg`` *[TEMPLATE]*
* ``/opt/system-status/system-status.d/40-mentat`` *[TEMPLATE]*
* ``/etc/apache2/sites-available/site_mentat-ng.conf`` *[TEMPLATE]*
* ``/etc/apache2/sites-enabled/site_mentat-ng.conf`` *[TEMPLATE]*
* ``/etc/apache2/sites-available/site_mentat_redirect.conf`` *[TEMPLATE]*
* ``/etc/apache2/sites-enabled/site_mentat_redirect.conf`` *[TEMPLATE]*

.. _section-role-mentat-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| *Author:* Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| Use of this role is governed by the MIT license, see LICENSE file.
|
