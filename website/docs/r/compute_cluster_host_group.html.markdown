---
subcategory: "Host and Cluster Management"
layout: "vsphere"
page_title: "VMware vSphere: vsphere_compute_cluster_host_group"
sidebar_current: "docs-vsphere-resource-compute-cluster-host-group"
description: |-
  Provides a VMware vSphere cluster virtual machine group. This can be used to manage groups of virtual machines for relevant rules in a cluster.
---

# vsphere\_compute\_cluster\_host\_group

The `vsphere_compute_cluster_host_group` resource can be used to manage groups
of hosts in a cluster, either created by the
`vsphere_compute_cluster` resource or looked up
by the `vsphere_compute_cluster` data source.


This resource mainly serves as an input to the
`vsphere_compute_cluster_vm_host_rule`
resource - see the documentation for that resource for further details on how
to use host groups.

~> **NOTE:** This resource requires vCenter and is not available on direct ESXi
connections.

~> **NOTE:** vSphere DRS requires a vSphere Enterprise Plus license.

## Example Usage

```hcl
variable "datacenter" {
  default = "dc1"
}

variable "hosts" {
  default = [
    "esxi1",
    "esxi2",
    "esxi3",
  ]
}

data "vsphere_datacenter" "dc" {
  name = "${var.datacenter}"
}

data "vsphere_host" "hosts" {
  count         = "${length(var.hosts)}"
  name          = "${var.hosts[count.index]}"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}

resource "vsphere_compute_cluster" "compute_cluster" {
  name            = "compute-cluster-test"
  datacenter_id   = "${data.vsphere_datacenter.dc.id}"
  host_system_ids = ["${data.vsphere_host.hosts.*.id}"]

  drs_enabled          = true
  drs_automation_level = "fullyAutomated"

  ha_enabled = true
}

resource "vsphere_compute_cluster_host_group" "cluster_host_group" {
  name                = "test-cluster-host-group"
  compute_cluster_id  = "${vsphere_compute_cluster.compute_cluster.id}"
  host_system_ids     = ["${data.vsphere_host.hosts.*.id}"]
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the host group. This must be unique in the
  cluster. Forces a new resource if changed.
* `compute_cluster_id` - (Required) The managed object reference
  ID of the cluster to put the group in.  Forces a new
  resource if changed.

* `host_system_ids` - (Optional) The managed object IDs of
  the hosts to put in the cluster.

~> **NOTE:** The namespace for cluster names on this resource (defined by the
[`name`](#name) argument) is shared with the
`vsphere_compute_cluster_vm_group`
resource. Make sure your names are unique across both resources.

## Attribute Reference

The only attribute this resource exports is the `id` of the resource, which is
a combination of the managed object reference ID of the
cluster, and the name of the host group.

## Importing

An existing group can be [imported][docs-import] into this resource by
supplying both the path to the cluster, and the name of the host group. If the
name or cluster is not found, or if the group is of a different type, an error
will be returned. An example is below:

[docs-import]: https://www.terraform.io/docs/import/index.html

```
terraform import vsphere_compute_cluster_host_group.cluster_host_group \
  '{"compute_cluster_path": "/dc1/host/cluster1", \
  "name": "terraform-test-cluster-host-group"}'
```
