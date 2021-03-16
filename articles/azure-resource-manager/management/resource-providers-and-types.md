---
title: Erőforrás-szolgáltatók és-erőforrástípusok
description: A Azure Resource Managert támogató erőforrás-szolgáltatókat ismerteti. Ismerteti a sémákat, az elérhető API-verziókat, valamint azokat a régiókat, amelyek tárolhatják az erőforrásokat.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 584f3065d0e696f2ee379a8cf6c048994a1e68d5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493135"
---
# <a name="azure-resource-providers-and-types"></a>Azure-beli erőforrás-szolgáltatók és -típusok

Erőforrások telepítésekor gyakran kell lekérnie az erőforrás-szolgáltatókkal és-típusokkal kapcsolatos információkat. Ha például kulcsokat és titkos kulcsokat szeretne tárolni, a Microsoft.KeyVault erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy vaults nevű erőforrástípust biztosít a kulcstároló létrehozásához.

Az erőforrástípus nevének formátuma: **{erőforrás-szolgáltató}/{erőforrástípus}**. A kulcstartó erőforrástípus a Microsoft. Key Vault **/Vaults**.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Az összes erőforrás-szolgáltató megtekintése az Azure-ban
* Erőforrás-szolgáltató regisztrációs állapotának keresése
* Erőforrás-szolgáltató regisztrálása
* Erőforrás-szolgáltatók erőforrás-típusainak megtekintése
* Egy adott erőforrástípus érvényes helyeinek megtekintése
* Egy adott erőforrástípus érvényes API-verzióinak megtekintése

Ezeket a lépéseket a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával hajthatja végre.

Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz leképező listán tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](azure-services-resource-providers.md)ismertető témakört.

## <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az erőforrás-szolgáltató használata előtt az Azure-előfizetést regisztrálni kell az erőforrás-szolgáltatóhoz. A regisztráció konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. Egyes erőforrás-szolgáltatók alapértelmezés szerint regisztrálva vannak. Az alapértelmezés szerint regisztrált erőforrás-szolgáltatók listáját az Azure- [szolgáltatások erőforrás-szolgáltatói](azure-services-resource-providers.md)című részében tekintheti meg.

Más erőforrás-szolgáltatók automatikusan regisztrálva vannak, amikor bizonyos műveleteket végez. Azure Resource Manager sablon központi telepítésekor a rendszer az összes szükséges erőforrás-szolgáltatót automatikusan regisztrálja. Amikor létrehoz egy erőforrást a portálon keresztül, az erőforrás-szolgáltató általában regisztrálva van. Más forgatókönyvek esetében előfordulhat, hogy manuálisan kell regisztrálnia egy erőforrás-szolgáltatót. 

Ez a cikk bemutatja, hogyan ellenőrizhető az erőforrás-szolgáltató regisztrációs állapota, és szükség szerint regisztrálható. Engedéllyel kell rendelkeznie a művelet végrehajtásához `/register/action` az erőforrás-szolgáltatón. Az engedélyt a közreműködő és a tulajdonosi szerepkör tartalmazza.

> [!IMPORTANT]
> Csak akkor regisztrálja az erőforrás-szolgáltatót, amikor készen áll a használatra. A regisztrációs lépés lehetővé teszi a legalacsonyabb jogosultságok fenntartását az előfizetésen belül. Egy rosszindulatú felhasználó nem használhat olyan erőforrás-szolgáltatót, amely nincs regisztrálva.

Az alkalmazás kódjának nem szabad letiltani a **regisztrálási** állapotban lévő erőforrás-szolgáltató erőforrásainak létrehozását. Ha regisztrálja az erőforrás-szolgáltatót, a művelet minden egyes támogatott régió esetében külön történik. Ahhoz, hogy erőforrásokat hozzon létre egy régióban, a regisztrációt csak az adott régióban kell végrehajtani. Ha nem blokkolja az erőforrás-szolgáltatót a regisztrálási állapotban, az alkalmazása sokkal hamarabb folytatható, mint az összes régió befejezésére való várakozás.

Nem törölheti az erőforrás-szolgáltató regisztrációját, ha továbbra is az adott erőforrás-szolgáltatótól származó erőforrástípusok vannak az előfizetésben.

## <a name="azure-portal"></a>Azure Portal

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az összes erőforrás-szolgáltató megjelenítéséhez és az előfizetés regisztrációs állapotának megtekintéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal menüben keressen **előfizetéseket**. Válassza ki az elérhető lehetőségek közül.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="előfizetések keresése":::

1. Válassza ki a megtekinteni kívánt előfizetést.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="előfizetések kiválasztása":::

1. A bal oldali menüben, a **Beállítások** területen válassza az **erőforrás-szolgáltatók** elemet.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="erőforrás-szolgáltatók kiválasztása":::

6. Keresse meg a regisztrálni kívánt erőforrás-szolgáltatót, és válassza a **regisztráció** lehetőséget. Az előfizetéshez tartozó legalacsonyabb jogosultságok fenntartásához csak azokat az erőforrás-szolgáltatókat regisztrálja, amelyeket készen áll a használatra.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="erőforrás-szolgáltatók regisztrálása":::

### <a name="view-resource-provider"></a>Erőforrás-szolgáltató megtekintése

Egy adott erőforrás-szolgáltató információinak megtekintéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.
3. A **minden szolgáltatás** mezőben adja meg az **erőforrás-kezelőt**, majd válassza a **erőforrás-kezelő** lehetőséget.

    ![Minden szolgáltatás kiválasztása](./media/resource-providers-and-types/select-resource-explorer.png)

4. Bontsa ki a **szolgáltatók** elemet a jobbra mutató nyíl kiválasztásával.

    ![Szolgáltatók kiválasztása](./media/resource-providers-and-types/select-providers.png)

5. Bontsa ki a megtekinteni kívánt erőforrás-szolgáltatót és erőforrás-típust.

    ![Erőforrás típusának kiválasztása](./media/resource-providers-and-types/select-resource-type.png)

6. A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Az előfizetése korlátozásokat is tartalmazhat, amelyek megakadályozzák, hogy az erőforrást támogató régiókat használjanak. Az erőforrás-kezelő az erőforrástípus érvényes helyét jeleníti meg.

    ![Helyszínek megjelenítése](./media/resource-providers-and-types/show-locations.png)

7. Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki. Az erőforrás-kezelő az erőforrástípus érvényes API-verzióit jeleníti meg.

    ![API-verziók megjelenítése](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőt:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az előfizetéshez tartozó összes regisztrált erőforrás megtekintéséhez használja a következőt:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Az előfizetéshez tartozó legalacsonyabb jogosultságok fenntartásához csak azokat az erőforrás-szolgáltatókat regisztrálja, amelyeket készen áll a használatra. Erőforrás-szolgáltató regisztrálásához használja a következőt:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Egy adott erőforrás-szolgáltató információinak megtekintéséhez használja az alábbiakat:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Az erőforrás-szolgáltató erőforrásainak megtekintéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Amely a következőket adja vissza:

```output
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki.

Az erőforrástípus elérhető API-verzióinak beszerzéséhez használja a következőt:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Amely a következőket adja vissza:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Az előfizetése korlátozásokat is tartalmazhat, amelyek megakadályozzák, hogy az erőforrást támogató régiókat használjanak.

Az erőforrástípus támogatott helyeinek beszerzéséhez használja a következőt:.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Amely a következőket adja vissza:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőt:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az előfizetéshez tartozó összes regisztrált erőforrás megtekintéséhez használja a következőt:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Az előfizetéshez tartozó legalacsonyabb jogosultságok fenntartásához csak azokat az erőforrás-szolgáltatókat regisztrálja, amelyeket készen áll a használatra. Erőforrás-szolgáltató regisztrálásához használja a következőt:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Ekkor egy üzenetet ad vissza, amely szerint a regisztráció folyamatban van.

Egy adott erőforrás-szolgáltató információinak megtekintéséhez használja az alábbiakat:

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Az erőforrás-szolgáltató erőforrásainak megtekintéséhez használja a következőt:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki.

Az erőforrástípus elérhető API-verzióinak beszerzéséhez használja a következőt:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Az előfizetése korlátozásokat is tartalmazhat, amelyek megakadályozzák, hogy az erőforrást támogató régiókat használjanak.

Az erőforrástípus támogatott helyeinek beszerzéséhez használja a következőt:.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Következő lépések

* A Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok](../templates/template-syntax.md)készítése. 
* Az erőforrás-szolgáltatói sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).
* Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz leképező listán tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](azure-services-resource-providers.md)ismertető témakört.
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez lásd: [Azure REST API](/rest/api/).
