Role Name
=========

This will deploy an Apache Web server.  It also will restart the service.

Requirements
------------

None

Role Variables
--------------
# Contols operation of Apache
# "deploy" - Will setup  a webserver; install packages and configure a virtual webserver
# "restart" - Will restart the Apache service
apache_operation: deploy

# Name of the virtual webserver
apache_webserver_name: int_webserver

# Port to use for http traffic
apache_http_port: 80

# Port to use for https traffic
apache_https_port: 443

# Directory to place webcontent for the virtual web server

apache_virtual_hosts_directory: /var/www/virtual_hosts

# Directory for the default web server's content
apache_default_webcontent_directory: /var/www/html

# Directory to use for the config files
apache_config_directory: /etc/httpd/conf.d

# Certificate file path
apache__certificate_file_path: "/files/{{ apache_webserver_name }}.crt"

# Certificate key file path
apache_certificate_key_file_path: "./files/{{ apach_webserver_name }}.key"

Dependencies
------------

None

Example Playbook
----------------

name: Deploy an Apache Web Server
hosts: node2.example.com
tasks:
  - name: call role to deploy apache web server
      ansible.builtin.import_role:
        name: apache
      vars:
        apache_operation: deploy

License
-------

BSD

Author Information
------------------

Rich O'Hara
streamline.it.gh@gmail.com
