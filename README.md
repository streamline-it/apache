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

#############################################
# Defaults
#############################################
# Values for this variable:
# "deploy"  deploy the webserver
# "restart" restart the service
apache_operation: deploy

# name of the webserver virtual host
apache_webserver_name: int_webserver

# If true SSL will be enabled
apache_secure_it: true

# main web host, used to set the main host's certification filename etc
apache_main_hostname: 

apache_jail_it: false

# leave next two variables blank unless installing in jail
apache_jailname: 

# path to pre-pend to the inside jail path.  Leave blank if not configuring
# a jail
apache_jail_prepath:

apache_http_port: 80
apache_https_port: 443

# Used to create the certificates
apache_cert_subj: "/C=US/ST=California/L=SanFrancisco/O=MyCompany/OU=IT/CN=mydomain.com"


#############################################
# vars/RedHat.yml
#############################################

apache_pkg: "@Basic Web Server"
apache_svc: httpd
apache_root_group: root

# Used on the FreeBSD side, leave blank on linux side
apache_enable_directive:

# group to use for apache
apache_group: apache

# path to virtual hosts
# note no all "relative" variables below.  This is a FreeBSD need, ignore for Linux hosts.
# just make the relative value the same as the variable above it on the Linux side
apache_virtual_hosts_directory: "/var/www/virtual_hosts"
apache_virtual_hosts_directory_relative: "/var/www/virtual_hosts"
apache_host_webcontent_directory_relative: /var/www/html
apache_virtual_hosts_webcontent_directory: /var/www/virtual_hosts/apache_webserver_name/html
apache_virtual_hosts_webcontent_directory_relative: /var/www/virtual_hosts/apache_webserver_name/html
apache_virtual_hosts_webcontent_directory_relative: /var/www/virtual_hosts/apache_webserver_name/html
apache_default_webcontent_directory: "/var/www/html"
apache_default_webcontent_directory_relative: "/var/www/html"
apache_config_directory: "/etc/httpd/conf.d"
apache_config_base_directory: "/etc/httpd/conf"
apache_config_virt_hosts_filepath: "/etc/httpd/conf.d"
apache_cert_destination_filepath: "/etc/pki/tls/certs/{{ apache_webserver_name }}.crt"
apache_cert_destination_relative_filepath: "/etc/pki/tls/certs/{{ apache_webserver_name }}.crt"
apache_cert_key_destination_filepath: "/etc/pki/tls/private/{{ apache_webserver_name }}.key"
apache_cert_key_destination_relative_filepath: "/etc/pki/tls/private/{{ apache_webserver_name }}.key"

#used to create certificates and certificate keys
apache_create_cert_commands:
  - command: "openssl req -new -x509 -newkey rsa:2048 -keyout /etc/pki/tls/private/{{ apache_webserver_name }}.key -out /etc/pki/tls/certs/{{ apache_webserver_name }}.crt -days 365 -nodes -subj '{{ apache_cert_subj }}'"
    certfile: "/etc/pki/tls/certs/{{ apache_webserver_name }}.crt"
  - command: "openssl req -new -x509 -newkey rsa:2048 -keyout /etc/pki/tls/private/{{ apache_main_hostname }}.key -out /etc/pki/tls/certs/{{ apache_main_hostname }}.crt -days 365 -nodes -subj '{{ apache_main_host_subj }}'"
    certfile: "/etc/pki/tls/certs/{{ apache_main_hostname }}.crt"

# mods to the the httpd.conf file
apache_httpd_conf_lines:
  - line:


#############################################
# vars/FreeBSD.vars
#############################################

# Used to enable service in the rc.conf file
apache_enable_directive: apache24_enable="yes"
# used for root user under FreeBSD
apache_root_group: wheel

# used for the group for apache
apache_group: www
apache_pkg: apache24
apache_openssl_pkg: openssl

# needed for the service "start" 
apache_svc: apache24

# All "relative" variables contain the "inside jail" path that usually is used for
# config lines.  If not running the webserver from a jail, make the relative value
# the save as the variable above it.
apache_virtual_hosts_directory: "{{ apache_jail_prepath }}/usr/local/www/virtual_hosts"
apache_virtual_hosts_directory_relative: "/usr/local/www/virtual_hosts"
apache_host_webcontent_directory_relative: /usr/local/www/apache24/data/
apache_virtual_hosts_webcontent_directory: "{{ apache_jail_prepath }}/var/www/virtual_hosts/{{ apache_webserver_name }}/data"
apache_virtual_hosts_webcontent_directory_relative: "/usr/local/www/virtual_hosts/{{ apache_webserver_name }}/data"
apache_default_webcontent_directory: "{{ apache_jail_prepath }}/usr/local/www/apache24/data/"
apache_default_webcontent_directory_relative: "/usr/local/www/apache24/data/"
apache_config_directory: "{{ apache_jail_prepath }}/usr/local/etc/apache24/Includes"
apache_config_base_directory: "{{ apache_jail_prepath }}/usr/local/etc/apache24"
apache_config_virt_hosts_filepath: "{{ apache_jail_prepath }}/usr/local/etc/apache24/Includes"
apache_cert_destination_filepath: "{{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_webserver_name }}.crt"
apache_cert_destination_relative_filepath: "/usr/local/etc/apache24/{{ apache_webserver_name }}.crt"
apache_cert_key_destination_filepath: "{{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_webserver_name }}.key"
apache_cert_key_destination_relative_filepath: "/usr/local/etc/apache24/{{ apache_webserver_name }}.key"

# used to create the certificates and cert keys
apache_create_cert_commands:
  - command: "openssl req -x509 -newkey rsa:2048 -keyout {{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_webserver_name }}.key -out {{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_webserver_name }}.crt -days 365 -nodes -subj '{{ apache_cert_subj}}'"
    certfile: "/usr/local/etc/apache24/{{ apache_webserver_name }}.crt"
  - command: "openssl req -x509 -newkey rsa:2048 -keyout {{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_main_hostname }}.key -out {{ apache_jail_prepath }}/usr/local/etc/apache24/{{ apache_main_hostname }}.crt -days 365 -nodes -subj '{{ apache_main_host_subj}}'"
    certfile: "/usr/local/etc/apache24/{{ apache_main_hostname }}.crt"

# used to add needed directives to the httpd.conf file in order to enable Rewrite and Secure SSL connections
apache_httpd_conf_lines:
  - line: LoadModule ssl_module libexec/apache24/mod_ssl.so
  - line: LoadModule socache_shmcb_module libexec/apache24/mod_socache_shmcb.so
  - line: LoadModule rewrite_module libexec/apache24/mod_rewrite.so
  - line: LoadModule vhost_alias_module libexec/apache24/mod_vhost_alias.so
  - line: Include etc/apache24/extra/httpd-ssl.conf

# list webpages to validate 
# this variable will be used to make sure that page can be viewed 
apache_webpage_validation:
  - webpage_url:
