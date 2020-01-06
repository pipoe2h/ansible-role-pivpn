PiVPN
=========

This role install and configures OpenVPN in a Raspberry Pi(s) running Raspbian Stretch+.

Requirements
------------

This role requires Ansible 2.9 or higher and a clean installation of Raspbian Stretch+.
Eth0 should be configured with a static IP or DHCP reservation (things will break if it the IP changes).

TODO
--------------
  * I've left in the original defaults although they are largely redundant - as I have multiple locations installed
  * iptables defaults have not been done yet.
  * I have not got around to calling the pivpn scripts to create a default session(s) although the stubbing is there.
  * I have no synced to upstream pivpn project in a bit for wireguard Support

PRs welcome.

Role Variables
--------------
The variables that can be passed to this role and a brief description about
them are as follows.

([defaults/main.yml](defaults/main.yml))

```yaml
certificate:
  key_size: 1024 # If you are paranoid you can change for 2048
  key_country: "ES" # Country Name (2 letter code)
  key_province: "Seville" # State or Province Name (full name)
  key_city: "" # Locality Name (eg, city)
  key_org: "ACME Ltd." # Organization Name (eg, company)
  key_email: "" # Email Address
  key_ou: "" # Organizational Unit Name (eg, section)


  openVPN:
   server:
      protocol: udp # UDP is recommended. You can change fot TCP.
      port: 1194 # This is the default OpenVPN port. Remember open this port in your router to allow the VPN connection from Internet.
  ## FIXME: This should accept a group based subnet by building
      server_subnet: {{ openvpn_transport_subnet }} # The subnet you want to use for the OpenVPN clients
      server_netmask: {{ openvpn_transport_subnet_netmask }} # The netmask for the OpenVPN client subnet
  ## FIXME Support larger groups
      server_tun0: {{ openvpn_transport_ip }} # The IP for the OpenVPN tunnel interface
      server_tun0_ptp: {{ openvpn_transport_ip_ptp }}  # The IP for the OpenVPN tunnel point-to-point alias
  ## FIXME: This should accept a group based subnet by building
      local_subnet: {{ openvpn_local_subnet }} # The local subnet where the Raspberry Pi is connected
      local_netmask: {{ openvpn_local_netmask }} # The local netmask for the Raspberry Pi subnet
      dns_ip_1: 8.8.8.8 # If your router does not do DNS, you can use Google DNS 8.8.8.8
      dns_ip_2: 1.1.1.1 # If your router does not do DNS, you can use Google DNS 8.8.8.8

```

Dependencies
------------
Some variables are purposefully set outside of the defaults* via group_vars or host_vars at the playbook level. Those variables are:

```
openVPN_key_length:
openVPN_client_username: " {{ansible_user}} "
openVPN_client_password:
openVPN_server_port:
openvpn_transport_subnet: 10.8.6.0
openvpn_transport_subnet_netmask: 255.255.255.0
openvpn_transport_ip: 10.8.6.1
openvpn_transport_ip_ptp: 10.8.6.2
openvpn_local_subnet: 192.168.1.0
openvpn_local_netmask: 255.255.255.0
```

Example Playbook
----------------

If you follow ansible-best practices (and note the Dependencies above), then the following should work-out-of-the box.

```
---
- hosts:  "{{ hostlist }}"
  remote_user: "{{ local_ssh_user }}"
  become: true
  roles:
     - role: ansible-role-pivpn
```

```
$ ansible-playbook -i hosts playbook.yml -e 'hostlist=<GroupOrHost>'

```
License
-------
MIT

Author Information
------------------
* Update(s) to integration: | [Nick Lange](https://github.com/NickJLange) | [LinkedIn](https://www.linkedin.com/in/nicklange/) | [twitter](https://twitter.com/nickjlange)
* Original Author: [Jose Gomez](https://github.com/pipoe2h) | [www](http://www.joseluisgomez.com) | [twitter](http://twitter.com/pipoe2h)
