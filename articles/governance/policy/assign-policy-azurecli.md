---
title: 'Gyors útmutató: új szabályzat-hozzárendelés az Azure CLI-vel'
description: Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9955f911f9a92d7b353a8f3d022af7884b5a6aae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090138"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához az Azure CLI-vel

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató az Azure CLI használatával hoz létre szabályzat-hozzárendelést az Azure környezetben lévő nem megfelelő erőforrások azonosításához.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

- Regisztrálja a Azure Policy bepillantást erőforrás-szolgáltatót az Azure CLI használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Az erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az erőforrás-szolgáltató regisztrálása művelethez. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információkért lásd: [erőforrás-szolgáltatók és-típusok](../../azure-resource-manager/management/resource-providers-and-types.md)

- Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban létre fog hozni egy szabályzat-hozzárendelést, és hozzá fogja rendeli a **Felügyelt lemezek nélküli virtuális gépek naplózása** definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Futtassa a következő parancsot egy szabályzat-hozzárendelés létrehozásához:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Az előző parancs a következő információkat használja:

- **Name** – A hozzárendelés tényleges neve. A fenti példában az _audit-vm-manageddisks_ nevet használtuk.
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_ történik.
- **Szabályzat** – A szabályzatdefiníció azonosítója, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_. A szabályzatdefiníció azonosítójának lekéréséhez futtassa ezt a parancsot: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

Az új hozzárendelésnek nem megfelelő erőforrások megtekintéséhez kérje le a szabályzat-hozzárendelés azonosítóját a következő parancsok futtatásával:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

További információ a szabályzat-hozzárendelési azonosítókkal kapcsolatban: [az Policy-hozzárendelés](/cli/azure/policy/assignment).

Ezután futtassa a következő parancsot a JSON-fájlba kerülő nem megfelelő erőforrások azonosítójának lekéréséhez:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Az eredmények hasonlók ahhoz, amit általában az Azure Portal nézetében a **Nem megfelelő erőforrások** területen láthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához használja a következő parancsot:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
