---
title: Megfelelőség a Azure Policy használatával
description: Az Azure Signaler szolgáltatás erőforrásainak megfelelőségének naplózásához Azure Policy beépített szabályzatokat rendelhet hozzá.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89295019"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Az Azure Signal Service-erőforrások megfelelőségének naplózása Azure Policy használatával

[Azure Policy](../governance/policy/overview.md) a szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez használt Azure-szolgáltatás. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk a beépített szabályzatokat (előzetes verzió) ismerteti az Azure Signaler szolgáltatáshoz. Ezeket a szabályzatokat az új és a meglévő szignáló-erőforrások megfelelőségének naplózására használhatja.

A Azure Policy használatához nem számítunk fel díjat.

## <a name="built-in-policy-definitions"></a>Beépített szabályzat-definíciók

Az alábbi beépített szabályzat-definíciók az Azure Signaler szolgáltatásra jellemzőek:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Szabályzat-definíciók kiosztása

* A szabályzat-definíciókat a [Azure Portal](../governance/policy/assign-policy-portal.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md), a [Resource Manager-sablon](../governance/policy/assign-policy-template.md)vagy a Azure Policy SDK-k segítségével rendelheti hozzá.
* Hatókör: szabályzat-hozzárendelés egy erőforráscsoport, egy előfizetés vagy egy [Azure felügyeleti csoport](../governance/management-groups/overview.md)számára. A jelző házirend-hozzárendelések a hatókörön belüli meglévő és új jelző erőforrásokra vonatkoznak.
* Bármikor engedélyezheti vagy letilthatja a [szabályzatok kényszerítését](../governance/policy/concepts/assignment-structure.md#enforcement-mode) .

> [!NOTE]
> A szabályzatok hozzárendelése vagy frissítése után időbe telik, amíg a hozzárendelés a meghatározott hatókörben lévő erőforrásokra lesz alkalmazva. Tekintse meg a [szabályzat-próbaverziós eseményindítókkal](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)kapcsolatos információkat.

## <a name="review-policy-compliance"></a>Szabályzat megfelelőségének áttekintése

A szabályzat-hozzárendelések által generált megfelelőségi információk a Azure Portal, az Azure parancssori eszközei vagy a Azure Policy SDK-k használatával érhetők el. Részletekért lásd: [Azure-erőforrások megfelelőségi adatainak beolvasása](../governance/policy/how-to/get-compliance-data.md).

Ha egy erőforrás nem megfelelő, számos lehetséges oka lehet. Az ok megállapításához vagy a felelős változás megkereséséhez tekintse meg a [nem megfelelőség megállapítása](../governance/policy/how-to/determine-non-compliance.md)című témakört.

### <a name="policy-compliance-in-the-portal"></a>Szabályzat megfelelősége a portálon:

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg a **házirendet**.
1. Válassza a **megfelelőség** lehetőséget.
1. A szűrők használata a megfelelőségi állapotok korlátozására vagy a házirendek keresésére
   
    [![Szabályzatoknak való megfelelés a portálon ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Válasszon ki egy házirendet az összesített megfelelőségi adatok és események áttekintéséhez. Ha szükséges, válasszon ki egy adott jelzőt az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Szabályzatok megfelelősége az Azure CLI-ben

Az Azure CLI-vel is lekérheti a megfelelőségi adatgyűjtést. Például használja az az [Policy-hozzárendelési lista](/cli/azure/policy/assignment#az-policy-assignment-list) parancsot a CLI-ben, hogy beolvassa az alkalmazott Azure signaler szolgáltatási szabályzatok házirend-azonosítóit:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Példa a kimenetre:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Ezután futtassa az az [Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot, hogy az adott erőforráscsoport összes erőforrásához tartozó JSON-formátumú megfelelőségi állapotot adja vissza:

```azurecli
az policy state list --g <resourceGroup>
```

Vagy futtassa az [az Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot egy adott jelző erőforrás JSON-formátumú megfelelőségi állapotának visszaküldéséhez:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy [definíciói](../governance/policy/concepts/definition-structure.md) és [hatásairól](../governance/policy/concepts/effects.md)

* [Egyéni szabályzat-definíció](../governance/policy/tutorials/create-custom-policy-definition.md) létrehozása

* További információ az Azure [irányítási képességeiről](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/