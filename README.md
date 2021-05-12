# ansible-dns

This builds a primary and secondary dns server. It assumes the following:

1. You're using CentOS7 or another RedHat forked distro. Only tested on CentOS7.
2. You plan on configuring only one primary and one secondary DNS server (for now).

Before running playbook:

1. You'll need to go into defaults/main.yml and edit the following variables to reflect what your environment will look like
- network_range: "192.168.50.0/24"
- domain: "special.localdomain"
- primary_ipv4: "192.168.50.231"
- secondary_ipv4: "192.168.50.232"
- server_name: "ns01"

2. You'll need to set up the playbook so it runs through the role twice. First time should be for Primary, second time for Secondary:
- NOTE: PLAYBOOK BEST VIEWED IN RAW MODE
  ---
  - hosts: dns_primary
    become: yes
    tasks:  
    - include_role:
        name: dns
      vars:   
        server_role: primary

  - hosts: dns_secondary
    become: true
    tasks:  
    - include_role:
        name: dns
      vars:   
        server_role: secondary

3. Ensure your hosts file reflect the hosts in the playbook

  [dns_primary]
  192.168.50.231

  [dns_secondary]
  192.168.50.232

NOTE:
- Primary server CANNOT be queried.
- Clients will need to be configured to query the secondary.
- Unless modified, network_range specifies the IPs that can query the secondary dns server.
- The secondary chroots the dns environment. This is for security purposes.
- You must know what you're doing when updating the zone files.

TO FIX
