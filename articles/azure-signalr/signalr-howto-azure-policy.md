---
title: Megfelelőség Azure Policy
description: A beépített szabályzatok hozzárendelése a Azure Policy az erőforrások megfelelő Azure SignalR Service naplózása érdekében.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784572"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Az erőforrások megfelelő Azure SignalR Service naplózása a Azure Policy

[Azure Policy](../governance/policy/overview.md) azure-beli szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére használható. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk a beépített szabályzatokat (előzetes verzió) Azure SignalR Service. Ezekkel a szabályzatokkal naplóz az új és meglévő SignalR-erőforrásokat a megfelelőség érdekében.

A Azure Policy.

## <a name="built-in-policy-definitions"></a>Beépített szabályzatdefiníciók

Az alábbi beépített szabályzatdefiníciók csak a Azure SignalR Service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Szabályzatdefiníciók hozzárendelése

* Szabályzatdefiníciók hozzárendelése a [Azure Portal](../governance/policy/assign-policy-portal.md), [az Azure CLI,](../governance/policy/assign-policy-azurecli.md)Resource Manager [sablon,](../governance/policy/assign-policy-template.md)vagy a Azure Policy SDK-k használatával.
* Szabályzat-hozzárendelés hatóköre erőforráscsoportra, előfizetésre vagy [Azure felügyeleti csoportra.](../governance/management-groups/overview.md) A SignalR-szabályzat-hozzárendelések a hatókörben meglévő és új SignalR-erőforrásokra vonatkoznak.
* A házirendek [betartatását bármikor](../governance/policy/concepts/assignment-structure.md#enforcement-mode) engedélyezheti vagy letilthatja.

> [!NOTE]
> A szabályzat hozzárendelése vagy frissítése után némi időbe telik, hogy a hozzárendelés alkalmazva legyen a meghatározott hatókör erőforrásaira. További információ a [szabályzatértékelési eseményindítókról.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Szabályzat-megfelelőség áttekintése

A szabályzat-hozzárendelések által létrehozott megfelelőségi információkhoz a Azure Portal, az Azure parancssori eszközei vagy a Azure Policy DK-k használatával férhet hozzá. Részletekért lásd: [Az Azure-erőforrások megfelelőségi adatainak lekért adatai.](../governance/policy/how-to/get-compliance-data.md)

Ha egy erőforrás nem megfelelő, annak számos oka lehet. A módosítás okának meghatározásához vagy a felelős megkeresését lásd: Meg nem [felelés meghatározása.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Szabályzat-megfelelőség a portálon:

1. Válassza **a Minden szolgáltatás** lehetőséget, és keressen rá a Szabályzat **kifejezésre.**
1. Válassza a **Megfelelőség lehetőséget.**
1. A szűrőkkel korlátozhatja a megfelelőségi államokat, vagy szabályzatokat kereshet
   
    [![Szabályzatok megfelelősége a portálon ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Válasszon ki egy szabályzatot az összesített megfelelőségi adatok és események áttekintésére. Ha szükséges, válasszon ki egy adott SignalR-t az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Szabályzatok megfelelősége az Azure CLI-ban

A megfelelőségi adatokat az Azure CLI használatával is lekértheti. A parancssori felület [az az policy assignment list parancsával](/cli/azure/policy/assignment#az_policy_assignment_list) például lekérte az alkalmazott Azure SignalR Service szabályzatok szabályzat-Azure SignalR Service le:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Példa a kimenetre:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Ezután futtassa [az az policy state list](/cli/azure/policy/state#az_policy_state_list) parancsokat az adott erőforráscsoportban található összes erőforrás JSON-formátumú megfelelőségi állapotának visszaadása érdekében:

```azurecli
az policy state list --g <resourceGroup>
```

Vagy futtassa [az az policy state list](/cli/azure/policy/state#az_policy_state_list) parancsokat egy adott SignalR-erőforrás JSON-formátumú megfelelőségi állapotának visszaadása érdekében:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy és [hatásairól](../governance/policy/concepts/effects.md) [](../governance/policy/concepts/definition-structure.md)

* Egyéni [szabályzatdefiníció létrehozása](../governance/policy/tutorials/create-custom-policy-definition.md)

* További információ az Azure [irányítási képességeiről](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/