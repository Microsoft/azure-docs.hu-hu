---
title: 'Gyors útmutató: új szabályzat-hozzárendelés REST API'
description: Ebben a rövid útmutatóban a REST API használatával hozhat létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219977"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához REST API

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

REST API Azure-erőforrások létrehozására és kezelésére szolgál. Ez az útmutató a REST API használatával hozza létre a szabályzat-hozzárendelést, és azonosítsa az Azure-környezetben nem megfelelő erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Ha még nem tette meg, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Ez egy olyan eszköz, amely HTTP-kéréseket küld Azure Resource Manager-alapú REST API-khoz. Használhatja a REST dokumentációban található "kipróbálás" funkciót is, vagy olyan eszközt is használhat, mint például a PowerShell [meghívása – RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) vagy [Poster](https://www.postman.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli **azokat a naplózási virtuális gépeket, amelyek nem használnak Managed Disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Futtassa a következő parancsot egy szabályzat-hozzárendelés létrehozásához:

   - REST API URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Kérelem törzse

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

Az előző végpont és a kérelem törzse a következő információkat használja:

REST API URI:
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Egy felügyeleti csoportból egy adott erőforrásra terjedhet. Ügyeljen arra, hogy a `{scope}` következő minták egyikét cserélje le:
  - Felügyeleti csoport: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Előfizetés: `/subscriptions/{subscriptionId}`
  - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Erőforrás `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name** – A hozzárendelés tényleges neve. A fenti példában az _audit-vm-manageddisks_ nevet használtuk.

Kérelem törzse:
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_ történik.
- **Description (Leírás** ) – mélyebb magyarázat arról, hogy mit tesz a szabályzat, vagy miért van hozzárendelve ehhez a hatókörhöz.
- **policyDefinitionId** – a szabályzat-definíció azonosítóját, amely alapján létrehozza a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_.
- **nonComplianceMessages** – beállíthatja, hogy a rendszer mikor tagadja meg az erőforrást a megfelelőség hiánya miatt, vagy hogy nem megfelelőnek értékelje. További információ: [hozzárendelés nem megfelelőségi üzenetek](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

Az új hozzárendelésnek nem megfelelő erőforrások megtekintéséhez futtassa a következő parancsot a JSON-fájl kimenetében lévő nem megfelelő erőforrások erőforrás-azonosítóinak beolvasásához:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Az eredmények hasonlók ahhoz, amit általában az Azure Portal nézetében a **Nem megfelelő erőforrások** területen láthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához használja a következő parancsot:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Cserélje le a helyére `{scope}` a szabályzat-hozzárendelés első létrehozásakor használt hatókört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
