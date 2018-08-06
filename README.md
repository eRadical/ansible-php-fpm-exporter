php-fpm-exporter
=========

PHP-FPM exporter for Prometheus from https://github.com/bakins/php-fpm-exporter


Role Variables
--------------

- php_fpm_exporter_version - exporter version to be downloaded from the above repo

- php_fpm_exporter_system_group - save & run the executable under this group, defaults to `php_fpm_exporter`
- php_fpm_exporter_system_user - save & run the executable under this group

- php_fpm_exporter_export_ip - the IP address which will be exposed by the exporter, defaults to `ansible_eth0.ipv4.address`

- php_fpm_pools - You can define more than one exporter for each PHP-FPM pool or (as my case is) for each docker container running php-fpm, the playbook will generate a systemd service for each pool

For each pool you have a dict containing:
- name - must be present, the name of the pool, it will be used in the systemd service name
- export_port - must be present, this is the port that the exporter binds to, you'll need this to scrape from Prometheus

And last one of:
- fastcgi_endpoint - this will connect directly to the php-fpm process and get the stats
- http_endpoint - this will pool through the local webserver, note that you must configure the webserver yourself to expose this

You can then use `php_fpm_pools` in your Prometheus server to tell it what to scrape.

Example Playbook
----------------

Add it to your playbook

- hosts: servers
  roles:
    - role: eRadical.php-fpm-exporter

Example of pools:

php_fpm_pools:
  - name: www
    export_port: 8080
    fastcgi_endpoint: tcp://127.0.0.1:9000/status
  - name: another_pool
    export_port: 8081
    http_endpoint: http://127.0.0.1:9000/status


License
-------

- GPLv3
- based mostly on https://github.com/cloudalchemy/ansible-node-exporter
