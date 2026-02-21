Role Name =========
This will deploy an Apache Web server.  It also will restart the service.
You can use this role to deploy a insucure/secure server.  Also if working with a FreeBSD host, you can configure apache in a jail.  In addition you can have this role install packages you provide in a list. Also provides you with the ability to configure multiple directory/web pages to serve applications for instance.  In these additional directories that you provide in a list, you can copy files to those directories.  For example a directory to hold image files.

Examples have been included to show how to provide for the following applications:

Insecure web server

Secure web server

Secure web server running in a Jail

Requirements
------------

None

Role Variables
--------------

*********************************************************************
    
    defaults section

*********************************************************************
# Values: deploy (Setup Apache) resart (restart apache)
apache_operation: deploy

# name of the webserver virtual host
apache_webserver_name: int_webserver

# If true SSL will be enabled
apache_secure_it: true

# main web host, used to set the main host's certificate filename etc
apache_main_hostname: 

# for FreeBSD will address apache running directly on the host or in a jail
apache_jail_it: false

# leave next two variables blank unless installing in jail
apache_jailname: 

# path to pre-pend to the inside jail path.  Leave blank if not configuring
# a jail.  For example if jails on the host exist under "/jails" folder, the
# prepath would be set to "/jails".
apache_jail_prepath:

apache_http_port: 80
apache_https_port: 443

# Used to create the certificates
apache_cert_subj: "/C=US/ST=California/L=SanFrancisco/O=MyCompany/OU=IT/CN=mydomain.com"

# list webpages to validate 
# this variable will be used to make sure that page can be viewed by a validation task
apache_webpage_validation:
  - webpage_url:

# This host will be used to run the uri module via the "delegate_to" attribute to validate the web pages
# from the "apache_webpage_validation variable
apache_validate_delegate_host:

# If true additional web directories will be included in the conf file
# Useful for incuding additional webdirectories to host additional webpages
apache_create_custom_web_directories: false

# Used to setup additional web directories in conf file template
apache_web_directories:
  - name: 
    owner:
    group:
    mod:

# additional packages to install
apache_custom_pkgs:
  -

# custom directives for example if using WSGI
# you could include WSGIScriptAlias directive here.
# you can include multiple lines as this is a list 
# These will exist under the virtual host's
# config
apache_custom_directives:
  -

# custom httpd directives, these will appear
# under the main httpd.conf file, not the copy
# for the virual host
apache_custom_httpd_directives:
  - 

# tells role if the custom file list is being used
apache_custom_filelist_defined: false

# Will copy any file(s) to the location
# specified.  Useful for CGI WSGI scripts
# data text files, pics etc
apache_custom_filelist:
  - filename: 
    src: 
    dest_directory_name: 
    owner: 
    group: 
    mode: 

# Next variables give ability for custom templates
# to be deployed

# if true custome templates will be merged
apache_use_custom_templates: false

# holds source and destination info for template
apache_custom_templates:
  - template_source:
    template_dest_filename:
    file_dest_path:
    file_owner:
    file_group:
    file_mode:


*********************************************************************

    vars

*********************************************************************

                        FreeBSD vars

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
# the same as the variable above it.
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

                                RedHat vars

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


# The following variable only needs to be used by RHEL hosts,
# ncat is installed with FreeBSD userland
# It's needed by the validate web page task to check to see
# if the firewall allows web traffic
apache_ncat_pkg: nmap-ncat

Dependencies
------------

None

Example Playbook
----------------

Simple config-

Example of a simple webserver no security (not SSL enabled security)

- name: test for apache role
  hosts: node5.rocky93.example.com
  become: true

  tasks:
    - name: call role to deploy apache web server
      ansible.builtin.import_role:
        name: apache
      vars:
        apache_operation: deploy
        apache_webserver_name: node5_webserver
        apache_main_hostname: node5.rocky93.example.com
        apache_secure_it: false
        apache_jail_it: false
        apache_validate_delegate_host: walter.example.com
        apache_webpage_validation:
          - webpage_url: "http://{{ apache_main_hostname }}"



-----------------------------------------------------------------------------
Server using SSL Security

* be aware for the apache_cert_subj and apache_main_host_subj variables, replace the items between <> with your appropriate values.

- name: test for apache role
  hosts: node5.rocky93.example.com
  become: true

  tasks:
    - name: call role to deploy apache web server
      ansible.builtin.import_role:
        name: apache
      vars:
        apache_operation: deploy
        apache_webserver_name: node5_webserver
        apache_main_hostname: node5.rocky93.example.com
        apache_secure_it: true
        apache_jail_it: false
        apache_cert_subj: '/C=US/ST=<your state>/L=<your city>/O=<your org>/OU=IT/CN={{ apache_webserver_name }}'
        apache_main_host_subj: '/C=US/ST=<your state>/L=<your city>/O=<your org>/OU=IT/CN={{ apache_main_hostname }}'
        apache_validate_delegate_host: walter.example.com
        apache_webpage_validation:
          - webpage_url: "http://{{ apache_main_hostname }}"

-----------------------------------------------------------------------------
Server using SSL Security and a FreeBSD jail

* be aware for the apache_cert_subj and apache_main_host_subj variables, replace the items between <> with your appropriate values.

- name: test for apache role
  hosts: node5.rocky93.example.com
  become: true

  tasks:
    - name: call role to deploy apache web server
      ansible.builtin.import_role:
        name: apache
      vars:
        apache_operation: deploy
        my_webserver_jail_name: node8-webjail.example.com
        apache_webserver_name: "{{ my_webserver_jail_name }}"
        apache_main_hostname: node8.freebsd13.example.com
        apache_secure_it: true
        apache_jail_it: true
        apache_jailname: node8-webjail
        apache_jail_prepath: "/jails/{{ apache_jailname }}"
        apache_cert_subj: '/C=US/ST=<your state>/L=<your city>/O=<your org>/OU=IT/CN={{ apache_webserver_name }}'
        apache_main_host_subj: '/C=US/ST=<your state>/L=<your city>/O=<your org>/OU=IT/CN={{ apache_main_hostname }}'

        apache_validate_delegate_host: walter.example.com
        apache_webpage_validation:
          - webpage_url: "https://{{ apache_main_hostname }}"


License
-------

BSD

Author Information
------------------

Rich O'Hara
streamline.it.gh@gmail.com
