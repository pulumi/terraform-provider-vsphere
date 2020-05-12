---
layout: "vsphere"
page_title: "VMware vSphere: vsphere_tag_category"
sidebar_current: "docs-vsphere-data-source-tag-category"
description: |-
  Provides a vSphere tag category data source. This can be used to reference tag categories not managed by this provider.
---

# vsphere\_tag\_category

The `vsphere_tag_category` data source can be used to reference tag categories
that are not managed by this provider. Its attributes are exactly the same as the
`vsphere_tag_category` resource, and, like importing,
the data source takes a name to search on. The `id` and other attributes are
then populated with the data found by the search.

~> **NOTE:** Tagging support is unsupported on direct ESXi connections and
requires vCenter 6.0 or higher.

## Example Usage

```hcl
data "vsphere_tag_category" "category" {
  name = "test-category"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the tag category.

## Attribute Reference

In addition to the `id` being exported, all of the fields that are available in
the `vsphere_tag_category` resource are also
populated. See that page for further details.
