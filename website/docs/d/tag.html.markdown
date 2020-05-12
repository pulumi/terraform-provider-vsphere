---
layout: "vsphere"
page_title: "VMware vSphere: vsphere_tag"
sidebar_current: "docs-vsphere-data-source-tag-data-source"
description: |-
  Provides a vSphere tag data source. This can be used to reference tags not managed by this provider.
---

# vsphere\_tag

The `vsphere_tag` data source can be used to reference tags that are not
managed by this provider. Its attributes are exactly the same as the `vsphere_tag`
resource, and, like importing, the data source takes a name and
category to search on. The `id` and other attributes are then populated with
the data found by the search.

~> **NOTE:** Tagging support is unsupported on direct ESXi connections and
requires vCenter 6.0 or higher.

## Example Usage

```hcl
data "vsphere_tag_category" "category" {
  name = "test-category"
}

data "vsphere_tag" "tag" {
  name        = "test-tag"
  category_id = "${data.vsphere_tag_category.category.id}"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the tag.
* `category_id` - (Required) The ID of the tag category the tag is located in.

## Attribute Reference

In addition to the `id` being exported, all of the fields that are available in
the `vsphere_tag` resource are also populated. See that page
for further details.
