Ansible-Kibana
=========

Install and upgrade Kibana 6 incl. full configuration file control, x-pack support and logrotate.

Requirements
------------

If x-pack security is enabled, all certificates should already be in place. See "Role Variables" for the certificate variables.

Role Variables
--------------

Default variables:
```
### Main config settings
kb_repo_major_version: 6
kb_use_elastic_repository: true
#kb_proxy_host:
#kb_proxy_port:
kb_restart_on_change: false
kb_allow_downgrade: true

### Kibana user and group settings
kb_user: kibana
#kb_user_id: UID
kb_group: kibana
#kb_group_id: GID
kb_dirs_mode: "0750"
kb_files_mode: "0640"

### Directories
kb_pid_dir: "/var/run/kibana"
kb_data_dir: "/var/lib/kibana"
kb_log_dir: "/var/log/kibana"
kb_conf_dir: "/etc/kibana"
kb_home_dir: "/usr/share/kibana"
```
Variables for the config file:
```
### Configuration file settings
kb_server_port: 5601
kb_server_host: "0.0.0.0"
kb_node_name: "{{ ansible_hostname }}"
kb_elasticsearch_url: "http{% if kb_enable_xpack and 'security' in kb_xpack_features %}s{% endif %}://{{ ansible_nodename }}:9200"
kb_server_maxPayloadBytes: 1048576
kb_elasticsearch_preserveHost: true
kb_kibana_index: ".kibana"
kb_kibana_defaultAppId: "home"
kb_elasticsearch_username: "kibana"
kb_elasticsearch_password: "changeme"
kb_ssl_protocols:
  - TLSv1.2
kb_ssl_cipher_suites:
  - ECDHE-RSA-AES128-GCM-SHA256
  - ECDHE-ECDSA-AES128-GCM-SHA256
  - ECDHE-RSA-AES128-SHA256
  - ECDHE-ECDSA-AES128-SHA256
  - ECDHE-RSA-AES256-GCM-SHA384
  - ECDHE-ECDSA-AES256-GCM-SHA384
  - ECDHE-RSA-AES256-SHA384
  - ECDHE-ECDSA-AES256-SHA384
kb_server_ssl_enabled: false
#kb_server_ssl_cert: /path/to/your/server.crt
#kb_server_ssl_key: /path/to/your/server.key
#kb_server_ssl_ca: [ "/path/to/your/CA.pem" ]
#kb_elasticsearch_ssl_cert: /path/to/your/client.crt
#kb_elasticsearch_ssl_key: /path/to/your/client.key
#kb_elasticsearch_ssl_ca: [ "/path/to/your/ES-CA.pem" ] 
#kb_elasticsearch_ssl_verify: true
#kb_elasticsearch_ssl_verificationMode: full
#kb_elasticsearch_requestHeadersWhitelist: []
#kb_elasticsearch_customHeaders: {}
kb_elasticsearch_requestTimeout: 30000
kb_elasticsearch_pingTimeout: 30000
kb_elasticsearch_shardTimeout: 0
kb_elasticsearch_startupTimeout: 5000
kb_logging_silent: false
kb_logging_quiet: false
kb_logging_verbose: false
kb_ops_interval: 5000

### Configure available ui's
kb_ui_timelion: false
kb_ui_console: false
```
X-Pack plugin variables:
```
### X-Pack options
kb_enable_xpack: false
#Valid values are: ["monitoring","graph","security", "reporting", "ml"]
kb_xpack_features: []
#by default, turn off monitoring menu item in kibana
kb_xpack_ui_monitoring: false
kb_xpack_ui_apm: false
kb_xpack_secure_cookies: "{{ (kb_enable_xpack and 'security' in kb_xpack_features) }}"
kb_xpack_encryption_key: "something_at_least_32_characters"
#kb_xpack_url: ""
```
Logrotate variables
```
### Logrotate
kb_logging_manage: true
kb_logging_max_file_size: "100M"
kb_logging_max_files: 7
kb_logging_delay_compress: true
```

Dependencies
------------

None

Example Playbook
----------------

Basic playbook to install Kibana:
```
- name: Install and configure kibana
  hosts: kibana_nodes
# please note: {"message":"Optimizing and caching bundles for monitoring, kibana, stateSessionStorageRedirect and status_page. This may take a few minutes"}
# CAN TAKE UP TO 30 MINUTES!
# once finished, kibana will start automatically.
  tasks:
  - name: Include Kibana role
    include_role:
      name: kibana
```

Example Configs
---------------

Place below variables in the group_vars or host_vars of your kibana_nodes.

Basic kibana install with UID and GID
```
kb_user_id: 4503
kb_group_id: 3402
```
Kibana install with X-Pack Monitoring enabled
```
kb_enable_xpack: true
kb_xpack_features: ["monitoring"]
```
Kibana install with timelion and console:
```
kb_ui_timelion: true
kb_ui_console: true
```
Kibana install Full options:
```
kb_server_ssl_cert: /path/to/your/server.crt
kb_server_ssl_key: /path/to/your/server.key
kb_server_ssl_ca: [ "/path/to/your/CA.pem" ]
#Needed for X-Pack Security:
kb_elasticsearch_ssl_cert: /path/to/your/client.crt
kb_elasticsearch_ssl_key: /path/to/your/client.key
kb_elasticsearch_ssl_ca: [ "/path/to/your/ES-CA.pem" ] 
kb_elasticsearch_ssl_verify: true
kb_elasticsearch_ssl_verificationMode: full
kb_enable_xpack: true
kb_xpack_features: ["monitoring","graph","security", "reporting", "ml"]
kb_xpack_ui_monitoring: true
kb_xpack_ui_apm: true
kb_xpack_secure_cookies: "{{ (kb_enable_xpack and 'security' in kb_xpack_features) }}"
kb_xpack_encryption_key: "something_at_least_32_characters"
```

License
-------

Apache 2

Author Information
------------------

