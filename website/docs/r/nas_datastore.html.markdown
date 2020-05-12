---
subcategory: "Storage"
layout: "vsphere"
page_title: "VMware vSphere: vsphere_nas_datastore"
sidebar_current: "docs-vsphere-resource-storage-nas-datastore"
description: |-
  Provides a vSphere NAS datastore resource. This can be used to mount a NFS share as a datastore on a host.
---

# vsphere\_nas\_datastore

The `vsphere_nas_datastore` resource can be used to create and manage NAS
datastores on an ESXi host or a set of hosts. The resource supports mounting
NFS v3 and v4.1 shares to be used as datastores.

~> **NOTE:** Unlike `vsphere_vmfs_datastore`, a NAS
datastore is only mounted on the hosts you choose to mount it on. To mount on
multiple hosts, you must specify each host that you want to add in the
`host_system_ids` argument.

## Example Usage

The following example would set up a NFS v3 share on 3 hosts connected through
vCenter in the same datacenter - `esxi1`, `esxi2`, and `esxi3`. The remote host
is named `nfs` and has `/export/test` exported.

```hcl
variable "hosts" {
  default = [
    "esxi1",
    "esxi2",
    "esxi3",
  ]
}

data "vsphere_datacenter" "datacenter" {}

data "vsphere_host" "esxi_hosts" {
  count         = "${length(var.hosts)}"
  name          = "${var.hosts[count.index]}"
  datacenter_id = "${data.vsphere_datacenter.datacenter.id}"
}

resource "vsphere_nas_datastore" "datastore" {
  name            = "test"
  host_system_ids = ["${data.vsphere_host.esxi_hosts.*.id}"]

  type         = "NFS"
  remote_hosts = ["nfs"]
  remote_path  = "/export/test"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the datastore. Forces a new resource if
  changed.
* `host_system_ids` - (Required) The managed object IDs of
  the hosts to mount the datastore on.
* `type` - (Optional) The type of NAS volume. Can be one of `NFS` (to denote
  v3) or `NFS41` (to denote NFS v4.1). Default: `NFS`. Forces a new resource if
  changed.
* `remote_hosts` - (Required) The hostnames or IP addresses of the remote
  server or servers. Only one element should be present for NFS v3 but multiple
  can be present for NFS v4.1. Forces a new resource if changed.
* `remote_path` - (Required) The remote path of the mount point. Forces a new
  resource if changed.
* `access_mode` - (Optional) Access mode for the mount point. Can be one of
  `readOnly` or `readWrite`. Note that `readWrite` does not necessarily mean
  that the datastore will be read-write depending on the permissions of the
  actual share. Default: `readWrite`. Forces a new resource if changed.
* `security_type` - (Optional) The security type to use when using NFS v4.1.
  Can be one of `AUTH_SYS`, `SEC_KRB5`, or `SEC_KRB5I`. Forces a new resource
  if changed.
* `folder` - (Optional) The relative path to a folder to put this datastore in.
  This is a path relative to the datacenter you are deploying the datastore to.
  Example: for the `dc1` datacenter, and a provided `folder` of `foo/bar`,
  The provider will place a datastore named `test` in a datastore folder
  located at `/dc1/datastore/foo/bar`, with the final inventory path being
  `/dc1/datastore/foo/bar/test`. Conflicts with
  `datastore_cluster_id`.
* `datastore_cluster_id` - (Optional) The managed object
  ID of a datastore cluster to put this datastore in.
  Conflicts with `folder`.
* `tags` - (Optional) The IDs of any tags to attach to this resource. 

~> **NOTE:** Tagging support is unsupported on direct ESXi connections and
requires vCenter 6.0 or higher.

* `custom_attributes` - (Optional) Map of custom attribute ids to attribute 
  value strings to set on datasource resource. 
  
~> **NOTE:** Custom attributes are unsupported on direct ESXi connections 
and require vCenter.

## Attribute Reference

The following attributes are exported:

* `id` - The managed object reference ID of the datastore.
* `accessible` - The connectivity status of the datastore. If this is `false`,
  some other computed attributes may be out of date.
* `capacity` - Maximum capacity of the datastore, in megabytes.
* `free_space` - Available space of this datastore, in megabytes.
* `maintenance_mode` - The current maintenance mode state of the datastore.
* `multiple_host_access` - If `true`, more than one host in the datacenter has
  been configured with access to the datastore.
* `uncommitted_space` - Total additional storage space, in megabytes,
  potentially used by all virtual machines on this datastore.
* `url` - The unique locator for the datastore.
* `protocol_endpoint` - Indicates that this NAS volume is a protocol endpoint.
  This field is only populated if the host supports virtual datastores. 

## Importing

An existing NAS datastore can be [imported][docs-import] into this resource via
its managed object ID, via the following command:

[docs-import]: https://www.terraform.io/docs/import/index.html

```
terraform import vsphere_nas_datastore.datastore datastore-123
```

You need a tool like [`govc`][ext-govc] that can display managed object IDs.

[ext-govc]: https://github.com/vmware/govmomi/tree/master/govc

In the case of govc, you can locate a managed object ID from an inventory path
by doing the following:

```
$ govc ls -i /dc/datastore/terraform-test
Datastore:datastore-123
```
