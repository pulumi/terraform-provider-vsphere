---
layout: "vsphere"
page_title: "VMware vSphere: vsphere_custom_attribute"
sidebar_current: "docs-vsphere-data-source-custom-attriubte"
description: |-
  Provides a vSphere custom attribute data source. This can be used to reference custom attributes not managed by this provider.
---

# vsphere\_custom\_attribute

The `vsphere_custom_attribute` data source can be used to reference custom 
attributes that are not managed by this provider. Its attributes are exactly the 
same as the `vsphere_custom_attribute` resource, 
and, like importing, the data source takes a name to search on. The `id` and 
other attributes are then populated with the data found by the search.

~> **NOTE:** Custom attributes are unsupported on direct ESXi connections 
and require vCenter.

## Example Usage

```hcl
data "vsphere_custom_attribute" "attribute" {
  name = "test-attribute"
}
```

## Argument Reference

* `name` - (Required) The name of the custom attribute.

## Attribute Reference

In addition to the `id` being exported, all of the fields that are available in 
the `vsphere_custom_attribute` resource are also 
populated. See that page for further details.
