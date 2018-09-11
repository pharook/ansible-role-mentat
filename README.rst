.. _section-role-mentat:

Role **mentat**
================================================================================

Ansible role for convenient installation of the `Mentat IDS and SIEM system <https://mentat.cesnet.cz/>`__.

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/mentat>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-mentat>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-mentat>`__


Description
--------------------------------------------------------------------------------

This role attempts to keep the things as simple as possible and performs only
basic installation of the system. All of the modules need to be configured manually
including the web interface.

.. note::

    This role supports the :ref:`template customization <section-overview-customize-templates>` feature.


Requirements
--------------------------------------------------------------------------------

Python3 with pip3 utility should already be installed on target system.


Dependencies
--------------------------------------------------------------------------------

This role is dependent on following roles:

* :ref:`postgresql <section-role-postgresql>`

Following roles have direct dependency on this role:

* :ref:`mentat_demo <section-role-mentat-demo>`


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/nagios/nrpe.d/mentat.cfg``
* ``/opt/system-status/system-status.d/40-mentat``


Role variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

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
	in groups ``servers-production``, ``servers-demo`` and ``servers-development``.

	* *Datatype:* ``string``
    * *Default:* (undefined)

.. envvar:: hm_mentat__package_list

	List of Mentat-related packages, that will be installed on target system.

	* *Datatype:* ``list of strings``
    * *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_mentat_do_cleanup

	Do system cleanup (flag).

	* *Datatype:* ``boolean``
    * *Default:* ``false``

.. envvar:: hm_mentat__deprecated_files

	List of deprecated files and folders that may be stil present after previous
	versions of Mentat system. These will be removed to keep the system tidy.

	* *Datatype:* ``list of strings``
    * *Default:* (please see YAML file ``defaults/main.yml``)

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: ansible_lsb['codename']

	Debian distribution codename is used for :ref:`template customization <section-overview-customize-templates>`
	feature.

.. envvar:: group_names

	See section *Group memberships* below for details.


Foreign variables
--------------------------------------------------------------------------------

This role uses following foreign variables defined in other roles:

:envvar:`hm_monitored__service_name`

    Name of the NRPE service in case the server is in **servers-monitored**
    group and the playbook is automagically configuring monitoring of the Mentat
    system.


Group memberships
--------------------------------------------------------------------------------

* **servers-production** or **servers-development** or **servers-demo**

  I like to use certain groups for dividing servers according to the service
  level. Currently following levels are recognized:

  * servers-development
  * servers-production
  * servers-demo

  This role in particular currently recognizes only ``servers-development`` and
  ``servers-production`` groups. You may use membership in aforementioned groups
  to choose which package suite (*development* or *production*) will be installed
  on target host.

* **servers-monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the Mentat system.

* **servers-commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the Mentat system.


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


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

	# EITHER: Add your server to 'servers-development' group to install 'development' package suite:
	[servers-development]
	localhost

	# OR: Add your server to 'servers-production' group to install 'production' package suite:
	[servers-production]
	localhost

	[servers-mentat]
	localhost

Example content of role playbook file ``playbook.yml``::

	- hosts: servers-mentat
	  remote_user: root
	  roles:
	    - role: honzamach.mentat
	  tags:
	    - role-mentat

Example usage::

	ansible-playbook -i inventory playbook.yml
	ansible-playbook -i inventory playbook.yml --extra-vars '{"hm_mentat__skip_cleanup":true}'
	ansible-playbook -i inventory playbook.yml --extra-vars '{"hm_mentat__apt_force_update":"yes"}'


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
