terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "3.89.0"
    }
  }
}

provider "azurerm" {
  subscription_id = "e5bf4e39-759d-4743-a0d1-27a66dac3e5b"
  client_id = "3a041483-7521-4670-b2aa-932490237c3d"
  client_secret = "kia8Q~yJ1m2nKYxAM.cfMO~WSWexChWP_XDIZags"
  tenant_id = "6b8c765d-7556-4e95-ab75-dab5ad9604f5"
  features {}
}

variable "storage_account_name" {
  type = string
  description = "Please enter storage account name"
}

locals {
  resource_group_name = "app-grp"
  location = "North Europe"
}

resource "azurerm_resource_group" "app_grp" {
  name = local.resource_group_name
  location = local.location
}

resource "azurerm_storage_account" "storage_account" {
  name                     = var.storage_account_name
  resource_group_name      = local.resource_group_name
  location                 = local.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
  allow_nested_items_to_be_public = true
  depends_on = [ 
    azurerm_resource_group.app_grp 
    ]
}

resource "azurerm_storage_container" "data" {
  name                  = "data"
  storage_account_name  = var.storage_account_name
  container_access_type = "blob"
  depends_on = [ 
    azurerm_storage_account.storage_account ]
}

resource "azurerm_storage_blob" "sample" {
  name = "sample.txt"
  storage_account_name = var.storage_account_name
  storage_container_name = "data"
  type = "Block"
  source = "sample.txt"
  depends_on = [
    azurerm_storage_account.storage_account
    ] 
}