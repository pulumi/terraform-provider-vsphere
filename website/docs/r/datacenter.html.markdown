---
subcategory: "Inventory"
layout: "vsphere"
page_title: "VMware vSphere: vsphere_datacenter"
sidebar_current: "docs-vsphere-resource-inventory-datacenter"
description: |-
  Provides a VMware vSphere datacenter resource. This can be used as the primary container of inventory objects such as hosts and virtual machines.
---

# vsphere\_datacenter

Provides a VMware vSphere datacenter resource. This can be used as the primary
container of inventory objects such as hosts and virtual machines.

## Example Usage

### Create datacenter on the root folder

```hcl
resource "vsphere_datacenter" "prod_datacenter" {
  name       = "my_prod_datacenter"
}
```

### Create datacenter on a subfolder

```hcl
resource "vsphere_datacenter" "research_datacenter" {
  name       = "my_research_datacenter"
  folder     = "/research/"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the datacenter. This name needs to be unique
  within the folder. Forces a new resource if changed.
* `folder` - (Optional) The folder where the datacenter should be created.
  Forces a new resource if changed.
* `tags` - (Optional) The IDs of any tags to attach to this resource.

~> **NOTE:** Tagging support is unsupported on direct ESXi connections and
requires vCenter 6.0 or higher.

* `custom_attributes` - (Optional) Map of custom attribute ids to value 
  strings to set for datacenter resource. See 
  [here][docs-setting-custom-attributes] for a reference on how to set values 
  for custom attributes.

[docs-setting-custom-attributes]: /docs/providers/vsphere/r/custom_attribute.html#using-custom-attributes-in-a-supported-resource

~> **NOTE:** Custom attributes are unsupported on direct ESXi connections 
and require vCenter.

## Attribute Reference

* `id` - The name of this datacenter. This will be changed to the managed
  object ID in v2.0.
* `moid` - Managed object ID of this datacenter.

## Importing 

An existing datacenter can be [imported][docs-import] into this resource
via supplying the full path to the datacenter. An example is below:

[docs-import]: /docs/import/index.html

```
terraform import vsphere_datacenter.dc /dc1
```

The above would import the datacenter named `dc1`.
