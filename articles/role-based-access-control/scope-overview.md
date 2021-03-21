---
title: Az Azure RBAC hatókörének megismerése
description: Ismerje meg az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hatókörét, valamint az erőforrás hatókörének meghatározását.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555915"
---
# <a name="understand-scope-for-azure-rbac"></a>Az Azure RBAC hatókörének megismerése

A *hatókör* azon erőforrások halmaza, amelyekhez a hozzáférés vonatkozik. Ha szerepkört rendel hozzá, fontos megérteni a hatókört, hogy csak a valóban szükséges hozzáférést biztosító rendszerbiztonsági tag legyen elérhető. A hatókör korlátozásával korlátozhatja, hogy milyen erőforrásokra van veszélye, ha a rendszerbiztonsági tag még mindig biztonságban van.

## <a name="scope-levels"></a>Hatókör szintjei

Az Azure-ban négy szinten adhatja meg a hatókört: [felügyeleti csoport](../governance/management-groups/overview.md), előfizetés, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)és erőforrás. A hatókörök szülő-gyermek (származtatott) kapcsolatokba vannak rendezve. A hierarchia minden szintje konkrétabb módon teszi elérhetővé a hatókört. A szerepköröket a hatókör ezen szintjein lehet hozzárendelni. A kiválasztott szint határozza meg, hogy a rendszer milyen széles körben alkalmazza a szerepkört. Az alacsonyabb szintek öröklik a szerepkör-engedélyeket a magasabb szintekről. 

![Szerepkör-hozzárendelés hatóköre](./media/scope-overview/rbac-scope-no-label.png)

A felügyeleti csoportok az előfizetések feletti hatóköri szint, de a felügyeleti csoportok összetettebb hierarchiákat is támogatnak. Az alábbi ábrán egy példa látható a felügyeleti csoportok és a megadható előfizetések hierarchiájának meghatározására. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [Mi az Azure felügyeleti csoportok?](../governance/management-groups/overview.md).

![Felügyeleti csoport és előfizetési hierarchia](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Hatókör formátuma

Ha a parancssor használatával rendel hozzá szerepköröket, meg kell adnia a hatókört. Parancssori eszközök esetén a hatókör egy potenciálisan hosszú karakterlánc, amely a szerepkör-hozzárendelés pontos hatókörét azonosítja. A Azure Portal a hatókör általában *erőforrás-azonosítóként* van felsorolva.

A hatókör a perjel (/) karakterrel elválasztott azonosítók sorozatából áll. Ezt a karakterláncot úgy tekintheti meg, mint a következő hierarchiát, ahol a helyőrzők nélküli szöveg ( `{}` ) rögzített azonosítók:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` a használandó előfizetés azonosítója (GUID).
- `{resourcesGroupName}` a tartalmazza a tartalmazó erőforráscsoport nevét.
- `{providerName}` az erőforrást kezelő [erőforrás-szolgáltató](../azure-resource-manager/management/azure-services-resource-providers.md) neve, majd az erőforrás- `{resourceType}` `{resourceSubType*}` szolgáltatón belüli további szintek azonosítása.
- `{resourceName}` egy adott erőforrást azonosító karakterlánc utolsó része.

A felügyeleti csoportok az előfizetések feletti szintek, és a legszélesebb (legkevésbé konkrét) hatókörrel rendelkeznek. Ezen a szinten a szerepkör-hozzárendelések a felügyeleti csoporton belüli előfizetésekre vonatkoznak. A felügyeleti csoport hatókörének formátuma a következő:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Hatóköri példák

> [!div class="mx-tableFixed"]
> | Hatókör | Példa |
> | --- | --- |
> | Felügyeleti csoport | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Előfizetés | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Erőforráscsoport | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Erőforrás | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Erőforrás hatókörének meghatározása

A felügyeleti csoport, az előfizetés vagy az erőforráscsoport hatókörének meghatározása meglehetősen egyszerű. Csak ismernie kell a nevet és az előfizetés AZONOSÍTÓját. Egy erőforrás hatókörének meghatározása azonban valamivel több munkát igényel. Íme néhány módszer, amelyekkel meghatározhatja egy erőforrás hatókörét.

- A Azure Portal nyissa meg az erőforrást, és tekintse meg a tulajdonságokat. Az erőforrásnak fel kell sorolnia az **erőforrás-azonosítót** , ahol meghatározhatja a hatókört. Például itt láthatók a Storage-fiókok erőforrás-azonosítói.

    ![Azure Portal található Storage-fiók erőforrás-azonosítói](./media/scope-overview/scope-resource-id.png)

- A másik lehetőség, hogy a Azure Portal használatával ideiglenesen rendeljen hozzá egy szerepkört az erőforrás-hatókörben, majd az [Azure PowerShell](role-assignments-list-powershell.md) vagy az [Azure CLI](role-assignments-list-cli.md) használatával listázza a szerepkör-hozzárendelést. A kimenetben a hatókör tulajdonságként jelenik meg.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök hozzárendelésének lépései](role-assignments-steps.md)
- [Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Mik az Azure-beli felügyeleti csoportok?](../governance/management-groups/overview.md)
