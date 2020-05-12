---
layout: "vsphere"
page_title: "VMware vSphere: vsphere_datastore_cluster"
sidebar_current: "docs-vsphere-data-source-cluster-datastore"
description: |-
  Provides a vSphere datastore cluster data source. This can be used to get the general attributes of a vSphere datastore cluster.
---

# vsphere\_datastore\_cluster

The `vsphere_datastore_cluster` data source can be used to discover the ID of a
datastore cluster in vSphere. This is useful to fetch the ID of a datastore
cluster that you want to use to assign datastores to using the
`vsphere_nas_datastore` or
`vsphere_vmfs_datastore` resources, or create
virtual machines in using the
`vsphere_virtual_machine` resource. 

## Example Usage

```hcl
data "vsphere_datacenter" "datacenter" {
  name = "dc1"
}

data "vsphere_datastore_cluster" "datastore_cluster" {
  name          = "datastore-cluster1"
  datacenter_id = "${data.vsphere_datacenter.dc.id}"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name or absolute path to the datastore cluster.
* `datacenter_id` - (Optional) The managed object reference
  ID of the datacenter the datastore cluster is located in.
  This can be omitted if the search path used in `name` is an absolute path.
  For default datacenters, use the id attribute from an empty
  `vsphere_datacenter` data source.

## Attribute Reference

Currently, the only exported attribute from this data source is `id`, which
represents the ID of the datastore cluster that was looked up.
