---
title: Azure Traffic Manager felülbírálása az Azure CLI használatával | Microsoft Docs
description: Ez a cikk segít megérteni, hogyan bírálható felül egy Traffic Manager-alhálózat felülbírálása egy Traffic Manager-profil útválasztási módszerével, hogy a forgalmat a végfelhasználói IP-cím alapján, előre meghatározott IP-címtartományon keresztül a végpontleképezések felé irányítsa.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767798"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager felülbírálása az Azure CLI használatával

Traffic Manager alhálózat felülbírálása lehetővé teszi egy profil útválasztási módszerének megváltoztatását.  A felülbírálás hozzáadása a végfelhasználó IP-címe alapján irányítja a forgalmat egy előre meghatározott IP-címtartománysal a végpontleképezéshez. 

## <a name="how-subnet-override-works"></a>Az alhálózat felülbírálási működése

Amikor alhálózati felülbírálásokat ad egy Traffic Manager-profilhoz, a Traffic Manager először ellenőrzi, hogy van-e alhálózati felülbírálás a végfelhasználó IP-címére. Ha talál egyet, a felhasználó DNS-lekérdezése a megfelelő végpontra lesz irányítva.  Ha nem talál leképezést, Traffic Manager vissza a profil eredeti útválasztási módszerére. 

Az IP-címtartományok CIDR-tartományokként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8) is megadhatja őket. Az egyes végpontok IP-címtartományának egyedinek kell lennie az adott végponton. Ha az IP-címtartományok átfedésben vannak a különböző végpontok között, akkor a profilt a rendszer Traffic Manager.

Az alhálózat felülbírálásokat támogató útválasztási profiloknak két típusa van:

* **Földrajzi** – Ha Traffic Manager alhálózati felülbírálást talál a DNS-lekérdezés IP-címéhez, a lekérdezést a végpont állapotától függetlenül a végponthoz irányíthatja.
* **Teljesítmény** – Ha Traffic Manager alhálózati felülbírálást talál a DNS-lekérdezés IP-címéhez, az csak akkor fogja a végpontra irányít át a forgalmat, ha az kifogástalan.  Traffic Manager a teljesítmény-útválasztás heurisztikus lesz, ha az alhálózat felülbírálási végpontja nem megfelelő.

## <a name="create-a-traffic-manager-subnet-override"></a>Új Traffic Manager létrehozása

Alhálózati Traffic Manager létrehozásához az Azure CLI használatával használhatja a felülbírálás alhálózatát a Traffic Manager végponthoz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Frissítse a Traffic Manager végpontot az alhálózat felülbírálása használatával.
Az Azure CLI használatával frissítse a végpontot [az az network traffic-manager endpoint update használatával.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

Az IP-címtartományokat az [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) az **--remove** kapcsolóval való futtatásával távolíthatja el.

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Következő lépések

További információ a Traffic Manager [útválasztási módszereiről.](traffic-manager-routing-methods.md)

Az [alhálózati forgalom-útválasztási módszer megismerése](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)