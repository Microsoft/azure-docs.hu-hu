---
title: Megfelelőség a Azure Policy használatával
description: Beépített szabályzatok kiosztása Azure Policyban az Azure Container-nyilvántartások megfelelőségének naplózásához
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 0fed0c4132043e1eaed7e634e1f45b27f7c6e933
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014298"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával

[Azure Policy](../governance/policy/overview.md) a szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez használt Azure-szolgáltatás. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk a Azure Container Registry beépített házirendjeit ismerteti. Ezekkel a házirendekkel naplózhatja az új és a meglévő beállításjegyzékeket a megfelelőség érdekében.

A Azure Policy használatához nem számítunk fel díjat.

## <a name="built-in-policy-definitions"></a>Beépített szabályzat-definíciók

A következő beépített szabályzat-definíciók a Azure Container Registryra vonatkoznak:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Szabályzatok hozzárendelése

* Rendeljen szabályzatokat a [Azure Portal](../governance/policy/assign-policy-portal.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md), egy [Resource Manager-sablon](../governance/policy/assign-policy-template.md)vagy a Azure Policy SDK-k használatával.
* Hatókör: szabályzat-hozzárendelés egy erőforráscsoport, egy előfizetés vagy egy [Azure felügyeleti csoport](../governance/management-groups/overview.md)számára. A tároló beállításjegyzék-házirendjének hozzárendelései a hatókörön belüli meglévő és új tároló-nyilvántartásokra vonatkoznak.
* Bármikor engedélyezheti vagy letilthatja a [szabályzatok kényszerítését](../governance/policy/concepts/assignment-structure.md#enforcement-mode) .

> [!NOTE]
> A szabályzatok hozzárendelése vagy frissítése után időbe telik, amíg a hozzárendelés a meghatározott hatókörben lévő erőforrásokra lesz alkalmazva. Tekintse meg a [szabályzat-próbaverziós eseményindítókkal](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)kapcsolatos információkat.

## <a name="review-policy-compliance"></a>Szabályzat megfelelőségének áttekintése

A szabályzat-hozzárendelések által generált megfelelőségi információk a Azure Portal, az Azure parancssori eszközei vagy a Azure Policy SDK-k használatával érhetők el. Részletekért lásd: [Azure-erőforrások megfelelőségi adatainak beolvasása](../governance/policy/how-to/get-compliance-data.md).

Ha egy erőforrás nem megfelelő, számos lehetséges oka lehet. Az ok megállapításához vagy a felelős változás megkereséséhez tekintse meg a [nem megfelelőség megállapítása](../governance/policy/how-to/determine-non-compliance.md)című témakört.

### <a name="policy-compliance-in-the-portal"></a>Szabályzat megfelelősége a portálon:

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg a **házirendet**.
1. Válassza a **megfelelőség** lehetőséget.
1. A szűrők használatával korlátozhatja a megfelelőségi állapotokat, vagy megkeresheti a szabályzatokat.

    ![Szabályzatoknak való megfelelés a portálon](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Válasszon ki egy házirendet az összesített megfelelőségi adatok és események áttekintéséhez. Ha szükséges, válasszon ki egy adott beállításjegyzéket az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Szabályzatok megfelelősége az Azure CLI-ben

Az Azure CLI-vel is lekérheti a megfelelőségi adatgyűjtést. Például használja az az [Policy-hozzárendelési lista](/cli/azure/policy/assignment#az-policy-assignment-list) parancsot a CLI-ben az alkalmazott Azure Container Registry szabályzatok házirend-azonosítóinak beszerzéséhez:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Példa a kimenetre:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Ezután futtassa az az [Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot az adott házirend-azonosítóhoz tartozó összes erőforrás JSON-formátumú megfelelőségi állapotának visszaküldéséhez:

```azurecli
az policy state list \
  --resource <policyID>
```

Vagy futtassa az [az Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot egy adott beállításjegyzék-erőforrás JSON-formátumú megfelelőségi állapotának visszaküldéséhez, például *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy- [definíciókkal](../governance/policy/concepts/definition-structure.md) és- [hatásokkal](../governance/policy/concepts/effects.md)kapcsolatban.

* Hozzon létre egy [Egyéni szabályzat-definíciót](../governance/policy/tutorials/create-custom-policy-definition.md).

* További információ az Azure [irányítási képességeiről](../governance/index.yml) .
