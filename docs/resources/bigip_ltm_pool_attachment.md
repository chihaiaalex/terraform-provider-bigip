---
layout: "bigip"
page_title: "BIG-IP: bigip_ltm_pool_attachment"
sidebar_current: "docs-bigip-resource-pool-attachment-x"
description: |-
    Provides details about bigip_ltm_pool_attachment resource
---

# bigip\_ltm\_pool\_attachment

`bigip_ltm_pool_attachment` Manages nodes membership in pools

## Example Usage

```hcl
resource "bigip_ltm_monitor" "monitor" {
  name     = "/Common/terraform_monitor"
  parent   = "/Common/http"
  send     = "GET /some/path\r\n"
  timeout  = "999"
  interval = "998"
}
resource "bigip_ltm_pool" "pool" {
  name                = "/Common/terraform-pool"
  load_balancing_mode = "round-robin"
  monitors            = ["${bigip_ltm_monitor.monitor.name}"]
  allow_snat          = "yes"
  allow_nat           = "yes"
}

resource "bigip_ltm_pool_attachment" "attach_node" {
  pool                  = bigip_ltm_pool.pool.name
  node                  = "1.1.1.1:80"
  ratio                 = 2
  connection_limit      = 2
  connection_rate_limit = 2
  priority_group        = 2
  dynamic_ratio         = 3
}

```      

## Argument Reference

* `pool` - (Required) Name of the pool to which members should be attached,it should be "full path".The full path is the combination of the partition + name of the pool.(For example `/Common/my-pool`)

* `node` - (Required) Pool member address/fqdn with service port, (ex: `1.1.1.1:80/www.google.com:80`). (Note: Member will be in same partition of Pool)

* `connection_limit` - (Optional) Specifies a maximum established connection limit for a pool member or node.The default is 0, meaning that there is no limit to the number of connections.

* `connection_rate_limit` - (Optional) Specifies the maximum number of connections-per-second allowed for a pool member,The default is 0.

* `dynamic_ratio` - (Optional) Specifies the fixed ratio value used for a node during ratio load balancing.

* `ratio`- (Optional) "Specifies the ratio weight to assign to the pool member. Valid values range from 1 through 65535. The default is 1, which means that each pool member has an equal ratio proportion.".

* `priority_group` - (Optional) Specifies a number representing the priority group for the pool member. The default is 0, meaning that the member has no priority

* `fqdn_autopopulate` - (Optional) Specifies whether the system automatically creates ephemeral nodes using the IP addresses returned by the resolution of a DNS query for a node defined by an FQDN. The default is enabled

## Importing
An existing pool attachment (i.e. pool membership) can be imported into this resource by supplying both the pool full path, and the node full path with the relevant port. If the pool or node membership is not found, an error will be returned. An example is below:

```sh
$ terraform import bigip_ltm_pool_attachment.node-pool-attach \
	'{"pool": "/Common/terraform-pool", "node": "/Common/node1:80"}'
```