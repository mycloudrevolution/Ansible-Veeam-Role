Role Name
=========

This Role ships Ansible modules for the management of Veeam Backup & Replication.

Requirements
------------

The Veeam modules are based on the Veeam PowerShell cmdlets ([Veeam PowerShell Reference](https://helpcenter.veeam.com/docs/backup/powershell/cmdlets.html?ver=95u4)). All modules are designed to be executed on a Veeam Veeam Backup & Replication server with installed console and PowerShell Snapin, no remote connection.

Role Variables
--------------

The settable variables depend on the individual module used.

Dependencies
------------

none

Example Playbook
----------------

- name: Add ESXi Host to VBR Server
  hosts: veeam
  gather_facts: no
  roles:
  - veeam
  vars:
    root_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          63386666303563306665376261643130346638336531386334323131303631663534383662663237
          3963343264656633646631366630633765636461396430650a343034363166623039396433386262
          62346534313736306636333937323635616435633632313937303062333665653236376261623837
          6364356265613533380a643834643531663230653365303163363338396266613162663465313433
          31353563386237636330343933643566663536326634356136643839356463346161
  tasks:
  - name: Add root credential
    veeam_credential:
        state: present
        type: standard
        username: root
        password: "{{ root_password }}"
        description: "Lab User for Standalone Host"
    register: root_cred
  - name: Debug root credential
    debug:
        var: root_cred
  - name: Add esxi server
    veeam_server:
        state: present
        type: esxi
        credential_id: "{{ root_cred.id }}"
        name: 192.168.234.101
    register: esxi_server
  - name: Get Veeam Facts
    veeam_connection_facts:
    register: my_facts
  - name: Debug Veeam Servers from Facts
    debug:
        var: my_facts.veeam_facts.veeam_servers

License
-------

GNU Lesser General Public License v3.0

Author Information
------------------

Markus Kraus [@vMarkus_K](https://twitter.com/vMarkus_K)
MY CLOUD-(R)EVOLUTION [mycloudrevolution.com](http://mycloudrevolution.com/)
