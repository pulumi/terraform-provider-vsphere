---
layout: "vsphere"
page_title: "VMware vSphere: vsphere_compute_cluster"
sidebar_current: "docs-vsphere-data-source-compute-cluster"
description: |-
  Provides a vSphere cluster data source. This can be used to get the general attributes of a vSphere cluster.
---

# vsphere\_compute\_cluster

The `vsphere_compute_cluster` data source can be used to discover the ID of a
cluster in vSphere. This is useful to fetch the ID of a cluster that you want
to use for virtual machine placement via the
`vsphere_virtual_machine` resource, allowing
you to specify the cluster's root resource pool directly versus using the alias
available through the `vsphere_resource_pool`
data source.

-> You may also wish to see the
`vsphere_compute_cluster` resource for further
details about clusters or how to work with them.

## Example Usage

```hcl
data "vsphere_datacenter" "datacenter" {
  name = "dc1"
}

data "vsphere_compute_cluster" "compute_cluster" {
  name          = "compute-cluster1"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name or absolute path to the cluster.
* `datacenter_id` - (Optional) The managed object reference
  ID of the datacenter the cluster is located in.  This can
  be omitted if the search path used in `name` is an absolute path.  For
  default datacenters, use the id attribute from an empty `vsphere_datacenter`
  data source.

## Attribute Reference

The following attributes are exported:

* `id`: The managed object reference ID of the cluster.
* `resource_pool_id`: The managed object reference ID of
  the root resource pool for the cluster.
