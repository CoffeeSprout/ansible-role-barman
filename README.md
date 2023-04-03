coffeesprout.barman
=========

Install and configure Barman (https://pgbarman.org) for backing up PostgreSQL databases

Requirements
------------

Barmnan requires access to at least the PostgreSQL server (networking, pg\_hba.conf and users, see below)
Optionally you also need access to SSH if you want to use rsync or recover the DB remotely from the backup server

The database requires 2 users, please follow the manual: https://docs.pgbarman.org/release/3.0.1/#preliminary-steps

Role Variables
--------------

Here are the variables available to configure the role:

- `postgresql_version`: Barman requires the PostgreSQL client and sometimes server binaries. This specifies the default. We generally target the latest, but keep in mind that streaming replication only works within the major version of the server (i.e., you can only backup PostgreSQL 10 with 10). See: https://www.postgresql.org/docs/current/warm-standby.html#STANDBY-PLANNING
- `barman_user`: The user under which Barman runs.
- `barman_config_directory`: The directory where the server configs are stored, usually overridden by platform vars.
- `barman_home`: The Barman home folder, usually overwritten by platform vars.
- `barman_log_directory`: The directory where the cron and Barman logs are stored.
- `barman_server`: A list of dictionaries containing Barman configurations. See example below.
- `barman_default_global_configuration`: A dictionary containing default settings.
- `barman_global_configuration`: Settings in this dictionary overwrite the `barman_default_global_configuration`.

A list of dictionaries containing barman configurations; See example below

    barman_default_global_configuration:
          streaming_archiver: "on"
          immediate_checkpoint: "True"
          path_prefix: "/usr/pgsql-{{ postgresql_version }}/bin"
          backup_compression: "gzip"
          create_slot: "auto"

Dictionary containing default settings of my liking

    barman_global_configuration

Settings in this dictionary overwrite the barman\_default\_global\_configuration


Example Playbook
----------------

Example:

    - hosts: servers
      roles:
      - role: coffeesprout.barmanprout.barman/
        barman_server_configuration:
        - name: streaming-serverbarman
          settings:
            backup_method: postgres	
            description: "Some shorter description"
            conninfo: "host={{ db_ip }} user=barman dbname=postgres password={{ db_barman_password }}"
            streaming_conninfo: "host={{ db_ip }} user=streaming_barman password={{ db_streaming_password }}"
            slot_name: "barman"

License
-------

BSD

Author Information
------------------

Just reach out via Github
