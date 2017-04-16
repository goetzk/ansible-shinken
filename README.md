Shinken
=======

This role is to install Shinken and configure your hosts and services.

Requirements
------------

This role requires sudo to be installed.

If installing through pip, no other external dependencies should be required.

This role has been tested on:
- Debian 8.x (Jessie)
- Centos 6.8
- Amazon Linux AMI release 2016.09

It is likely to work on others (eg Ubuntu, RedHat, Fedora, ...), it simply hasn't been run there by me.

Role Variables
--------------

The following are set via defaults/main.yml *and currently usable* (some things
have not been implemented yet)


    # How should things be installed, 'packages' or 'pip'
    shinken_install_from: 'pip'

    # Path of broker configuration
    shinken_config_broker: /etc/shinken/brokers/

    # List of modules to enable in the broker. 'webui2' enables web interface.
    shinken_broker_modules: 'webui2'

    # Shinken web UI configuration, only changed items are here so far.
    shinken_broker_webui_host: 0.0.0.0
    shinken_broker_webui_port: 7767
    shinken_broker_webui_auth_secret: SiteSpecificAuthSecret # MUST BE CHANGED

    # Where are contacts placed
    shinken_config_contacts: /etc/shinken/contacts/

    # Details of the available contacts (include password for internal authentication)
    # This example shows both expanded and single line list format for admin and guest.
    shinken_contacts:
     - contact_name: 'admin'
       email: 'shinken@localhost'
       password: 'password'
       is_admin: 1
       expert: 1
       can_submit_commands: 1
     - { contact_name: 'guest', email: 'guest@localhost', password: 'password'}

    # where does service configuration go?
    shinken_config_services: /etc/shinken/services/

    # What services are we monitoring? which hostgroups do they apply to?
    # NOTE: hostgroup_name missing means the test will be applied to ALL hostgroups.
    # To apply to all except a subset of hosts use hostgroup_name: [ 'all!excludeme' ]
    shinken_services:
     - { service_description: 'ping test', command: 'check_ping' }
     - { service_description: 'port 80 test', command: 'check_tcp!80', hostgroup_name: ['webservers', 'proxies'] }

    # Where should the host configuration go
    shinken_config_hosts: /etc/shinken/hosts/
    shinken_config_hostgroups: /etc/shinken/hostgroups/

    # Which hosts are we monitoring? NOTE: hostgroups list is a required field, even if empty.
    shinken_hosts:
     - { host_name: 'router.local' , hostgroups: [ 'infrastructure' ]}
     - { host_name: 'modem.local', parent: 'router.local', hostgroups: [ 'appliances' ] }
     - { host_name: 'shinken.io', parent: 'modem.local', hostgroups: [ 'remote-server', 'web-server' ] }



Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: monitoring
      vars:
        shinken_hosts:
         - { host_name: 'router.local' , hostgroups: [ 'infrastructure' ]}
         - { host_name: 'modem.local', parent: 'router.local', hostgroups: [ 'appliances' ] }
        shinken_contacts:
         - { contact_name: 'guest', email: 'guest@localhost', password: 'password'}
         - contact_name: 'admin'
           email: 'admin@example.com'
           pager: '+12312312312'
           password: 'something here'
           is_admin: 1
           expert: 1
           can_submit_commands: 1
           host_notifications_enabled: 1
           service_notifications_enabled: 1
           notificationways: 'email'
      roles:
         - { role: goetz.shinken, shinken_broker_modules: webui2 }

License
-------

GPL2+

Author Information
------------------

Issues or feedback can be reported to the author at karl@kgoetz.id.au; please
prefix the subject with 'ansible' or 'role'.

