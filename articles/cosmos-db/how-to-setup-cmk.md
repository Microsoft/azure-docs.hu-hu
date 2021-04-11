---
title: Ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Cosmos DB-fiókjához a Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/19/2021
ms.author: thweiss
ms.openlocfilehash: 3ee566a598ea7fdf060712c934305ef63467e548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656516"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurálja a felhasználó által kezelt kulcsokat az Azure Cosmos-fiókjához az Azure Key Vaulttal
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Az ügyfél által felügyelt kulcsok Azure Cosmos DB [analitikus tárolóval](analytical-store-introduction.md) való használata jelenleg további konfigurálást igényel a fiókjában. Kérjük, lépjen kapcsolatba [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) a részletekkel.

Az Azure Cosmos-fiókban tárolt adatai automatikusan és zökkenőmentesen titkosítva vannak a Microsoft által kezelt kulcsokkal (**szolgáltatás által felügyelt kulcsok**). Lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a kezelt kulcsokkal (az **ügyfél által felügyelt kulcsokkal**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Az ügyféladatok körébe tartozó titkosítási rétegek":::

Az ügyfél által felügyelt kulcsokat a [Azure Key Vaultban](../key-vault/general/overview.md) kell tárolnia, és meg kell adnia egy kulcsot minden olyan Azure Cosmos-fiókhoz, amely engedélyezve van az ügyfél által felügyelt kulcsokkal. Ezzel a kulccsal lehet titkosítani az adott fiókban tárolt összes adathalmazt.

> [!NOTE]
> Az ügyfél által felügyelt kulcsok jelenleg csak az új Azure Cosmos-fiókok esetében érhetők el. Ezeket a fiók létrehozásakor kell konfigurálnia.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), nyissa meg az Azure-előfizetését, és válassza az **erőforrás-szolgáltatók** lehetőséget a **Beállítások** lapon:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Erőforrás-szolgáltatói bejegyzés a bal oldali menüben":::

1. Keresse meg a **Microsoft.DocumentDB** erőforrás-szolgáltatót. Ellenőrizze, hogy az erőforrás-szolgáltató már regisztráltként van-e megjelölve. Ha nem, válassza ki az erőforrás-szolgáltatót, és válassza a **regisztráció** lehetőséget:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="A Microsoft.DocumentDB erőforrás-szolgáltató regisztrálása":::

## <a name="configure-your-azure-key-vault-instance"></a>Az Azure Key Vault-példány konfigurálása

Az ügyfél által felügyelt kulcsok Azure Cosmos DB használata esetén a titkosítási kulcsok tárolásához használni kívánt Azure Key Vault-példányon két tulajdonságot kell beállítania: a helyreállítható **törlési** és **törlési védelem**.

Ha új Azure Key Vault példányt hoz létre, engedélyezze ezeket a tulajdonságokat a létrehozás során:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Az új Azure Key Vault példányok eltávolításának és törlésének engedélyezése":::

Ha meglévő Azure Key Vault példányt használ, ellenőrizheti, hogy ezek a tulajdonságok engedélyezve vannak-e a Azure Portal **Tulajdonságok** szakaszának megtekintésével. Ha a tulajdonságok bármelyike nincs engedélyezve, tekintse meg a következő cikkek egyikében a "Soft-delete engedélyezése" és a "kiürítés engedélyezése" szakaszt:

- [A Soft delete használata a PowerShell-lel](../key-vault/general/key-vault-recovery.md)
- [A Soft delete használata az Azure CLI-vel](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

1. A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. A bal oldali menüben válassza a **hozzáférési szabályzatok** lehetőséget:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Hozzáférési szabályzatok a bal oldali menüben":::

1. Válassza a **+ hozzáférési házirend hozzáadása** lehetőséget.

1. A **kulcs engedélyei** legördülő menüben válassza a **lekérés**, a **kicsomagolás** és a **kulcsok becsomagolása** lehetőséget:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="A megfelelő engedélyek kiválasztása":::

1. A **rendszerbiztonsági tag kiválasztása** területen válassza a **nincs kiválasztva** lehetőséget. Ezután keressen rá **Azure Cosmos db** rendszerbiztonsági tag elemre, és válassza ki azt (hogy könnyebb legyen megkeresni, a résztvevő azonosítója: `a232010e-820c-4083-83bb-3ace5fc29d0b` bármely Azure-régió esetében, Azure Government kivéve a rendszerbiztonsági tag azonosítóját `57506a73-e302-42a9-b869-6f12d9ec29e9` ). Végül válassza a **kijelölés alul lehetőséget** . Ha a **Azure Cosmos db** rendszerbiztonsági tag nem szerepel a listában, akkor előfordulhat, hogy újra kell regisztrálnia a **Microsoft.DocumentDB** erőforrás-szolgáltatót a jelen cikk [erőforrás-szolgáltató regisztrálása](#register-resource-provider) című szakaszában leírtak szerint.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Azure Cosmos DB rendszerbiztonsági tag kiválasztása":::

1. Válassza a **Hozzáadás** lehetőséget az új hozzáférési szabályzat hozzáadásához.

1. Válassza a **Mentés** lehetőséget az Key Vault példányon az összes módosítás mentéséhez.

## <a name="generate-a-key-in-azure-key-vault"></a>Kulcs létrehozása Azure Key Vault

1. A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. Ezután válassza a bal oldali menü **kulcsok** elemét:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Kulcsok bejegyzése a bal oldali menüben":::

1. Válassza a **Létrehozás/importálás** lehetőséget, adja meg az új kulcs nevét, és válasszon ki egy RSA-kulcs méretét. Legalább 3072 ajánlott a legjobb biztonság érdekében. Ezután válassza a **Létrehozás** elemet:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Új kulcs létrehozása":::

1. A kulcs létrehozása után válassza ki az újonnan létrehozott kulcsot, majd a jelenlegi verzióját.

1. Másolja a kulcs **azonosítóját** a legutolsó perjel utáni rész kivételével:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="A kulcs kulcs-azonosítójának másolása":::

## <a name="create-a-new-azure-cosmos-account"></a>Új Azure Cosmos-fiók létrehozása

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Amikor új Azure Cosmos DB fiókot hoz létre a Azure Portal, a **titkosítási** lépésben válassza az **ügyfél által felügyelt kulcs** elemet. Illessze be a **kulcs URI** mezőjébe az előző lépésből másolt Azure Key Vault-kulcs URI-azonosítóját:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="CMK paraméterek beállítása a Azure Portalban":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell használata

Amikor új Azure Cosmos DB-fiókot hoz létre a PowerShell használatával:

- Adja át a **PropertyObject** **keyVaultKeyUri** tulajdonságában korábban átmásolt Azure Key Vault kulcs URI-ját.

- Használja az **2019-12-12** -es vagy újabb verziót API-verzióként.

> [!IMPORTANT]
> A `locations` tulajdonságot explicit módon kell beállítania ahhoz, hogy a fiók sikeresen létre legyen hozva az ügyfél által felügyelt kulcsokkal.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

A fiók létrehozása után ellenőrizheti, hogy az ügyfél által felügyelt kulcsok engedélyezve vannak-e a Azure Key Vault kulcs URI azonosítójának beolvasásával:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

Új Azure Cosmos-fiók létrehozásakor Azure Resource Manager sablonon keresztül:

- Adja meg a korábban a **Tulajdonságok** objektum **keyVaultKeyUri** tulajdonsága alatt másolt Azure Key Vault-kulcs URI-ját.

- Használja az **2019-12-12** -es vagy újabb verziót API-verzióként.

> [!IMPORTANT]
> A `locations` tulajdonságot explicit módon kell beállítania ahhoz, hogy a fiók sikeresen létre legyen hozva az ügyfél által felügyelt kulcsokkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Telepítse a sablont a következő PowerShell-parancsfájl használatával:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Az Azure CLI használata

Amikor új Azure Cosmos-fiókot hoz létre az Azure CLI-n keresztül, adja át a paraméterben korábban átmásolt Azure Key Vault kulcs URI-JÁT `--key-uri` .

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

A fiók létrehozása után ellenőrizheti, hogy az ügyfél által felügyelt kulcsok engedélyezve vannak-e a Azure Key Vault kulcs URI azonosítójának beolvasásával:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>Kulcsrotálás

Az Azure Cosmos-fiók által használt ügyfél által felügyelt kulcs elforgatása kétféleképpen végezhető el.

- Hozza létre az Azure Key Vault által jelenleg használt kulcs új verzióját:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Új kulcs verziójának létrehozása":::

- Cserélje le az aktuálisan használt kulcsot egy teljesen eltérőre, ha frissíti a kulcs URI-JÁT a fiókjában. A Azure Portal lépjen az Azure Cosmos-fiókjába, és válassza az **adattitkosítás** lehetőséget a bal oldali menüben:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Az adattitkosítási menü bejegyzése":::

    Ezután cserélje le a **kulcs URI-ját** a használni kívánt új kulcsra, majd válassza a **Mentés** lehetőséget:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="A kulcs URI-ja frissítése":::

    A PowerShell ugyanezt az eredményt éri el:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Az előző kulcs vagy kulcs verziószáma 24 óra elteltével letiltható, vagy ha a [Azure Key Vault naplók](../key-vault/general/logging.md) nem jelenítik meg az adott kulcs vagy kulcs verziójának Azure Cosmos db tevékenységeit.
    
## <a name="error-handling"></a>Hibakezelés

Ha a Azure Cosmos DBban Customer-Managed kulcsok (CMK) használatakor hiba történik, a Azure Cosmos DB a válaszban szereplő HTTP-alállapot-kóddal együtt adja vissza a hiba részleteit. Ezt az alállapot-kódot használhatja a probléma kiváltó okának a hibakereséséhez. Tekintse meg a http- [állapotkódok Azure Cosmos db](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) cikket a támogatott http-alállapot-kódok listájának lekéréséhez.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Díjköteles az ügyfél által felügyelt kulcsok?

Nem, a funkció engedélyezéséhez díjmentesen kell fizetni.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Hogyan befolyásolja az ügyfél által felügyelt kulcsok a kapacitás megtervezését?

Az ügyfél által felügyelt kulcsok használatakor az adatbázis-műveletek által felhasznált [kérelmek egységei](./request-units.md) az adatok titkosításának és visszafejtésének elvégzéséhez szükséges további feldolgozást tükrözik. Ez a kiépített kapacitás valamivel magasabb kihasználtságával járhat. Az alábbi táblázat segítségével útmutatást kaphat:

| Művelettípus | Kérés egységének növekedése |
|---|---|
| Pont – olvasás (elemek beolvasása az azonosító alapján) | + 5%/művelet |
| Bármilyen írási művelet | + 6%/művelet<br/>kb. + 0,06 RU/indexelt tulajdonság |
| Lekérdezések, változási hírcsatorna vagy ütközési csatorna olvasása | + 15%/művelet |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Milyen adattitkosítást kap az ügyfél által felügyelt kulcsokkal?

Az Azure Cosmos-fiókban tárolt összes adat titkosítva van az ügyfél által felügyelt kulcsokkal, a következő metaadatok kivételével:

- A Azure Cosmos DB [-fiókok,-adatbázisok és-tárolók](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account) neve

- A [tárolt eljárások](./stored-procedures-triggers-udfs.md) nevei

- Az [indexelési szabályzatokban](./index-policy.md) deklarált tulajdonságok elérési útjai

- A tárolók [partíciós kulcsainak](./partitioning-overview.md) értékei

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Támogatottak-e az ügyfél által felügyelt kulcsok a meglévő Azure Cosmos-fiókok esetében?

Ez a funkció jelenleg csak az új fiókok esetében érhető el.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Használhatók-e az ügyfél által felügyelt kulcsok a Azure Cosmos DB [analitikus tárolóval](analytical-store-introduction.md)együtt?

Igen, de ez jelenleg további konfigurációt igényel a fiókjában. Kérjük, lépjen kapcsolatba [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) a részletekkel.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Van olyan terv, amely támogatja a legfinomabb részletességet, mint a fiók szintű kulcsok?

Jelenleg nem, de a tároló szintű kulcsok is megtekinthetők.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Honnan tudhatom meg, hogy az ügyfél által felügyelt kulcsok engedélyezve vannak-e az Azure Cosmos-fiókomban?

A Azure Portal lépjen az Azure Cosmos-fiókjába, és figyelje meg a bal oldali menüben található **adattitkosítási** bejegyzést. Ha a bejegyzés létezik, az ügyfél által felügyelt kulcsok engedélyezve vannak a fiókjában:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Az adattitkosítási menü bejegyzése":::

Programozott módon is beolvashatja az Azure Cosmos-fiók adatait, és megkeresheti a tulajdonság jelenlétét `keyVaultKeyUri` . A fenti módokon megtekintheti a [PowerShellben](#using-powershell) és [Az Azure CLI használatával](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Hogyan érintik az ügyfél által felügyelt kulcsok a biztonsági mentést?

A Azure Cosmos DB [rendszeres és automatikus biztonsági mentést](./online-backup-and-restore.md) készít a fiókjában tárolt adatairól. Ezzel a művelettel biztonsági másolatot készít a titkosított adatforgalomról. A visszaállított biztonsági mentés használatához a biztonsági mentés időpontjában használt titkosítási kulcsot kötelező megadni. Ez azt jelenti, hogy nem történt visszavonás, és a biztonsági mentés idején használt kulcs verziószáma továbbra is engedélyezve lesz.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hogyan visszavon egy titkosítási kulcsot?

A kulcs visszavonása a kulcs legújabb verziójának letiltásával végezhető el:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Kulcs verziójának letiltása":::

Másik lehetőségként a Azure Key Vault-példány összes kulcsának visszavonásához törölheti a Azure Cosmos DB rendszerbiztonsági tag számára biztosított hozzáférési szabályzatot:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="A Azure Cosmos DB rendszerbiztonsági tag hozzáférési szabályzatának törlése":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Milyen műveletek érhetők el az ügyfél által felügyelt kulcs visszavonása után?

Az egyetlen művelet lehetséges, ha a titkosítási kulcs visszavonva van, a fiók törlése.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Cosmos db adattitkosításáról](./database-encryption-at-rest.md).
- Tekintse át a [Cosmos db lévő adathozzáférések biztonságos elérését](secure-access-to-data.md)ismertető cikket.