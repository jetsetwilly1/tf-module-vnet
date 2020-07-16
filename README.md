# tf-module-vnet
Terraform Azure Vnet module

Taken from the terraform repo and adapted slightly to allow delegations to be added as well as associating NSG's to subnets.

TODO - write up how to use this module.
```
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "my-resources"
  location = "West Europe"
}

module "vnet" {
  source              = "Azure/vnet/azurerm"
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
  subnet_prefixes     = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  subnet_names        = ["subnet1", "subnet2", "subnet3"]
 
  nsg_ids = {
    "subnet1"       = module.nsg1.network_security_group_id
    "subnet2"       = module.nsg2.network_security_group_id
    "subnet3"       = module.nsg3.network_security_group_id
  }
  
  delegations = [ [],[ { 
    name = "delagation2" 
      service_delegation = {
        name    = "Microsoft.Web/serverFarms"
        actions = ["Microsoft.Network/virtualNetworks/subnets/action"]
      } 
    } ], []
  ]

  tags = {
    environment = "dev"
    costcenter  = "it"
  }
}
```
