---
page_title: "cloudflare_split_tunnel Resource - Cloudflare"
subcategory: ""
description: |-
  Provides a Cloudflare Split Tunnel resource. Split tunnels are used to either
  include or exclude lists of routes from the WARP client's tunnel.
---

# cloudflare_split_tunnel (Resource)

Provides a Cloudflare Split Tunnel resource. Split tunnels are used to either
include or exclude lists of routes from the WARP client's tunnel.

## Example Usage

```terraform
# Excluding *.example.com from WARP routes
resource "cloudflare_split_tunnel" "example_split_tunnel_exclude" {
  account_id = "f037e56e89293a057740de681ac9abbe"
  mode       = "exclude"
  tunnels {
    host        = "*.example.com"
    description = "example domain"
  }
}

# Including *.example.com in WARP routes
resource "cloudflare_split_tunnel" "example_split_tunnel_include" {
  account_id = "f037e56e89293a057740de681ac9abbe"
  mode       = "include"
  tunnels {
    host        = "*.example.com"
    description = "example domain"
  }
}

# Create a device policy
resource "cloudflare_device_policy" "developer_warp_policy" {
  account_id    = "f037e56e89293a057740de681ac9abbe"
  name          = "Developers"
  precedence    = 10
  match         = "any(identity.groups.name[*] in {\"Developers\"})"
  switch_locked = true
}

# Excluding *.example.com from WARP routes for a particular device policy
resource "cloudflare_split_tunnel" "example_device_policy_split_tunnel_exclude" {
  account_id = "f037e56e89293a057740de681ac9abbe"
  policy_id  = cloudflare_device_policy.developer_warp_policy.id
  mode       = "exclude"
  tunnels {
    host        = "*.example.com"
    description = "example domain"
  }
}

# Including *.example.com in WARP routes for a particular device policy
resource "cloudflare_split_tunnel" "example_split_tunnel_include" {
  account_id = "f037e56e89293a057740de681ac9abbe"
  policy_id  = cloudflare_device_policy.developer_warp_policy.id
  mode       = "include"
  tunnels {
    host        = "*.example.com"
    description = "example domain"
  }
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `account_id` (String) The account identifier to target for the resource.
- `mode` (String) The mode of the split tunnel policy. Available values: `include`, `exclude`.
- `tunnels` (Block Set, Min: 1) The value of the tunnel attributes. (see [below for nested schema](#nestedblock--tunnels))

### Optional

- `policy_id` (String) The settings policy for which to configure this split tunnel policy.

### Read-Only

- `id` (String) The ID of this resource.

<a id="nestedblock--tunnels"></a>
### Nested Schema for `tunnels`

Optional:

- `address` (String) The address for the tunnel.
- `description` (String) A description for the tunnel.
- `host` (String) The domain name for the tunnel.

## Import

Import is supported using the following syntax:

```shell
# Split Tunnels for default device policies must use "default" as the policy ID.
$ terraform import cloudflare_split_tunnel.example <account_id>/<policy_id>/<mode>
```