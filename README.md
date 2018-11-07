# Ansible Role: Kubernetes

An Ansible Role that installs [Kubernetes](https://kubernetes.io) on Linux.

## Requirements

Requires Docker; recommended role for Docker installation: `geerlingguy.docker`.

## Role Variables

| variable | required | default | describtion |
| --- | --- | --- | --- |
| kubernetes_cluster_name | false | "kubernetes" | kubernetes cluster name |
| kubernetes_master_version | false | "v1.12.0" | This is the version of the kubernetes master components |
| kubernetes_version_rhel_package | false | '1.12.1' | only redhat, version of kubectl,kubeadm,kubelet |
| kubernetes_yum_arch | false | x86_64 | only redhat |
| kubernetes_apt_repo_url | false | http://apt.kubernetes.io/ | only debian, kubernetes repository |
| kubernetes_apt_repo_pool | false | kubernetes-xenial | only debian, kubernetes repository pool |
| kubernetes_apiserver_dns | false | "" | dns-name for kubernetes apiserver |
| kubernetes_apiserver_port | false | "6443" | port of kubernetes apiserver |
| kubernetes_imageRepository | false | "k8s.gcr.io" | docker registry for kubernetes master components |
| kubernetes_certs_dir | false | "/etc/kubernetes/pki" | certs folder |
| kubernetes_log_dir | false | | "/var/log/kubernetes/audit" | log folder |
| kubernetes_log_age | false | 2 | max age of logfiles |
| kubernetes_etcd_data_dir | false | "/var/etcd" | folder for etcd data |
| kubernetes_etcd_image | false | "" | image name for own etcd-container |
| kubernetes_authorization_mode | false | "Node,RBAC" | kubernetes authorization mode |
| kubernetes_enable_admission_plugins | false | | "Initializers,NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota" | kubernetes enabled admission plugins |
| kubernetes_dns_domain | false | "cluster.local" | internal dns domain in kubernetes cluster |
| kubernetes_pod_subnet | false | "10.244.0.0/16" | ipv4 subnet for pods, must be a cidr |
| kubernetes_service_subnet | false | "10.96.0.0/16" | ipv4 subnet vor service, must be a cidr |
| kubernetes_kubelet_extra_args | false | "" | extra arguments for the kubelet daemon |
| kubernetes_packages| false | `- name: kubelet` | the name of the kubernetes packages,
| |  |  `  state: present` | where isntalled from package-manager
| | |  `- name: kubectl` |
| | |  `  state: present` |
| | |  `- name: kubeadm` |
| | |  `  state: present` |
| | |  `- name: kubernetes-cni` |
| | |  `  state: present` |
kubernetes_version_kubeadm | false | 'stable-{{ kubernetes_version }}' | version of kubeadm

## Dependencies

None.

## Example Playbooks

There are some example szenarios with vagrant boxes.

### Single Master-Cluster

```bash
cd ./tests
vagrant up k8s-master-01 k8s-worker-01
cd ..
ansible-playbook -i tests/single_master/inventory tests/single_master/site.yml
```

### HA-Master-Cluster

```bash
cd ./tests
vagrant up k8s-master-01 k8s-master-02 k8s-master-03 k8s-worker-01
cd ..
ansible-playbook -i tests/ha_master/inventory tests/ha_master/site.yml
```

### seperated etcd, HA Master Cluster

```bash
cd ./tests
vagrant up etcd-01 etcd-02 etcd-03 k8s-master-01 k8s-master-02 k8s-master-03 k8s-worker-01
cd ..
ansible-playbook -i tests/seperated_etcd/inventory tests/seperated_etcd/site.yml
```

## License

Apache 2.0

## Author Information

created in 2018 by [André Möller](http://www.andre-moeller.eu/)
