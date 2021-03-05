---
title: Azure Traffic Manager alhálózat felülbírálása az Azure CLI használatával | Microsoft Docs
description: Ez a cikk segít megérteni, hogy Traffic Manager alhálózat felülbírálásával hogyan bírálhatja felül a Traffic Manager-profilok útválasztási módszerét, hogy a végfelhasználói IP-cím alapján, előre meghatározott IP-címeken keresztül irányítsa át a forgalmat a végpont-hozzárendelések számára.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211328"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Alhálózat Traffic Manager felülbírálása az Azure CLI használatával

Traffic Manager alhálózat felülbírálása lehetővé teszi egy profil útválasztási módszerének módosítását.  A felülbírálás hozzáadásával a rendszer a végfelhasználó IP-címe alapján, előre meghatározott IP-tartománnyal továbbítja a forgalmat a végpont-hozzárendeléshez. 

## <a name="how-subnet-override-works"></a>Az alhálózat felülbírálásának működése

Ha az alhálózat felülbírálásait egy Traffic Manager-profilhoz adja, Traffic Manager először ellenőrizze, hogy van-e alhálózat felülbírálva a végfelhasználó IP-címéhez. Ha az egyik található, a rendszer a felhasználó DNS-lekérdezését a megfelelő végpontra irányítja.  Ha nem talál leképezést, Traffic Manager a profil eredeti útválasztási metódusára kerül vissza. 

Az IP-címtartományok megadhatók CIDR-tartományként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8). Az egyes végpontokhoz társított IP-tartományoknak egyedinek kell lenniük a végpont számára. Az IP-címtartományok különböző végpontok közötti átfedése miatt a Traffic Manager elutasítja a profilt.

Az alhálózatok felülbírálásait két típusú útválasztási profil támogatja:

* **Földrajzi** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, a lekérdezés a végpont állapotának megfelelő állapotba irányítja a lekérdezést.
* **Teljesítmény** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, akkor a rendszer csak akkor irányítja át a forgalmat a végponthoz, ha az kifogástalan állapotú.  Ha az alhálózat felülbírálási végpontja nem kifogástalan állapotú, akkor a Traffic Manager vissza fog térni a teljesítmény-útválasztási heurisztikus művelethez.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager alhálózat felülbírálásának létrehozása

Traffic Manager alhálózat felülbírálásának létrehozásához az Azure CLI használatával adhatja hozzá a felülbírálás alhálózatait a Traffic Manager végponthoz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Frissítse a Traffic Manager végpontot az alhálózat felülbírálásával.
Az Azure CLI használatával frissítse a végpontot az [az Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update)paranccsal.

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

Az IP-címtartományok eltávolításához futtassa az az [Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) parancsot a **--Remove** kapcsolóval.

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Következő lépések

További információ a Traffic Manager [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).

Tudnivalók az [alhálózat forgalmáról – útválasztási módszer](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)