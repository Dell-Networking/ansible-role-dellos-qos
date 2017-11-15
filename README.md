QoS role
========

This role facilitates the configuration quality of service (QoS) attributes like policy-map and class-map. This role is abstracted for dellos10. The QoS role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in Dell EMC Networking OS connection variables, or the *provider* dictionary.

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-qos

Role variables
--------------

- Role is abstracted using the *ansible_net_os_name* variable that can take a dellos10 value
- If *dellos_cfg_generate* is set to true, the variable generates the role configuration commands in a file
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable 
- Setting an empty value for any variable negates the corresponding configuration
- Variables and values are case-sensitive

**dellos_qos keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``policy_map`` | list | Configures the policy-map (see ``policy_map.*``) | dellos10 |
| ``policy_map.name`` | string (required)        | Configures the policy-map name  | dellos10 |
| ``policy_map.type`` | string: qos\*,application,control-plane,network-qos,queuing    | Configures the policy-map type  | dellos10 |
| ``policy_map.state`` | string: present\*,absent   | Deletes the policy-map if set to absent  | dellos10 |
| ``class_map`` | list | Configures the class-map (see ``class_map.*``) | dellos10 |
| ``class_map.name`` | string (required)        | Configures the class-map name  | dellos10 |
| ``class_map.type`` | string: qos\*,application,control-plane,network-qos,queuing    | Configures the class-map type  | dellos10 |
| ``class_map.state`` | string: present\*,absent   | Deletes the class-map if set to absent  | dellos10 |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified. 

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the value defaults to 22 |
| ``username`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_NET_USERNAME environment variable value is used |
| ``password`` | no       |            | Specifies the password that authenticates the connection to the remote device; if value is unspecified, the ANSIBLE_NET_PASSWORD environment variable value is used |
| ``authorize`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_NET_AUTHORIZE environment variable values is used, and the device attempts to execute all commands in non-privileged mode |
| ``auth_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if *authorize* is set to no, key is not applicable; if value is unspecified, the ANSIBLE_NET_AUTH_PASS environment variable value is used |
| ``provider`` | no       |            | Passes all connection arguments as a dictonary object; all constraints (such as required or choices) must be met either by individual arguments or values in this dictionary |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-qos* role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example playbook
----------------

This example uses the *dellos.dellos-qos* role to configure the policy-map class-map. It creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_net_os_name* variable with corresponding Dell EMC networking OS name. When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default, the variable is set to false. It writes a simple playbook that only references the *dellos-qos* role. By including the role, you automatically get access to all of the tasks to configure qos features. 

**Sample hosts file**
 
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos10)>

**Sample host_vars/leaf1**

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx 
    build_dir: ../temp/dellos10
	  
    dellos_qos:
      policy_map:
       - name: testpolicy
         type: qos
         state: present
      class_map:
       - name: testclass
         type: application
         state: present
     
**Simple playbook to setup qos - leaf.yaml**

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-qos

**Run**

    ansible-playbook -i hosts leaf.yaml

(c) 2017 Dell EMC
