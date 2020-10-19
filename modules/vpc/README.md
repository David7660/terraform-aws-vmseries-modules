# VPC module for VM-Series

## Overview  
Module for sinlge VPC and all other optional base infrastructure resources to support various Palo Alto Networks VM-Series deployments. See examples for more details of usage.



### Usage
```
provider "aws" {
  region = var.region
}

module "vpc" {
  source     = "git::https://github.com/PaloAltoNetworks/terraform-aws-vmseries-modules/modules/vpc?ref=v0.1.0"

prefix_name_tag = "my-prefix"   // Used for resource name Tags. Leave as empty string if not desired

global_tags = {
 Environment = "us-east-1"
 Group       = "SecOps"
 Managed_By  = "Terraform"
 Description = "Example Usage"
}

vpc = {
 vmseries_vpc = {
  existing              = false
   name                  = "vmseries-vpc"
   cidr_block            = "10.100.0.0/16"
   secondary_cidr_blocks = ["10.200.0.0/16", "10.201.0.0/16"]
   instance_tenancy      = "default"
   enable_dns_support    = true
   enable_dns_hostname   = true
   igw                   = true
 }
}

subnets = {
 mgmt-1a       = { existing = false, name = "mgmt-1a", cidr = "10.100.0.0/25", az = "us-east-1a", rt = "mgmt" }            # VM-Series management
 public-1a     = { name = "public-1a", cidr = "10.100.1.0/25", az = "us-east-1a", rt = "vdss-outside" }    # interface in public subnet for internet
 mgmt-1b       = { name = "mgmt-1b", cidr = "10.100.0.128/25", az = "us-east-1b", rt = "mgmt" }            # VM-Series management
 public-1b     = { name = "public-1b", cidr = "10.100.1.128/25", az = "us-east-1b", rt = "vdss-outside" }    # interface in public subnet for internet
}

```

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| terraform | ~> 0.13 |
| aws | ~> 3 |

## Providers

| Name | Version |
|------|---------|
| aws | ~> 3 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| global\_tags | Optional Map of arbitrary tags to apply to all resources | `map(any)` | `{}` | no |
| nat\_gateways | Map of NAT Gateways to create | `any` | `{}` | no |
| prefix\_name\_tag | Prepended to name tags for various resources. Leave as empty string if not desired. | `string` | `""` | no |
| security\_groups | Map of Security Groups | `any` | `{}` | no |
| subnets | Map of Subnets to create | `any` | `{}` | no |
| vpc | Map of parameters for the VPC. | `any` | `{}` | no |
| vpc\_endpoints | Map of VPC endpoints | `any` | `{}` | no |
| vpc\_route\_tables | Map of VPC route Tables to create | `any` | `{}` | no |
| vpn\_gateways | Map of VGWs to create | `any` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| aws\_vpc\_endpoint\_interface\_ids | Interface VPC Endpoint Name -> ID Map (New) |
| internet\_gateway\_id | Internet Gateway Name -> ID Map (New) |
| nat\_gateway\_ids | NAT Gateway Name -> ID Map (New) |
| route\_table\_ids | Route Tables Name -> ID Map (New) |
| security\_group\_ids | Security Group Name -> ID Map (New) |
| subnet\_ids | Subnets Name -> ID Map (New AND Existing) |
| vpc\_id | VPC Name -> ID Map (New OR Existing) |
| vpn\_gateway\_ids | VPN Gateway Name -> ID Map (New) |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Map Input Variable Definitions

### vpc

### vpc_route_tables

### subnets

The subnets variable is a map of maps, where each map represents a subnet. The key of each map will be the state resource identifier within terraform and must be unique, but is not used for resource naming.

There is brownfield support for existing subnets, for this only required to specify `subnet_name` and `existing = true`.

Each subnet map has the following inputs available (please see examples folder for additional references):

| Name | Description | Type | Default | Required | Brownfield Required
|------|-------------|:----:|:-----:|:-----:|:-----:|
| name | The Name Tag of the new / existing subnet  | string | - | yes | yes |
| existing | Flag only if referencing an existing subnet  | bool | `"false"` | yes | yes |
| cidr | The CIDR formatted IP range of the subnet being created | string | - | yes | no |
| rt | The Route Table to associate the subnet with | string | - | yes | no |
| az | The availability zone for the subnet  | string | none (auto-selected) | no | no |
| local_tags  | Map of aribrary tags key/value pairs to apply to this subnet | map | - | no | no |

### security_groups