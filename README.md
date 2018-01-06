ansible-role-flanneld
=====================

This Ansible playbook is used in [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 7 - Worker](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-7/). It installes flanneld which provides functionality for the Kubernetes pod network (makes it possible for pods on different hosts to communicate).

Requirements
------------

This role must be rolled out before Docker is installed. Additionally etcd must be running (but without that you won't have any part of Kubernetes running anyways ;-) ). During run the playbook will connect to the first node it finds in the `k8s_etcd` group and executes `etcdclt` there to add a new entry into etcd. That entry contains the flannel network config and it is located at "`flannel_etcd_prefix`/config".

Role Variables
--------------

```
# The interface on which the K8s services should listen on. As all cluster
# communication should use the PeerVPN interface the interface name is
# normally "tap0" or "peervpn0".
k8s_interface: "tap0"
# The directory to store the K8s certificates and other configuration
k8s_conf_dir: "/var/lib/kubernetes"
# CNI network plugin settings
k8s_cni_conf_dir: "/etc/cni/net.d"
# The directory from where to copy the K8s certificates. By default this
# will expand to user's LOCAL $HOME (the user that run's "ansible-playbook ..."
# plus "/k8s/certs". That means if the user's $HOME directory is e.g.
# "/home/da_user" then "k8s_ca_conf_directory" will have a value of
# "/home/da_user/k8s/certs".
k8s_ca_conf_directory: "{{ '~/k8s/certs' | expanduser }}"

flannel_version: "v0.9.1"
flannel_etcd_prefix: "/kubernetes-cluster/network"
flannel_ip_range: "10.200.0.0/16"
flannel_backend_type: "vxlan"
flannel_cni_name: "podnet"
flannel_subnet_file_dir: "/run/flannel"
flannel_options_dir: "/etc/flannel"
flannel_bin_dir: "/usr/local/sbin"
flannel_ip_masq: "true"
flannel_cni_conf_file: "10-flannel"
flannel_healthz_ip: "0.0.0.0"
flannel_healthz_port: "0" # 0 = disable
```

Dependencies
------------

https://galaxy.ansible.com/githubixx/etcd/ installed.

Example Playbook
----------------

```
- hosts: k8s:children
  roles:
    - githubixx.kubernetes-flanneld
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)

