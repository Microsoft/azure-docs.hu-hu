---
title: Tervterv létrehozása Azure DDoS Protection konfigurálása az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre DDoS Protection-tervet az Azure CLI használatával
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777634"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Rövid útmutató: Standard Azure DDoS Protection létrehozása és konfigurálása az Azure CLI használatával

A Standard Azure DDoS Protection használatának első lépések az Azure CLI használatával. 

A DDoS Protection-csomag olyan virtuális hálózatok készletét határozza meg, amelyeken engedélyezve van a DDoS Protection Standard, több előfizetésben. Konfigurálhat egy DDoS protection-tervet a szervezet számára, és több előfizetésből származó virtuális hálózatokat kapcsolhat ugyanhez a csomaghoz. 

Ebben a rövid útmutatóban egy DDoS Protection-tervet fog létrehozni, és egy virtuális hálózathoz fogja azt csatolni. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Helyileg telepített Azure CLI vagy Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-ddos-protection-plan"></a>Új DDoS Protection létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli hozzá. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Erőforráscsoport létrehozásához használja az [az group create et.](/cli/azure/group#az_group_create) Ebben a példában az erőforráscsoportnak a _MyResourceGroup_ nevet adhatja, és az USA keleti _régiója helyet fogjuk_ használni:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Most hozzon létre egy _MyDdosProtectionPlan nevű_ DDoS protection-tervet:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-védelem engedélyezése virtuális hálózathoz

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-védelem engedélyezése új virtuális hálózathoz

Virtuális hálózat létrehozásakor engedélyezheti a DDoS-védelmet. Ebben a példában a myVnet nevet adhatja a virtuális _hálózatnak:_ 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

A virtuális hálózatok nem áthelyezhetőek másik erőforráscsoportba vagy előfizetésbe, ha a DDoS Standard engedélyezve van a virtuális hálózaton. Ha olyan virtuális hálózatot kell áthelyezni, amelynél engedélyezve van a DDoS Standard, először tiltsa le a Standard DDoS-t, helyezze át a virtuális hálózatot, majd engedélyezze a DDoS Standardot. Az áthelyezés után a virtuális hálózatban lévő összes védett nyilvános IP-cím szabályzati küszöbértékei alaphelyzetbe állnak.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS Protection engedélyezése meglévő virtuális hálózathoz

[DDoS protection-csomag létrehozásakor](#create-a-ddos-protection-plan)egy vagy több virtuális hálózatot társíthat a tervhez. Ha egynél több virtuális hálózatot szeretne hozzáadni, egyszerűen listába kell sorolni a neveket vagy az neveket, szóközök elválasztva. Ebben a példában hozzáadjuk a _MyVnet alhálózatot:_

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Másik lehetőségként engedélyezheti a DDoS Protectiont egy adott virtuális hálózathoz:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

Először ellenőrizze a DDoS Protection-terv részleteit:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Ellenőrizze, hogy a parancs a DDoS Protection-terv megfelelő adatait adja-e vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban megtarthatja az erőforrásait. Ha már nincs rá szükség, törölje a _MyResourceGroup_ erőforráscsoportot. Az erőforráscsoport törlésekor a DDoS Protection-tervet és annak összes kapcsolódó erőforrását is törli. 

Az erőforráscsoport törléséhez használja [az az group delete parancsot:](/cli/azure/group#az_group_delete)

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Egy adott virtuális hálózat frissítése a DDoS Protection letiltásához:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Ha törölni szeretne egy DDoS Protection-tervet, először el kell különítania az összes virtuális hálózatot. 

## <a name="next-steps"></a>Következő lépések

A DDoS Protection-terv telemetriai adatainak megtekintéséről és konfigurálásról az oktatóanyagokban olvashat.

> [!div class="nextstepaction"]
> [DDoS Protection-telemetria megtekintése és konfigurálása](telemetry.md)
