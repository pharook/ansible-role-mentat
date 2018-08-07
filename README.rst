.. _section-role-mentat:

Role *mentat*
================================================================================

*Ansible role for convenient installation of the `Mentat IDS and SIEM system <https://mentat.cesnet.cz/>`__.*


Description
--------------------------------------------------------------------------------

This role attempts to keep the things as simple as possible and performs only
basic installation of the system.


Requirements
--------------------------------------------------------------------------------

PostgreSQL database and Python3 with pip3 utility should already be installed on
target system.


Managed files
--------------------------------------------------------------------------------

This role does not directly manage content of any files on target system.


Dependencies
--------------------------------------------------------------------------------

This role is dependent on following roles:

* :ref:`postgresql <section-role-postgresql>`

Following roles have direct dependency on this role:

* :ref:`mentat_demo <section-role-mentat-demo>`


Role Variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_mentat__user

	Name of the UNIX system user for Mentat system.

	*Type:* ``string`` | *Default:* ``"mentat"``

.. envvar:: hm_mentat__group

	Name of the UNIX system group for Mentat system.

	*Type:* ``string`` | *Default:* ``"mentat"``

.. envvar:: hm_mentat__package_repository_url

	Base URL to package repository.

	*Type:* ``string`` | *Default:* ``"https://alchemist.cesnet.cz"``

.. envvar:: hm_mentat__suite_production

	Name of the package suite to use for *prodution* level servers.

	*Type:* ``string`` | *Default:* ``"production"``

.. envvar:: hm_mentat__suite_development

	Name of the package suite to use for *development* level servers.

	*Type:* ``string`` | *Default:* ``"development"``

.. envvar:: hm_mentat__package_list

	List of Mentat-related packages, that will be installed on target system.

	*Type:* ``list of strings`` | *Default:* ``["mentat-ng"]``

.. envvar:: hm_mentat_skip_cleanup

	Skip system cleanup (flag).

	*Type:* ``bool`` | *Default:* ``False``

.. envvar:: hm_mentat__deprecated_files

	List of deprecated files and folders that may be stil present after previous
	versions of Mentat system. These will be removed to keep the system tidy.

	*Type:* ``list of strings``

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: group_names

	I like to use certain groups for dividing servers according to the service
	level. Currently following levels are recognized:

	* servers-development
	* servers-production
	* servers-demo

	This role in particular currently recognizes only ``servers-development`` and
	``servers-production`` groups. You may use membership in aforementioned groups
	to choose which package suite (*development* or *production*) will be installed
	on target host.


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

The advantage of using direct Git cloning is the ability to easily update the role
when new version comes out.


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
