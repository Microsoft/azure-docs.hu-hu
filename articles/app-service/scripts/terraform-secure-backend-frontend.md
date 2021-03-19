---
title: 'Terraform: előtér-webalkalmazás és háttérbeli webalkalmazás üzembe helyezése biztonságos módon, VNet-integrációval és privát végponttal'
description: Ismerje meg, hogyan használhatja a Terraform-szolgáltatót App Service a privát végpontokkal és a VNet-integrációval való biztonságos csatlakozást biztosító két webalkalmazás üzembe helyezéséhez
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91739833"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Hozzon létre két webalkalmazást biztonságos kapcsolattal a privát végponttal és a VNet-integrációval

Ez a cikk példákat mutat be a [privát végpontok](../networking/private-endpoint.md) és a regionális [VNet-integráció](../web-sites-integrate-with-vnet.md) használatára két webalkalmazás (előtér és háttér) biztonságos csatlakoztatásához az alábbi lépéseket követve:
- VNet üzembe helyezése
- Hozza létre az első alhálózatot az integrációhoz
- Hozza létre a második alhálózatot a privát végponthoz, egy adott paramétert kell beállítania a hálózati házirendek letiltásához
- Helyezzen üzembe egy PremiumV2 vagy PremiumV3 típusú App Service tervet, amely a privát végpont funkcióhoz szükséges.
- Hozza létre a előtér-webalkalmazást meghatározott Alkalmazásbeállítások használatával a magánhálózati DNS-zóna felhasználása érdekében, [További részletekért](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Az előtér-webalkalmazás összekötése az integrációs alhálózattal
- A háttér-Webalkalmazás létrehozása
- A saját DNS-zóna létrehozása a privát kapcsolati zóna nevével a webalkalmazás privatelink.azurewebsites.net
- A zóna összekapcsolása a VNet
- Hozza létre a háttér-webalkalmazáshoz tartozó magánhálózati végpontot a végponti alhálózaton, és regisztrálja a DNS-neveket (webhely és SCM) a korábban létrehozott DNS privát zónában.

## <a name="how-to-use-terraform-in-azure"></a>A Terraform használata az Azure-ban

A Terraform és az Azure használatának megismeréséhez keresse fel az [Azure dokumentációját](/azure/developer/terraform/) .

## <a name="the-complete-terraform-file"></a>A teljes Terraform fájl

A fájl használatához módosítania kell a frontwebapp és a backwebapp erőforrás Name (név) tulajdonságát (a WebApp neve csak a világ egyedi DNS-neve lehet). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Következő lépések


> [További információ a Terraform Azure-beli használatáról](/azure/developer/terraform/)