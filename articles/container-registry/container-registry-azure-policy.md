---
title: Megfelelőség Azure Policy
description: Beépített szabályzatok hozzárendelése a Azure Policy azure-beli tárolóregisztrálók megfelelőségének naplózása érdekében
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784172"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure-beli tárolóregisztrálók megfelelőségi naplózása a Azure Policy

[Azure Policy](../governance/policy/overview.md) azure-beli szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére használható. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk bemutatja a beépített szabályzatokat a Azure Container Registry. Ezekkel a szabályzatokkal naplókat naplót lehet majd használni az új és a meglévő regisztrációs adatbázisokkal a megfelelőség érdekében.

A Azure Policy.

## <a name="built-in-policy-definitions"></a>Beépített szabályzatdefiníciók

Az alábbi beépített szabályzatdefiníciók csak a Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Szabályzatok hozzárendelése

* Szabályzatokat rendelhet hozzá [a Azure Portal,](../governance/policy/assign-policy-portal.md) [az Azure CLI,](../governance/policy/assign-policy-azurecli.md)Resource Manager [sablon](../governance/policy/assign-policy-template.md)vagy a Azure Policy használatával.
* Szabályzat-hozzárendelés hatóköre erőforráscsoportra, előfizetésre vagy [Azure felügyeleti csoportra.](../governance/management-groups/overview.md) A tároló-beállításjegyzék szabályzat-hozzárendelései a hatókörben lévő meglévő és új tárolóregisztrálókra vonatkoznak.
* A házirendek [betartatását bármikor](../governance/policy/concepts/assignment-structure.md#enforcement-mode) engedélyezheti vagy letilthatja.

> [!NOTE]
> A szabályzat hozzárendelése vagy frissítése után némi időt vesz igénybe, hogy a hozzárendelés alkalmazva legyen a meghatározott hatókör erőforrásaira. Lásd a [szabályzatértékelési eseményindítókra vonatkozó információkat.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Szabályzat-megfelelőség áttekintése

A szabályzat-hozzárendelések által létrehozott megfelelőségi információkhoz a Azure Portal, az Azure parancssori eszközeivel vagy a Azure Policy SDK-k használatával férhet hozzá. Részletekért lásd: [Az Azure-erőforrások megfelelőségi adatainak lekért adatai.](../governance/policy/how-to/get-compliance-data.md)

Ha egy erőforrás nem megfelelő, annak számos oka lehet. A módosítás okának meghatározásához vagy a felelős megkeresését lásd: Meg nem [felelés meghatározása.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Szabályzat-megfelelőség a portálon:

1. Válassza **a Minden szolgáltatás** lehetőséget, és keressen rá a Szabályzat **kifejezésre.**
1. Válassza a **Megfelelőség lehetőséget.**
1. A szűrőkkel korlátozhatja a megfelelőségi államokat, vagy szabályzatokat kereshet.

    ![Szabályzatok megfelelősége a portálon](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Válasszon ki egy szabályzatot az összesített megfelelőségi adatok és események áttekintésére. Ha szükséges, válasszon ki egy adott regisztrációs adatbázist az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Szabályzatok megfelelősége az Azure CLI-ban

Megfelelőségi adatokat az Azure CLI használatával is lekért. Például a parancssori felület [az policy assignment list parancsával](/cli/azure/policy/assignment#az_policy_assignment_list) lekérte az alkalmazott Azure Container Registry szabályzatok szabályzat-Azure Container Registry le:

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

Ezután futtassa [az az policy state list](/cli/azure/policy/state#az_policy_state_list) parancsokat az adott szabályzatazonosítóban található összes erőforrás JSON-formátumú megfelelőségi állapotának visszaadása érdekében:

```azurecli
az policy state list \
  --resource <policyID>
```

Vagy futtassa [az az policy state list](/cli/azure/policy/state#az_policy_state_list) parancsokat egy adott beállításjegyzék-erőforrás, például a myregistry JSON-formátumú megfelelőségi *állapotának visszaadása érdekében:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy és [hatásairól.](../governance/policy/concepts/effects.md) [](../governance/policy/concepts/definition-structure.md)

* Hozzon létre [egy egyéni szabályzatdefiníciót.](../governance/policy/tutorials/create-custom-policy-definition.md)

* További információ az Azure [irányítási képességeiről.](../governance/index.yml)
