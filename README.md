# Terraform Azure Kubernetes Service

This module installs an AKS cluster.

More info [here](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster).

## Usage example

```hcl
module "aks" {
  source = "github.com/scalair/terraform-azure-aks"

  name                     = "aks-cluster"
  dns_prefix               = "akscluster"
  location                 = "francecentral"
  resource_group_name      = "aks_rg"
  node_resource_group_name = "aks_node_rg"

  kubernetes_version = "1.17.11"
  sku_tier           = "Free"

  api_server_authorized_ip_ranges = [10.2.0.0/16]

  default_node_pool_vm_size        = "Standard_DS2_v2"
  default_node_pool_node_count     = 3
  default_node_pool_vnet_subnet_id = "/subnet/id"

  // If this block is not defined, Azure will create an identity for the cluster
  service_principal = {
    client_id     = "xxxxxxxxxxxxxxxxxxxxxxxx"
    client_secret = "xxxxxxxxxxxxxxxxxxxxxxxx"
  }

  // If this block is not defined, kubenet is the default plugin
  network_profile = {
    plugin             = "azure"
    policy             = "azure"
    dns_service_ip     = "10.254.0.10"
    service_cidr       = "10.254.0.0/16"
    docker_bridge_cidr = "172.17.0.1/16"
    outbound_type      = "loadBalancer"
    egress_ip_count    = 1
  }

  enable_kube_dashboard = false

  // If true, will create a log analytic workspace in the cluster resource group
  enable_oms_agent      = true
  tags = {}
}
```

## What is not (yet) implemented

- ACI connector
- Auto-scaling
- Azure Policy
- HTTP application routing (as it not recommended for production)
- AAD integration in RBAC
- Windows profile
