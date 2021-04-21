---
title: 'CLI: Privát végpont üzembe helyezése webalkalmazáshoz az Azure CLI használatával'
description: Megtudhatja, hogyan helyezhet üzembe privát végpontot a webalkalmazáshoz az Azure CLI használatával
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3c8d0927c3fb74c52e54ceb5ff8ba5c0361c4f46
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787832"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Alkalmazás létrehozása App Service és privát végpont üzembe helyezése az Azure CLI használatával

Ez a példaszkprogram létrehoz egy alkalmazást App Service a kapcsolódó erőforrásokkal, majd üzembe helyez egy privát végpontot.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Mielőtt bármilyen erőforrást létrehozhat, létre kell hoznia egy erőforráscsoportot a webalkalmazás, a Virtual Network és más hálózati összetevők számára. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *francecentral helyen:*

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Létre kell hoznia egy App Service a webalkalmazás gazdagépeként.
Hozzon létre egy App Service az [appservice plan create használatával.](/cli/azure/appservice/plan#az_appservice_plan_create)
Ez a példa App Service *myAppServicePlan* nevű tervet hoz létre a *francecentral* helyen *P1V2* sku-val és csak egy feldolgozóval: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Most, hogy már van egy App Service- csomagja, üzembe helyezhet egy webalkalmazást.
Hozzon létre egy webalkalmazást az [az appservice plan create] (/cli/azure/webapp#az_webapp_create.
Ez a példa létrehoz egy *mySiteName* nevű webalkalmazást a *myAppServicePlan nevű csomagban*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy Virtual Network [az az network vnet create gombra.](/cli/azure/network/vnet) Ez a példa egy *mySubnet* nevű Virtual Network létrehoz egy myVNet nevű *alapértelmezett alhálózatot:*

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Az alhálózat konfigurálása 

A privát végpont hálózati szabályzatának letiltásához frissítenie kell az alhálózatot. Frissítse a *mySubnet* nevű alhálózati konfigurációt [az az network vnet subnet update frissítéssel:](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>A privát végpont létrehozása

Hozza létre a webalkalmazás privát végpontját [az az network private-endpoint create használatával.](/cli/azure/network/private-endpoint) Ez a példa létrehoz  egy *myPrivateEndpoint* nevű privát végpontot a *mySubnet* alhálózat *myVNet* virtuális hálózatában egy *myConnectionName* nevű kapcsolattal a webalkalmazás /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp erőforrás-azonosítójával, a csoportparaméter a webalkalmazás típusa helyek. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>A privát zóna konfigurálása

A végén létre kell hoznia egy  virtuális hálózatra privatelink.azurewebsites.net nevű privát DNS-zónát a webalkalmazás DNS-nevének feloldásához.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Következő lépések

- Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
- További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
