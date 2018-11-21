Ansible Role: Kubernetes
==========

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

| Name | Required | Default | Describtion |
|:-----|:---------|:--------|:------------|
| kubernetes_cluster_name | false | "kubernetes" | kubernetes cluster name |
| kubernetes_master_version | false | "v1.12.0" | This is the version of the kubernetes master components |
| kubernetes_version_rhel_package | false | '1.12.1' | only redhat, version of kubectl,kubeadm,kubelet |
| kubernetes_yum_arch | false | x86_64 | only redhat |
| kubernetes_apt_repo_url | false | http://apt.kubernetes.io/ | only debian, kubernetes repository |
| kubernetes_apt_repo_pool | false | kubernetes-xenial | only debian, kubernetes repository pool |
| kubernetes_imageRepository | false | "k8s.gcr.io" | docker registry for kubernetes master components |
| kubernetes_config_dir | false | "/etc/kubernetes" | Config path |
| kubernetes_certs_dir | false | "{{ kubernetes_config_dir }}/pki" | certs folder |
| kubernetes_pod_manifest_path | false | Absoulte path where static pod manifests will be stored | "{{ kubernetes_config_dir }}/manifests" |
| kubernetes_log_dir | false | | "/var/log/kubernetes/audit" | log folder |
| kubernetes_log_age | false | 2 | max age of logfiles |
| kubernetes_authorization_mode | false | "Node,RBAC" | kubernetes authorization mode |
| kubernetes_enable_admission_plugins | false | | "Initializers,NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota" | kubernetes enabled admission plugins |
| kubernetes_dns_domain | false | "cluster.local" | internal dns domain in kubernetes cluster |
| kubernetes_pod_subnet | false | "10.244.0.0/16" | ipv4 subnet for pods, must be a cidr |
| kubernetes_service_subnet | false | "10.96.0.0/16" | ipv4 subnet vor service, must be a cidr |
| kubernetes_kubelet_extra_args | false | "" | extra arguments for the kubelet daemon |
| kubernetes_packages| false | `- name: kubelet` | the name of the kubernetes packages, |
| | |  `  state: present` | where isntalled from package-manager |
| | |  `- name: kubectl` | |
| | |  `  state: present` | |
| | |  `- name: kubeadm` | |
| | |  `  state: present` | |
| | |  `- name: kubernetes-cni` | |
| | |  `  state: present` | |
| kubernetes_version_kubeadm | false | 'stable-{{ kubernetes_version }}' | version of kubeadm |

## API server

| Name | Required | Default | Describtion |
| --- | --- | --- | --- |
| kubernetes_apiserver_dns | false | `""` | dns-name for kubernetes apiserver |
| kubernetes_apiserver_port | false | `6443` | port of kubernetes apiserver |
| kubernetes_apiserver_manifest_file | false | `'{{ kubernetes_pod_manifest_path }}/kube-apiserver.yaml'` | Absolute path to manifest file |

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
| kubernetes_etcd_image | image name for own etcd-container |  |
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

# Example Playbooks

There is an Vagrantfile to create all servers for all szenarios:
[Vagrantfile](./tests/Vagrantfile)
[servers.yml](./tests/servers.yml) (needed for Vagrantfile)

## Single Master-Cluster

[inventory](./tests/single_master/inventory)

[site.yml](./tests/single_master/site.yml)

## HA-Master-Cluster

[inventory](./tests/master_ha/inventory)

[group_vars/k8s_masters](./tests/master_ha/group_vars/k8s_masters)

[site.yml](./tests/master_ha/site.yml)

## seperated etcd, HA Master Cluster

[inventory](./tests/seperated_etcd/inventory)

[group_vars/k8s_masters](./tests/seperated_etcd/group_vars/k8s_masters)

[site.yml](./tests/seperated_etcd/site.yml)

# License

Apache 2.0

## Contributors

- [Thomas Krahn](https://github.com/Nosmoht)

# Author Information

created in 2018 by [André Möller](http://www.andre-moeller.eu/)
