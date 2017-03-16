VLT Role for Dell EMC Networking OS
====================================

This role facilitates the configuration of the basics of Virtual Link Trunking (VLT) to provide a loop-free topology. This role is abstracted for Dell EMC Networking OS9.

Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-vlt
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

``Note: Install python-netaddr library on the ansible control host to use the role variable backupdestination.``

Role Variables
--------------

``dellos_vlt`` (dictionary) contains the hostname (dictionary). The hostname is the value of the ``hostname`` variable that corresponds to the name of the OS device. This role is abstracted using the ``ansible_net_os_name`` variable  that can take the following value: dellos9.

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration. The variables and its values are case-sensitive.
 

``hostname`` holds the following keys:


|        Key | Type                      | Notes                                                                                                                                                                                     |
|-----------:|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| domain       | integer(required)        | Configures VLT domain with a number for identification. The number can be from 1-1000. |
| backupdestination | string    | Configures IPv4 address for VLT backup link in the form A.B.C.D or an IPv6 address in the format X:X:X:X::X. |
| vrfname      | string         | Configures Virtual routing and forwarding (VRF) instance through which the back-up destination IP is reachable. The vrfname must be already present.  |
| channelid    | integer        | Configures peer link port channel ID (1-4096) for the VLT domain. |
| priority     | integer        | Configures primary priority to the corresponding channel ID. |
| unitid       | integer        | Configures system unit ID for VLT. It can be either 0 or 1. |
| state        | string       | Removes the VLT instance when state is set to absent. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```


Connection Variables
--------------------

Ansible Dell EMC Networking OS roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible group_vars or host_vars directories, or in the playbook itself.

|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Specifies the host name or address for connecting to the remote device over the specified ``transport`` variable. The value of ``host`` is the destination address for the transport. |
|        port | no       |            | Specifies the port to use when building the connection to the remote device. This value applies to either acceptable value of the ``transport`` variable. The port value defaults to the appropriate transport common port if the task does not provide a value (CLI=22, HTTP=80, HTTPS=443). |
|    username | no       |            | Configures the username to use to authenticate the connection to the remote device. The value of this variable authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password to use to authenticate the connection to the remote device. This is a common argument used for either acceptable value of ``transport``. If this key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key is not specified, the device attempts to execute all commands in non-privileged mode. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. |
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this argument does nothing. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. The ``transport`` variable supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```
Dependencies
------------

The dellos-vlt role is built on modules included in the core Ansible code. These modules were added in ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos-vlt role to setup a vlt-domain. This example also creates a ``hosts`` file with the switch, a corresponding ``host_vars`` file, and then a simple playbook that references the dellos-vlt role.


Sample ``hosts`` file:

    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: XXXX
      password: XXXX
      authorize: yes
          auth_pass: XXXX
      transport: cli

Sample ``vars/main.yaml``:

    dellos_vlt:
      leaf1:
        domain: 1
        backupdestination: 192.168.1.1
        priority: 1
        channelid: 101
        vrfname: VLTi-KEEPALIVE
        unitid: 0
        state: present

A simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-vlt

Then run with:

    ansible-playbook -i hosts leaf.yaml

License
-------

Copyright (c) 2017, Dell Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

                     
