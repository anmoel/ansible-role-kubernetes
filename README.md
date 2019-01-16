Ansible Role: Kubernetes
==========

[![pipeline status](https://gitlab.com/anmoel/ansible-role-kubernetes/badges/master/pipeline.svg)](https://gitlab.com/anmoel/ansible-role-kubernetes/commits/master)
[![coverage report](https://gitlab.com/anmoel/ansible-role-kubernetes/badges/master/coverage.svg)](https://gitlab.com/anmoel/ansible-role-kubernetes/commits/master)

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Introduction](#introduction)
- [Requirements](#requirements)
- [Dependencies](#dependencies)
- [Variables](#variables)
	- [Common](#common)
	- [API server](#api-server)
	- [Etcd](#etcd)
	- [Controller Manager](#controller-manager)
	- [kubeadm](#kubeadm)
	- [Scheduler](#scheduler)
- [Example Playbooks](#example-playbooks)
	- [Single Master-Cluster](#single-master-cluster)
	- [HA-Master-Cluster](#ha-master-cluster)
	- [seperated etcd, HA Master Cluster](#seperated-etcd-ha-master-cluster)
- [License](#license)
- [contributors](#contributors)
- [Author Information](#author-information)

<!-- /TOC -->

# Introduction

An Ansible Role that installs [Kubernetes](https://kubernetes.io) on Linux.

# Requirements

- Docker; recommended role for Docker installation: `geerlingguy.docker`.
- Loadbalancer or keepalived for HA; recommended role for keepalived installations: `evrardjp.keepalived`

# Dependencies

- Python library Docker

# Variables

## Common

| Name | Default | Describtion |
|:-----|:--------|:------------|
| kubernetes_cluster_name | "kubernetes" | kubernetes cluster name |
| kubernetes_master_version | "v1.13.2" | This is the version of the kubernetes master components |
| kubernetes_version_rhel_package | '1.13.2' | only redhat, version of kubectl,kubeadm,kubelet |
| kubernetes_yum_arch | x86_64 | only redhat |
| kubernetes_apt_repo_url | http://apt.kubernetes.io/ | only debian, kubernetes repository |
| kubernetes_apt_repo_pool | kubernetes-xenial | only debian, kubernetes repository pool |
| kubernetes_useHyperKubeImage | "false" | switch to change control-plane docker image to hyperkube |
| kubernetes_dns_type | "CoreDNS" | dns plugin in kubernetes, could be: "kube-dns" or "CoreDNS" |
| kubernetes_imageRepository | "k8s.gcr.io" | docker registry for kubernetes master components |
| kubernetes_config_dir | "/etc/kubernetes" | Config path |
| kubernetes_certs_dir | "{{ kubernetes_config_dir }}/pki" | certs folder |
| kubernetes_pod_manifest_path | Absoulte path where static pod manifests will be stored | "{{ kubernetes_config_dir }}/manifests" |
| kubernetes_log_dir | "{{ kubernetes_log_dir }}/audit" | log folder |
| kubernetes_audit_log_dir | "/var/log/kubernetes/audit" | audit log folder |
| kubernetes_log_age | 2 | max age of logfiles |
| kubernetes_authorization_mode | "Node,RBAC" | kubernetes authorization mode |
| kubernetes_enable_admission_plugins | "Initializers,NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota" | kubernetes enabled admission plugins |
| kubernetes_dns_domain | "cluster.local" | internal dns domain in kubernetes cluster |
| kubernetes_pod_subnet | "10.244.0.0/16" | ipv4 subnet for pods, must be a cidr |
| kubernetes_service_subnet | "10.96.0.0/16" | ipv4 subnet vor service, must be a cidr |
| kubernetes_kubelet_extra_args | "" | extra arguments for the kubelet daemon |
| kubernetes_packages| false | `- name: kubelet` | the name of the kubernetes packages, |
| | |  `  state: present` | where isntalled from package-manager |
| | |  `- name: kubectl` | |
| | |  `  state: present` | |
| | |  `- name: kubeadm` | |
| | |  `  state: present` | |
| | |  `- name: kubernetes-cni` | |
| | |  `  state: present` | |
| kubernetes_version_kubeadm | 'stable-{{ kubernetes_version }}' | version of kubeadm |

## API server

| Name | Default | Describtion |
| --- | --- | --- |
| kubernetes_apiserver_dns | `""` | dns-name for kubernetes apiserver |
| kubernetes_apiserver_ip | - | virtual ip of kubernetes apiserver loadbalancer |
| kubernetes_apiserver_port | `6443` | port of kubernetes apiserver |
| kubernetes_apiserver_manifest_file | `'{{ kubernetes_pod_manifest_path }}/kube-apiserver.yaml'` | Absolute path to manifest file |

## Etcd

| Name | Description | Default |
| --- | --- | --- |
| kubernetes_etcd_certs_dir | Path to store Etcd certificates | `'{{ kubernetes_certs_dir }}/etcd'` |
| kubernetes_etcd_ca_cert_file | Etcd root CA certificate file | `'{{ kubernetes_etcd_certs_dir }}/ca.crt'` |
| kubernetes_etcd_ca_key_file | Etcd root CA key file | `'{{ kubernetes_etcd_certs_dir }}/ca.key'` |
| kubernetes_etcd_server_cert_file | Etcd server certificate file | `'{{ kubernetes_etcd_certs_dir }}/server.crt'` |
| kubernetes_etcd_server_key_file | Etcd server key file | `'{{ kubernetes_etcd_certs_dir }}/server.key'` |
| kubernetes_etcd_peer_cert_file | | `'{{ kubernetes_etcd_certs_dir }}/peer.crt'` |
| kubernetes_etcd_peer_key_file | | `'{{ kubernetes_etcd_certs_dir }}/peer.key'` |
| kubernetes_etcd_healthcheck_client_cert_file | | `'{{ kubernetes_etcd_certs_dir }}/healthcheck-client.crt'` |
| kubernetes_etcd_healthcheck_client_key_file | | `'{{ kubernetes_etcd_certs_dir }}/healthcheck-client.key'` |
| kubernetes_etcd_apiserver_client_cert_file | | `'{{ kubernetes_certs_dir }}/apiserver-etcd-client.crt'` |
| kubernetes_etcd_apiserver_client_key_file | | `'{{ kubernetes_certs_dir }}/apiserver-etcd-client.key'` |
| kubernetes_etcd_manifest_file | | `'{{ kubernetes_pod_manifest_path }}/etcd.yaml'` |
| kubernetes_etcd_data_dir | folder for etcd data | `/var/etcd` |  |
| kubernetes_etcd_server_port | Etcd server communication port |  `2380` |
| kubernetes_etcd_client_port | Etcd client communication port | `2379` |

## Controller Manager

| Name | Description | Default |
| --- | --- | --- |
| kubernetes_controller_manager_manifest_file | Absolute path to manifest file | `'{{ kubernetes_pod_manifest_path }}/kube-controller-manager.yaml'`  |

## kubeadm

| Name | Description | Default |
| --- | --- | --- |
| kubernetes_kubeadm_config_file_path | Path to store kubeadm config file | `'{{ kubernetes_config_dir }}/kubeadm'` |
| kubernetes_kubeadm_config_file_name | Name of kubeadm config file | `clusterconfig.yaml` |

## Scheduler

| Name | Description | Default |
| --- | --- | --- |
| kubernetes_scheduler_manifest_file | Absolute path to manifest file | `'{{ kubernetes_pod_manifest_path }}/kube-scheduler.yaml'` |

## Cloud Provider

| Name | Description | Default |
| --- | --- | --- |
| kubernetes_cloud_provider | choose supported cloudprovider, values: "" or "vsphere" | ""
| kubernetes_cloud_config_file | path for cloud_config file | "{{ kubernetes_config_dir }}/{{ kubernetes_cloud_provider }}.conf"
| kubernetes_cloud_vsphere_workspace_server | option server in area \[workspace\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_workspace_datacenter | option datacenter in area \[workspace\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_workspace_default_datastore | option default_datastore in area \[workspace\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_workspace_folder | option folder in area \[workspace\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_default_user | option user in the area \[global\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_default_password | option password in the area \[global\] of the cloud_config (only vsphere) | ""
| kubernetes_cloud_vsphere_datacenters | list of datacenters with the attributes: server,datatcenters,username,password of the cloud_config (only vsphere) | []
| kubernetes_cloud_vsphere_network_options | all options in the area \[network\] of the cloud_config (only vsphere) | []
| kubernetes_cloud_vsphere_disk_options | all options in the area \[disk\] of the cloud_config (only vsphere) | []

# Example Playbooks

There is an Vagrantfile to create all servers for all szenarios:
[Vagrantfile](./tests/vagrant/Vagrantfile)
[servers.yml](./tests/vagrant/servers.yml) (needed for Vagrantfile)

## AllinOne

[inventory](./tests/allinone/inventory)

[site.yml](./tests/site.yml)

## Single Master-Cluster

[inventory](./tests/single_master/inventory)

[site.yml](./tests/site.yml)

## seperated etcd, single Master Cluster

[inventory](./tests/seperated_etcd/inventory)

[site.yml](./tests/site.yml)

## HA-Master-Cluster

[inventory](./tests/master_ha/inventory)

[site.yml](./tests/site.yml)

## seperated etcd, HA Master Cluster

[inventory](./tests/ha_extended/inventory)

[site.yml](./tests/site.yml)

# License

Apache 2.0

## Contributors

- [Thomas Krahn](https://github.com/Nosmoht)
- [Björn Jessen-Noak](https://github.com/oswalya)

# Author Information

created in 2018 by [André Möller](http://www.andre-moeller.eu/)
