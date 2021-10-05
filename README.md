# Terraform postgres

## Setup instructions

These setup instructions are there if you want to run the lab in your own subscription.

Required software

```sh
terraform
azure cli
Text editor (vscode ideal, but not required)
```

[Installing Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/azure-get-started#install-terraform)

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

[Visual Studio Code](https://code.visualstudio.com/Download)

Suggested vscode extensions:
Azure Terraform
HashiCorp Terraform

## Azure authentication

Make sure you ran "az login" and followed the prompts or have a service principal specified with the variables exported in your shell.

```sh
az login
az account set -s <sub id>
```

## Looking over terraform files

Clone the files to your local machine or copy them from the github repo

```sh
git clone https://github.com/asalbers/terraform_postgres.git
```

### main.tf

Open the main.tf file in a text editor.
This deployment will deploy a basic tier of postgres to your chosen resource group. 

This section of the main.tf locks in the Azure provider version number up to the latest minor release

```sh
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~>2.0"
    }
  }
}
```

The next piece to look at in the main.tf file is the provider section. This the field where you set the provider that you will be using in you code.

```sh
provider "azurerm" {
  features {}
}
```

The next piece is the resource block for postgres. This is the block where all of the config options are set for postgres. Here you can see references to the variables that are set in the variables.tf file.
Only a few values are hardcoded in the main.tf. You can leave the values below set to false.

```sh
geo_redundant_backup_enabled = false
auto_grow_enabled            = false
```
If there are additional configuration values you want to set you can look at the [provider reference](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_server).

### Variables.tf

This file contains all of the referenced variables for the terraform run. All of the values in this file have some sort of default value set that can be modified.

Edit the variables.tf file to change the following values.

For editing tags you can modify the block in the file and add your tags in the map below.
```sh
variable "tags" {
  type = map(string)
  default = {
    environment : "dev",
    test  : "HOL"
  }
}
```

You can also edit the resource group in variables.tf file in the block specified below.
```sh
variable "resource_group" {
  description = "(Required) The name of the resource group in which to create the PostgreSQL Server."
  default     = "AALabRG"
}
```

Two other values to modify are the administrator_login and the administrator_login_password. Its not required to modify these, but if you unique admin password and login information it would be good to change these to different values.
```sh
variable "administrator_login" {
  description = "(Required) The Administrator Login for the PostgreSQL Server."
  default     = "pgadminacct"
}

variable "administrator_login_password" {
    description = "administrator password for the Postgres database"
    default     = "Don0ts3tth1sh3r3!"
}
```

The last value you will want to edit is the server name itself. You can either add to the name in the variable or use your own.
```sh
variable "name" {
  description = "(Required) The name of the PostgreSQL Server."
  default     = "pg-terraform-test"
}
```

## Running terraform

### Terraform init

After you edited the main.tf and varibles.tf you can begin running terraform itself.

The first command to run is the terraform init command to install any dependencies.
```sh
terraform init
```

### Terraform plan

The next will be the terraform plan command.

```sh
terraform plan
```

Examine the output to make sure it matches the variables you changed in the the steps above.

Sample output

```sh
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_postgresql_server.lab-deploy will be created
  + resource "azurerm_postgresql_server" "lab-deploy" {
      + administrator_login              = "pgadminacct"
      + administrator_login_password     = (sensitive value)
      + auto_grow_enabled                = false
      + backup_retention_days            = 35
      + create_mode                      = "Default"
      + fqdn                             = (known after apply)
      + geo_redundant_backup_enabled     = false
      + id                               = (known after apply)
      + location                         = "eastus"
      + name                             = "pg-terraform-test"
      + public_network_access_enabled    = true
      + resource_group_name              = "AALabRG"
      + sku_name                         = "B_Gen5_2"
      + ssl_enforcement                  = (known after apply)
      + ssl_enforcement_enabled          = true
      + ssl_minimal_tls_version_enforced = "TLSEnforcementDisabled"
      + storage_mb                       = 51200
      + tags                             = {
          + "environment" = "dev"
          + "test"        = "HOL"
        }
      + version                          = "11"

      + storage_profile {
          + auto_grow             = (known after apply)
          + backup_retention_days = (known after apply)
          + geo_redundant_backup  = (known after apply)
          + storage_mb            = (known after apply)
        }
    }
```

### Terraform apply

After verifying the plan output matches the values you set run the terraform apply command.

Sample output

```sh
terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_postgresql_server.lab-deploy will be created
  + resource "azurerm_postgresql_server" "lab-deploy" {
      + administrator_login              = "pgadminacct"
      + administrator_login_password     = (sensitive value)
      + auto_grow_enabled                = false
      + backup_retention_days            = 35
      + create_mode                      = "Default"
      + fqdn                             = (known after apply)
      + geo_redundant_backup_enabled     = false
      + id                               = (known after apply)
      + location                         = "eastus"
      + name                             = "pg-terraform-test"
      + public_network_access_enabled    = true
      + resource_group_name              = ""
      + sku_name                         = "B_Gen5_2"
      + ssl_enforcement                  = (known after apply)
      + ssl_enforcement_enabled          = true
      + ssl_minimal_tls_version_enforced = "TLSEnforcementDisabled"
      + storage_mb                       = 51200
      + tags                             = {
          + "environment" = "dev"
          + "test"        = "HOL"
        }
      + version                          = "11"

      + storage_profile {
          + auto_grow             = (known after apply)
          + backup_retention_days = (known after apply)
          + geo_redundant_backup  = (known after apply)
          + storage_mb            = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

azurerm_postgresql_server.lab-deploy: Creating...
azurerm_postgresql_server.lab-deploy: Still creating... [10s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [20s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [30s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [40s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [50s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m0s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m10s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m20s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m30s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m40s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [1m50s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m0s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m10s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m20s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m30s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m40s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [2m50s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m0s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m10s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m20s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m30s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m40s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [3m50s elapsed]
azurerm_postgresql_server.lab-deploy: Still creating... [4m0s elapsed]
azurerm_postgresql_server.lab-deploy: Creation complete after 4m3s [id=]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

### Terraform destroy

```sh
terraform destroy
azurerm_postgresql_server.lab-deploy: Refreshing state... [id=]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # azurerm_postgresql_server.lab-deploy will be destroyed
  - resource "azurerm_postgresql_server" "lab-deploy" {
      - administrator_login               = "pgadminacct" -> null
      - administrator_login_password      = (sensitive value)
      - auto_grow_enabled                 = false -> null
      - backup_retention_days             = 35 -> null
      - create_mode                       = "Default" -> null
      - fqdn                              = "pg-terraform-test.postgres.database.azure.com" -> null
      - geo_redundant_backup_enabled      = false -> null
      - id                                = "" ->
 null
      - infrastructure_encryption_enabled = false -> null
      - location                          = "eastus" -> null
      - name                              = "pg-terraform-test" -> null
      - public_network_access_enabled     = true -> null
      - resource_group_name               = "" -> null
      - sku_name                          = "B_Gen5_2" -> null
      - ssl_enforcement                   = "Enabled" -> null
      - ssl_enforcement_enabled           = true -> null
      - ssl_minimal_tls_version_enforced  = "TLSEnforcementDisabled" -> null
      - storage_mb                        = 51200 -> null
      - tags                              = {
          - "environment" = "dev"
          - "test"        = "HOL"
        } -> null
      - version                           = "11" -> null

      - storage_profile {
          - auto_grow             = "Disabled" -> null
          - backup_retention_days = 35 -> null
          - geo_redundant_backup  = "Disabled" -> null
          - storage_mb            = 51200 -> null
        }
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_postgresql_server.lab-deploy: Destroying... [id=]
azurerm_postgresql_server.lab-deploy: Still destroying... [id=, 10s elapsed]
azurerm_postgresql_server.lab-deploy: Destruction complete after 15s
```