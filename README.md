# Terraform - DigitalOcean basic firewall rule set

Terraform module to configure a basic default set of firewall rules on DigitalOcean, based on personal preferences. It creates outbound rules for http, https, dns, ntp and ssh traffic, and inbound rules for ssh, with various configuration options.

[![CircleCI](https://circleci.com/gh/cpxPratik/terraform-digitalocean-firewall-rules/tree/master.svg?style=svg)](https://circleci.com/gh/cpxPratik/terraform-digitalocean-firewall-rules/tree/master)

- [Requirements](#requirements)
- [Usage](#usage)
- [Firewall rules](#firewall-rules)

---

## Requirements

- Terraform = v0.12.x
- Digitalocean account / API token with write access

## Usage

Basic usage example:

```hcl
provider "digitalocean" {
    token = var.do_token
    version = "~> 1.4"
}

resource "digitalocean_tag" "environment" {
    name = "development"
}

module "default-firewall" {
    source   = "github.com/cpxpratik/terraform-digitalocean-firewall-rules.git?ref=master"
    do_token = var.do_token

    prefix = "dev"
    tags   = [digitalocean_tag.environment.id]
}
```

Disable ssh outbound connections:

```hcl
provider "digitalocean" {
    token = var.do_token
    version = "~> 1.4"
}

resource "digitalocean_tag" "environment" {
    name = "development"
}

module "default-firewall" {
    source   = "github.com/cpxpratik/terraform-digitalocean-firewall-rules.git?ref=master"
    do_token = var.do_token

    prefix                         = "dev"
    tags                           = [digitalocean_tag.environment.id]
    allowed_outbound_ssh_addresses = []
}
```

## Firewall rules

The following rules will be created:

- HTTP outbound (destination port 80)
- HTTPS outbound (destination port 443)
- DNS outbound (destination ports 53/tcp and 53/udp)
- NTP outbound (destination port 123/udp)
- SSH outbound (destination port 22)
- SSH inbound (port 22)

Each rule allows by default the following IP addresses: `["0.0.0.0/0", "::/0"]`. It is possible to lock this down further by overwriting the various variables available for each rule set.
