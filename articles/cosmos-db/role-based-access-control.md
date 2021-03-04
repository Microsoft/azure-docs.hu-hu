---
title: Azure szerepköralapú hozzáférés-vezérlés Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja az Azure Cosmos DB az adatbázis-védelmet az Active Directory-integrációval (Azure RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: 3a0f0f617e45834f1f205bcb4c8bcbc884b20d22
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048114"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Azure szerepköralapú hozzáférés-vezérlés Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Ez a cikk a felügyeleti sík Azure Cosmos DB-beli szerepköralapú hozzáférés-vezérlésével kapcsolatos tudnivalókat ismerteti. Ha adatsík-műveleteket használ, tekintse meg az adatsík-műveletekhez alkalmazott szerepköralapú hozzáférés-vezérlés [Azure Cosmos db RBAC](how-to-setup-rbac.md) .

A Azure Cosmos DB beépített Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít a gyakori felügyeleti forgatókönyvekhez Azure Cosmos DBban. A Azure Active Directory profillal rendelkező személy a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz rendelheti hozzá ezeket az Azure-szerepköröket az erőforrásokhoz és műveletekhez való hozzáférés engedélyezéséhez vagy megtagadásához Azure Cosmos DB erőforrásokon. A szerepkör-hozzárendelések hatóköre csak a csak vezérlőre vonatkozik, amely hozzáférést biztosít az Azure Cosmos-fiókok,-adatbázisok,-tárolók és-ajánlatok (átviteli sebesség) számára.

## <a name="built-in-roles"></a>Beépített szerepkörök

A Azure Cosmos DB által támogatott beépített szerepkörök a következők:

|**Beépített szerepkör**  |**Leírás**  |
|---------|---------|
|[DocumentDB-fiók közreműködői](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Felügyelheti Azure Cosmos DB fiókokat.|
|[Cosmos DB-fiók olvasója](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB fiókadatok olvasása.|
|[Cosmos Backup operátor](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Visszaállíthat egy visszaállítási kérést az Azure Portal számára egy rendszeres biztonsági mentési engedélyezett adatbázis vagy tároló számára. Módosíthatja a biztonsági mentés intervallumát és a Azure Portal megőrzését. Nem lehet hozzáférni semmilyen adattal, vagy nem használhatja a Adatkezelő.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | A képes a folyamatos biztonsági mentési móddal Azure Cosmos DB fiókra vonatkozó visszaállítási művelet végrehajtására.|
|[Cosmos DB operátor](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Az Azure Cosmos-fiókokat,-adatbázisokat és-tárolókat is kiépítheti. Nem lehet hozzáférni semmilyen adattal, vagy nem használhatja a Adatkezelő.|

> [!IMPORTANT]
> Az Azure Cosmos DB Azure RBAC-támogatás csak a vezérlési sík műveleteire vonatkozik. Az adatsíkok műveletei elsődleges kulcsok vagy erőforrás-tokenek használatával biztonságosak. További információ: az [adathozzáférés biztonságossá tétele Azure Cosmos db](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitás-és hozzáférés-kezelés (IAM)

A Azure Portal **hozzáférés-vezérlés (iam)** ablaktáblája az Azure-beli szerepköralapú hozzáférés-vezérlés konfigurálására szolgál az Azure Cosmos-erőforrásokon. A szerepköröket a rendszer a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra alkalmazza Active Directoryban. A felhasználók és csoportok számára beépített szerepköröket vagy egyéni szerepköröket is használhat. Az alábbi képernyőfelvételen a Azure Portal hozzáférés-vezérlés (IAM) használatával Active Directory integráció (Azure RBAC) látható:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Hozzáférés-vezérlés (IAM) a Azure Portal – az adatbázis biztonságának bemutatása.":::

## <a name="custom-roles"></a>Egyéni szerepkörök

A beépített szerepkörökön kívül a felhasználók [Egyéni szerepköröket](../role-based-access-control/custom-roles.md) is létrehozhatnak az Azure-ban, és ezeket a szerepköröket a Active Directory bérlőn belüli összes előfizetéshez alkalmazhatják az egyes szolgáltatásokra. Az egyéni szerepkörök lehetővé teszik a felhasználók számára, hogy Azure-szerepkör-definíciókat hozzanak létre az erőforrás-szolgáltatói műveletek egyéni készletével. Annak megismeréséhez, hogy mely műveletek érhetők el a Azure Cosmos DB egyéni szerepköreinek létrehozásához: [Azure Cosmos db erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> Azok az egyéni szerepkörök, amelyeknek Cosmos DB vagy a Azure Portal Adatkezelő használatával kell elérniük az adatelérést, `Microsoft.DocumentDB/databaseAccounts/listKeys/*` művelettel kell rendelkezniük.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>A Azure Cosmos DB SDK-k változásainak megakadályozása

A Azure Cosmos DB erőforrás-szolgáltató zárolható, így megakadályozható, hogy az ügyfelek az Azure Cosmos SDK-n keresztül csatlakozó és a fiók kulcsainak használatával összekapcsolják az erőforrások változásait. Ez a Azure Portal végrehajtott módosításokat is magában foglalja. Ez a funkció olyan felhasználók számára lehet hasznos, akik magasabb fokú irányítást és irányítást kívánnak a termelési környezetekben. Az SDK változásainak megakadályozása lehetővé teszi olyan funkciók használatát is, mint például az erőforrás-zárolások és a diagnosztikai naplók a vezérlési sík műveleteihez. A Azure Cosmos DB SDK-hoz csatlakozó ügyfeleket a rendszer nem módosítja az Azure Cosmos-fiókok,-adatbázisok,-tárolók és-átviteli sebességek bármely tulajdonságát. Az adatok Cosmos-tárolóba történő olvasását és írását érintő műveletek nincsenek hatással a rendszerre.

Ha ez a funkció engedélyezve van, bármely erőforrás módosítása csak a megfelelő Azure-szerepkörrel rendelkező felhasználóktól és Azure Active Directory hitelesítő adatokkal, beleértve a felügyelt szolgáltatás identitásait is.

> [!WARNING]
> A funkció engedélyezése hatással lehet az alkalmazásra. Győződjön meg arról, hogy az engedélyezése előtt megérti a hatást.

### <a name="check-list-before-enabling"></a>Az engedélyezés előtt keresse meg a listát

Ezzel a beállítással megakadályozható, hogy bármely Cosmos-erőforrás bármely, a fiók kulcsait, például a Cosmos DB SDK-t, a fiók kulcsain keresztül csatlakozó eszközöket, illetve a Azure Portal. A szolgáltatás engedélyezése után az alkalmazásokkal kapcsolatos problémák és hibák megelőzése érdekében ellenőrizze, hogy az alkalmazások vagy Azure Portal-felhasználók a következő műveletek bármelyikét végrehajtják-e a funkció engedélyezése előtt, beleértve a következőket:

- A Cosmos-fiók bármilyen módosítása, beleértve a tulajdonságokat, illetve a régiók hozzáadását vagy eltávolítását.

- Alárendelt erőforrások, például adatbázisok és tárolók létrehozása, törlése. Ide tartoznak a más API-k, például a Cassandra, a MongoDB, a Gremlin és a Table-erőforrások erőforrásai.

- Adatbázis vagy tároló szintű erőforrások átviteli sebességének frissítése.

- A tároló tulajdonságainak módosítása, beleértve az index szabályzatot, az ÉLETTARTAMot és az egyedi kulcsokat.

- Tárolt eljárások, eseményindítók vagy felhasználó által definiált függvények módosítása.

Ha az alkalmazások (vagy Azure Portalon keresztüli felhasználók) végrehajtják ezeket a műveleteket, azokat át kell telepíteni az ARM- [sablonok](./manage-with-templates.md), a [PowerShell](manage-with-powershell.md), az [Azure CLI](manage-with-cli.md), a REST vagy az [Azure felügyeleti könyvtár](https://github.com/Azure-Samples/cosmos-management-net)használatával. Vegye figyelembe, hogy az Azure-felügyelet [több nyelven](/azure/?product=developer-tools#languages-and-tools)is elérhető.

### <a name="set-via-arm-template"></a>Beállítás ARM-sablonnal

Ha a tulajdonságot ARM-sablonnal szeretné beállítani, frissítse meglévő sablonját, vagy exportáljon egy új sablont a jelenlegi központi telepítéshez, majd adja meg az `"disableKeyBasedMetadataWriteAccess": true` `databaseAccounts` erőforrások tulajdonságait. Az alábbi példa egy Azure Resource Manager sablon alapszintű példája ennek a tulajdonságnak a beállításával.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Ügyeljen rá, hogy a fiókja és a gyermek erőforrásainak egyéb tulajdonságait is tartalmazza, ha redploying ezzel a tulajdonsággal. Ne telepítse a sablont úgy, hogy az az legyen, vagy alaphelyzetbe állítja az összes fiók tulajdonságait.

### <a name="set-via-azure-cli"></a>Beállítás az Azure CLI-n keresztül

Az Azure CLI használatának engedélyezéséhez használja az alábbi parancsot:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Beállítás a PowerShell használatával

A Azure PowerShell használatának engedélyezéséhez használja az alábbi parancsot:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md)
- [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md)
- [Erőforrás-szolgáltatói műveletek Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
