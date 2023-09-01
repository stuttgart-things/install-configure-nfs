# stuttgart-things/install-configure-nfs
This ansible role can set up an NFS server or a client. It can automatically create nfs mounts for clients and also install and configure a server. 

## Example playbooks to use this role

<details><summary>Install this role on your ansible host</summary>

```
cat <<EOF > /tmp/requirements.yaml
roles:
- src: git@codehub.sva.de:Lab/stuttgart-things/supporting-roles/install-requirements.git
  scm: git
- src: git@codehub.sva.de:Lab/stuttgart-things/supporting-roles/install-configure-nfs.git 
  scm: git
- src: git@codehub.sva.de:Lab/stuttgart-things/supporting-roles/download-install-binary
  scm: git
collections:
- name: community.general
  version: 6.3.0
- name: ansible.posix
  version: 1.5.1
EOF
ansible-galaxy install -r /tmp/requirements.yaml --force
```

</details>


<details><summary>Example playbook for an nfs client installation with mount on a netapp</summary>

```
- hosts: all
  gather_facts: true
  become: true
  vars:
    kind: client

    nfs_mnt:
      - name: archive
        permanent: true #false makes no fstab entry
        need_nfs_subfolder: false
        remote_uri: smt-final-2.labul.sva.de:/archive
        mount_dir: /archive
        permissions: 777

  roles:
    - install-configure-nfs
```

**Note: This role requires become yes**
</details>

<details><summary>Example playbook for an nfs server installation with export configuration</summary>

```
- hosts: "nfs"
  gather_facts: true
  become: true
  vars:
    kind: server
    permanent: true
    nfs_manage_firewall: true
    #nfs_custom_exports:  #optional use this or nfs_export_path
    #  - / *(ro,fsid=0) #You can specify more mounts at one time
    #  - /var/lib/docker *(rw,sync,nohide)
    nfs_export_path:      #optional use this or nfs_custom_exports
      - /srv/nfs

  roles:
    - install-configure-nfs
```
**Note: This role requires become yes**

</details>

<details><summary>Example inventory</summary>

```
[nfs]
foo.bar.example.com ansible_user=foobar
```
</details>

<details><summary>Execute playbook</summary>

```
ansible-playbook -i inventory install-configure-nfs.yml
```
</details>




## Role TODOs



## Variables

This role need variables

1. NFS Server
    - kind: (client / server) Client or server installation 
    - permanent: (true [default] / false) save changes after reboot
    - nfs_manage_firewall: (true [default] / false)
    - nfs_exports: (string) exportfs entry
    - remote_uri: 

2. NFS Client
    - kind: (client / server) Client or server installation
    - permanent: (true [default] / false) save changes after reboot
    - remote_uri: (string) nfs uri
    - mount_dir: (string) path for mount nfs
    
## Requirements and Dependencies:
Server and client:
- Ubuntu 20.04
- Ubuntu 18.04
- CentOS 8
- CentOS 7

Role history
----------------
| date  | who | changelog |
|---|---|---|
|2021-28-10  | Marcel Zapf | add NetApp support, added dict in mount creation logic, added support for creating nfs subdirs
|2021-28-02  | Patrick Hermann | updated role structure, doc and requirements
|2020-07-07  | Marcel Zapf | intial commit for this role

License
-------

BSD

Author Information
------------------

Marcel Zapf; 08/2020; SVA GmbH
