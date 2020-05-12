---
subcategory: "Host and Cluster Management"
layout: "vsphere"
page_title: "VMware vSphere: vsphere_compute_cluster_vm_anti_affinity_rule"
sidebar_current: "docs-vsphere-resource-compute-cluster-vm-anti-affinity-rule"
description: |-
  Provides a VMware vSphere cluster virtual machine anti-affinity rule. This can be used to manage rules to tell virtual machines to run on separate hosts.
---

# vsphere\_compute\_cluster\_vm\_anti\_affinity\_rule

The `vsphere_compute_cluster_vm_anti_affinity_rule` resource can be used to
manage VM anti-affinity rules in a cluster, either created by the
`vsphere_compute_cluster` resource or looked up
by the `vsphere_compute_cluster` data source.

This rule can be used to tell a set to virtual machines to run on different
hosts within a cluster, useful for preventing single points of failure in
application cluster scenarios. When configured, DRS will make a best effort to
ensure that the virtual machines run on different hosts, or prevent any
operation that would keep that from happening, depending on the value of the
[`mandatory`](#mandatory) flag.

-> Keep in mind that this rule can only be used to tell VMs to run separately
on _non-specific_ hosts - specific hosts cannot be specified with this rule.
For that, see the
`vsphere_compute_cluster_vm_host_rule`
resource.

~> **NOTE:** This resource requires vCenter and is not available on direct ESXi
connections.

~> **NOTE:** vSphere DRS requires a vSphere Enterprise Plus license.

## Example Usage

The example below creates two virtual machines in a cluster using the
`vsphere_virtual_machine` resource, creating the
virtual machines in the cluster looked up by the
`vsphere_compute_cluster` data source. It
then creates an anti-affinity rule for these two virtual machines, ensuring
they will run on different hosts whenever possible.

```hcl
data "vsphere_datacenter" "dc" {
  name = "dc1"
}

data "vsphere_datastore" "datastore" {
  name          = "datastore1"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}

data "vsphere_compute_cluster" "cluster" {
  name          = "cluster1"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}

data "vsphere_network" "network" {
  name          = "network1"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}

resource "vsphere_virtual_machine" "vm" {
  count            = 2
  name             = "test-${count.index}"
  resource_pool_id = "${data.vsphere_compute_cluster.cluster.resource_pool_id}"
  datastore_id     = "${data.vsphere_datastore.datastore.id}"

  num_cpus = 2
  memory   = 2048
  guest_id = "other3xLinux64Guest"

  network_interface {
    network_id = "${data.vsphere_network.network.id}"
  }

  disk {
    label = "disk0"
    size  = 20
  }
}

resource "vsphere_compute_cluster_vm_anti_affinity_rule" "cluster_vm_anti_affinity_rule" {
  name                = "test-cluster-vm-anti-affinity-rule"
  compute_cluster_id  = "${data.vsphere_compute_cluster.cluster.id}"
  virtual_machine_ids = ["${vsphere_virtual_machine.vm.*.id}"]
}
```

## Argument Reference

The following arguments are supported:

* `compute_cluster_id` - (Required) The managed object reference
  ID of the cluster to put the group in.  Forces a new
  resource if changed.

* `name` - (Required) The name of the rule. This must be unique in the cluster.
* `virtual_machine_ids` - (Required) The UUIDs of the virtual machines to run
  on hosts different from each other.
* `enabled` - (Optional) Enable this rule in the cluster. Default: `true`.
* `mandatory` - (Optional) When this value is `true`, prevents any virtual
  machine operations that may violate this rule. Default: `false`.

~> **NOTE:** The namespace for rule names on this resource (defined by the
[`name`](#name) argument) is shared with all rules in the cluster - consider
this when naming your rules.

## Attribute Reference

The only attribute this resource exports is the `id` of the resource, which is
a combination of the managed object reference ID of the
cluster, and the rule's key within the cluster configuration.

## Importing

An existing rule can be [imported][docs-import] into this resource by supplying
both the path to the cluster, and the name the rule. If the name or cluster is
not found, or if the rule is of a different type, an error will be returned. An
example is below:

[docs-import]: https://www.terraform.io/docs/import/index.html

```
terraform import vsphere_compute_cluster_vm_anti_affinity_rule.cluster_vm_anti_affinity_rule \
  '{"compute_cluster_path": "/dc1/host/cluster1", \
  "name": "terraform-test-cluster-vm-anti-affinity-rule"}'
```
