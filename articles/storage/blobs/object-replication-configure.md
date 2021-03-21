---
title: Objektumreplikáció konfigurálása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az objektum-replikációt úgy, hogy aszinkron módon másolja a blokk blobokat egy tárolóból egy másikba.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2b6855d72b644a3fe1fa46c883eb7414383a1a57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031701"
---
# <a name="configure-object-replication-for-block-blobs"></a>Objektumok replikálásának konfigurálása a blokkos blobokhoz

Az objektum-replikáció aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálásával kapcsolatos további információkért lásd: [objektumok replikálása](object-replication-overview.md).

Az objektumok replikálásának konfigurálásakor létre kell hoznia egy replikációs házirendet, amely megadja a forrás Storage-fiókot és a célként megadott fiókot. A replikációs házirend egy vagy több olyan szabályt tartalmaz, amely a forrás tárolót és a célhelyet adja meg, és jelzi, hogy a rendszer melyik blokk blobokat replikálja a forrás tárolóban.

Ez a cikk azt ismerteti, hogyan konfigurálható az objektum replikálása a Storage-fiókhoz a Azure Portal, a PowerShell vagy az Azure CLI használatával. Az objektumok replikálásának konfigurálásához használhatja az Azure Storage erőforrás-szolgáltató ügyfél-kódtárainak egyikét is.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Replikációs házirend és szabályok létrehozása

Az objektumok replikálásának konfigurálása előtt hozza létre a forrás és a cél Storage-fiókokat, ha azok még nem léteznek. Mindkét fióknak általános célú v2 Storage-fióknak kell lennie. További információ: [Azure Storage-fiók létrehozása](../common/storage-account-create.md).

Az objektum-replikációhoz a blob verziószámozásának engedélyezése szükséges a forrás-és a cél fiókhoz is, és a blob-változási csatorna engedélyezve van a forrás fiók számára. A blob verziószámozásával kapcsolatos további tudnivalókért tekintse meg a [blob verziószámozását](versioning-overview.md)ismertető témakört. További információ a hírcsatornák változásáról: [a hírcsatornák támogatásának módosítása az Azure Blob Storageban](storage-blob-change-feed.md). Ne feledje, hogy ezeknek a szolgáltatásoknak a engedélyezése további költségeket eredményezhet.

A Storage-fiók legfeljebb két célobjektum forrásaként használható. Előfordulhat, hogy a forrás-és a cél fiók ugyanabban a régióban vagy különböző régiókban található. Emellett különböző előfizetésekben és különböző Azure Active Directory (Azure AD-) bérlőben is lehetnek. Minden egyes fiókhoz csak egy replikációs házirend hozható létre.

Az objektumok replikálásának konfigurálásakor az Azure Storage erőforrás-szolgáltatón keresztül létre kell hoznia egy replikációs házirendet a célhelyen. A replikációs házirend létrehozása után az Azure Storage egy házirend-azonosítót rendel hozzá. Ezt követően a házirend-azonosító használatával kell hozzárendelni a replikációs házirendet a forrás-fiókhoz. A replikáció végrehajtásához meg kell egyeznie a forrás-és a célhelyen lévő házirend-AZONOSÍTÓval.

A Storage-fiókhoz tartozó objektum-replikációs házirend konfigurálásához hozzá kell rendelnie a Azure Resource Manager **közreműködő** szerepkört, amely a Storage-fiók szintjére vagy magasabbra van korlátozva. További információ: [Azure-beli beépített szerepkörök](../../role-based-access-control/built-in-roles.md) az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) dokumentációjában.

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Az objektumok replikálásának konfigurálása, ha mindkét Storage-fiókhoz hozzáfér

Ha rendelkezik hozzáféréssel a forrás-és a cél Storage-fiókhoz is, akkor mindkét fiókon konfigurálhatja az objektum-replikációs házirendet.

Mielőtt az objektum-replikálást konfigurálja a Azure Portalban, hozza létre a forrás-és a cél tárolókat a megfelelő Storage-fiókjaiban, ha még nem léteznek. Engedélyezze a blob verziószámozását is, és módosítsa a forrás fiókon a hírcsatornát, és engedélyezze a blob verziószámozását a célhelyen.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal a fiók konfigurálása után automatikusan létrehozza a szabályzatot a forrás fiókban.

Ha a Azure Portal replikációs szabályzatot szeretne létrehozni, kövesse az alábbi lépéseket:

1. Navigáljon a forrás Storage-fiókhoz a Azure Portal.
1. A **blob Service** területen válassza az **objektum-replikáció** elemet.
1. Válassza a **replikációs szabályok beállítása** lehetőséget.
1. Válassza ki a cél előfizetést és a Storage-fiókot.
1. A **Container Pairs (tároló párok** ) szakaszban válasszon ki egy forrásoldali tárolót a forrás fiókból, valamint egy célhelyet a célhelyről. Replikációs házirendben legfeljebb 10 tároló pár hozható létre.

    Az alábbi képen a replikációs szabályok halmaza látható.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="A Azure Portal replikációs szabályait ábrázoló képernyőkép":::

1. Ha kívánja, egy vagy több szűrőt is megtalálhat, hogy csak az előtag mintájának megfelelő blobokat másolja. Ha például egy előtagot ad meg `b` , csak azok a Blobok replikálódnak, amelyek neve ezzel a betűvel kezdődik. A virtuális könyvtárat az előtag részeként is megadhatja. Az előtag-karakterlánc nem támogatja a helyettesítő karaktereket.

    Az alábbi képen olyan szűrők láthatók, amelyek korlátozzák, hogy mely Blobok legyenek átmásolva egy replikációs szabály részeként.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="A replikációs szabály szűrőinek megjelenítését bemutató képernyőkép":::

1. Alapértelmezés szerint a másolási hatókör úgy van beállítva, hogy csak az új objektumokat másolja. Ha a tárolóban lévő összes objektumot át szeretné másolni, vagy egyéni dátumból és időpontból szeretné másolni az objektumokat, válassza a **módosítás** hivatkozást, és konfigurálja a tároló pár másolási hatókörét.

    Az alábbi képen egy egyéni másolási hatókör látható, amely az objektumokat egy adott dátumtól és időponttól kezdve másolja.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Az objektumok replikálásának egyéni másolási hatókörét bemutató képernyőkép":::

1. Válassza a **Mentés és az alkalmaz** lehetőséget a replikációs házirend létrehozásához és az adatreplikálás megkezdéséhez.

Az objektumok replikálásának konfigurálása után a Azure Portal megjeleníti a replikációs házirendet és a szabályokat, ahogy az az alábbi képen is látható.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="A Azure Portal objektum-replikációs házirendjét ábrázoló képernyőfelvétel":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell használatával létrehozott replikációs házirend létrehozásához először telepítse az az. Storage PowerShell-modult [, vagy újabb](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) verziót. További információ a Azure PowerShell telepítéséről: [Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps).

Az alábbi példa azt szemlélteti, hogyan hozható létre replikációs házirend a forrás-és a célhelyen. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való replikációs házirend létrehozásához először telepítse az Azure CLI-verziót 2.11.1 vagy újabb verzióra. További információ: Ismerkedés [Az Azure CLI-vel](/cli/azure/get-started-with-azure-cli).

Ezután engedélyezze a blob-verziószámozást a forrás-és a cél Storage-fiókokon, és engedélyezze a fiók módosítási csatornáját az az [Storage Account blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) parancs meghívásával. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Hozza létre a forrás-és a cél tárolókat a megfelelő Storage-fiókjaiban.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Hozzon létre egy új replikációs házirendet és egy társított szabályt a célhelyen az az [Storage Account vagy a-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create)paranccsal.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Az Azure Storage az új szabályzathoz tartozó házirend-azonosítót állítja be a létrehozásakor. Ha további szabályokat szeretne felvenni a szabályzatba, hívja meg az az [Storage Account vagy a-Policy szabályt](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) , és adja meg a szabályzat azonosítóját.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Ezután hozza létre a szabályzatot a forrásoldali fiókban a házirend-azonosító használatával.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Az objektumok replikálásának konfigurálása, ha csak a célként megadott fiókhoz fér hozzá

Ha nem rendelkezik engedéllyel a forrásként szolgáló Storage-fiókhoz, konfigurálhatja az objektum-replikálást a célhelyen, és megadhat egy JSON-fájlt, amely tartalmazza a házirend-definíciót egy másik felhasználó számára, hogy ugyanazt a házirendet hozza létre a forrás fiókon. Ha például a forrásoldali fiók egy másik Azure AD-bérlőn található a célhelyen, akkor ezt a módszert használhatja az objektumok replikálásának konfigurálásához.

Ne feledje, hogy a házirend létrehozásához hozzá kell rendelnie a Azure Resource Manager **közreműködő** szerepkört a célként megadott Storage-fiók szintjére vagy magasabbra. További információ: [Azure-beli beépített szerepkörök](../../role-based-access-control/built-in-roles.md) az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) dokumentációjában.

A következő táblázat összefoglalja, hogy mely értékeket kell használni a JSON-fájlban a szabályzat-AZONOSÍTÓhoz és a szabály-azonosítóhoz.

| Ha létrehoz egy JSON-fájlt ehhez a fiókhoz... | Állítsa be a házirend-azonosítót erre az értékre. | Szabály-azonosítók beállítása erre az értékre |
|-|-|-|
| Cél fiók | A sztring *alapértelmezett* értéke. Az Azure Storage létrehozza a házirend-azonosító értékét. | Üres karakterlánc. Az Azure Storage létrehozza a szabály azonosító értékeit. |
| Forrásoldali fiók | Annak a házirend-AZONOSÍTÓnak az értéke, amelyet a rendszer a cél fiókban a JSON-fájlként megadott házirend letöltésekor adott vissza. | A rendszer a cél fiókban a JSON-fájlként megadott szabályzat letöltésekor visszaadott szabály-azonosítók értékeit adja vissza. |

Az alábbi példa egy replikációs házirendet definiál a célszámítógépen egyetlen olyan szabállyal, amely megfelel a *b* előtagnak, és beállítja a replikálni kívánt Blobok minimális létrehozási idejét. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A következő lépésekkel konfigurálhatja az objektumok replikálását a cél fiókban egy JSON-fájllal a Azure Portalban:

1. Hozzon létre egy helyi JSON-fájlt, amely meghatározza a cél fiók replikációs házirendjét. Állítsa a **policyId** mezőt az *alapértelmezett* értékre, hogy az Azure Storage meghatározza a szabályzat azonosítóját.

    A replikációs házirendet definiáló JSON-fájlok létrehozásának egyszerű módja, ha először létrehoz egy tesztelési replikációs házirendet a Azure Portal két Storage-fiókja között. Ezután letöltheti a replikációs szabályokat, és szükség szerint módosíthatja a JSON-fájlt.

1. A Azure Portalban navigáljon a cél fiókhoz tartozó **objektum-replikációs** beállításokhoz.
1. Válassza a **replikációs szabályok feltöltése** lehetőséget.
1. Töltse fel a JSON-fájlt. A Azure Portal megjeleníti a létrehozandó szabályzatot és szabályokat, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Képernyőfelvétel egy JSON-fájl feltöltéséről a replikációs házirend meghatározásához":::

1. Válassza a **feltöltés** lehetőséget a replikációs házirend létrehozásához a cél fiókon.

Ezután letöltheti azt a házirend-definíciót tartalmazó JSON-fájlt, amelyet megadhat egy másik felhasználónak a forrásoldali fiók konfigurálásához. A JSON-fájl letöltéséhez kövesse az alábbi lépéseket:

1. A Azure Portalban navigáljon a cél fiókhoz tartozó **objektum-replikációs** beállításokhoz.
1. Kattintson a letölteni kívánt szabályzat melletti **további** gombra, majd válassza a **szabályok letöltése** lehetőséget a következő képen látható módon.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="A replikációs szabályok JSON-fájlba való letöltését bemutató képernyőkép":::

1. Mentse a JSON-fájlt a helyi számítógépre, és ossza meg egy másik felhasználóval, hogy konfigurálja a házirendet a forrás fiókon.

A letöltött JSON-fájl tartalmazza azt a házirend-azonosítót, amelyet az Azure Storage hozott létre a fiókhoz a házirendhez. Az objektumok replikálásának konfigurálásához ugyanazt a házirend-azonosítót kell használnia a forrásoldali fiókban.

Ne feledje, hogy egy JSON-fájl feltöltésével hozzon létre egy replikációs házirendet a cél fiókhoz a Azure Portalon keresztül, nem hozza létre automatikusan ugyanazt a házirendet a forrás fiókban. Egy másik felhasználónak létre kell hoznia a szabályzatot a forrásoldali fiókon, mielőtt az Azure Storage megkezdi az objektumok replikálását.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Egy olyan JSON-fájl letöltéséhez, amely a cél fiókhoz tartozó replikációs házirend definícióját tartalmazza a PowerShellből, hívja meg a [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) parancsot a szabályzat visszaküldéséhez. Ezután alakítsa át a szabályzatot JSON-ra, és mentse helyi fájlként, ahogy az az alábbi példában is látható. Ne felejtse el lecserélni az értékeket a szögletes zárójelben, a fájl elérési útját pedig a saját értékeire:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Ha a JSON-fájllal szeretné definiálni a replikációs házirendet a PowerShell használatával, kérje le a helyi fájlt, és konvertálja a JSON-ből egy objektumra. Ezután hívja meg a [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) parancsot a házirend konfigurálásához a forrás fiókon, az alábbi példában látható módon. Ne felejtse el lecserélni az értékeket a szögletes zárójelben, a fájl elérési útját pedig a saját értékeire:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha meg szeretné írni a cél fiók replikációs szabályzatának definícióját az Azure CLI-ből származó JSON-fájlra, hívja meg az az [Storage Account vagy a-Policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) parancsot és a kimenetet egy fájlba.

A következő példa egy *policy.js* nevű JSON-fájlba írja a házirend-definíciót. Ne felejtse el lecserélni az értékeket a szögletes zárójelben, a fájl elérési útját pedig a saját értékeire:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Ha a JSON-fájl használatával szeretné konfigurálni a replikációs házirendet az Azure CLI-vel, hívja meg az az [Storage Account vagy a-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) parancsot, és hivatkozzon a *policy.js* fájlra. Ne felejtse el lecserélni az értékeket a szögletes zárójelben, a fájl elérési útját pedig a saját értékeire:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>BLOB replikálási állapotának megkeresése

A Azure Portal, a PowerShell vagy az Azure CLI használatával megtekintheti a Blobok replikálási állapotát a forrás fiókban. Az objektum-replikációs tulajdonságokat a rendszer nem tölti fel, amíg a replikáció nem fejeződött be vagy sikertelen.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alábbi lépéseket követve ellenőrizheti egy blob replikálási állapotát a Azure Portal forrásoldali fiókjában:

1. Navigáljon a Azure Portal található forrásoldali fiókhoz.
1. Keresse meg azt a tárolót, amely tartalmazza a forrás blobot.
1. Válassza ki a blobot a tulajdonságainak megjelenítéséhez. Ha a blob replikációja sikeresen megtörtént, az **objektum-replikálás** szakaszban láthatja, hogy az állapot be van-e *fejezni*. A rendszer a replikációs házirend AZONOSÍTÓját és a tároló objektum-replikálását szabályozó szabály AZONOSÍTÓját is felsorolja.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="A forrás fiókban található blob replikálási állapotát bemutató képernyőfelvétel":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShellben található blob replikálási állapotának megtekintéséhez szerezze be az objektum-replikációs **ReplicationStatus** tulajdonság értékét az alábbi példában látható módon. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel rendelkező blob replikálási állapotának megtekintéséhez szerezze be az objektum-replikációs **állapot** tulajdonság értékét az alábbi példában látható módon:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Ha a forrás fiókban található blob replikációs állapota hibát jelez, akkor vizsgálja meg a következő lehetséges okokat:

- Győződjön meg arról, hogy az objektum-replikációs házirend konfigurálva van a célhelyen.
- Ellenőrizze, hogy a cél tároló még létezik-e.
- Ha a forrás blob egy ügyfél által megadott kulccsal lett titkosítva egy írási művelet részeként, akkor az objektum replikálása sikertelen lesz. Az ügyfél által biztosított kulcsokkal kapcsolatos további információkért lásd: [titkosítási kulcs megadása a blob Storage-hoz való kérelemben](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Replikációs házirend eltávolítása

A replikációs házirend és a hozzá tartozó szabályok eltávolításához használja a Azure Portal, a PowerShell vagy a parancssori felületet.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha el szeretné távolítani egy replikációs házirendet a Azure Portalban, kövesse az alábbi lépéseket:

1. Navigáljon a forrás Storage-fiókhoz a Azure Portal.
1. A **Beállítások** területen válassza az **objektum replikálása** elemet.
1. Kattintson a házirend neve melletti **további** gombra.
1. Válassza a **szabályok törlése** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A replikációs szabályzat eltávolításához törölje a megfelelő házirendet a forrás-és a cél fiókból is. A házirend törlése a hozzá társított szabályokat is törli.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A replikációs szabályzat eltávolításához törölje a megfelelő házirendet a forrás-és a cél fiókból is. A házirend törlése a hozzá társított szabályokat is törli.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Következő lépések

- [Objektum-replikáció áttekintése](object-replication-overview.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
- [Folyamat módosítása az Azure Blob Storage-ban](storage-blob-change-feed-how-to.md)
