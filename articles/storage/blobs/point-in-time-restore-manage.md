---
title: Időponthoz tartozó visszaállítás végrehajtása a blob-adatok blokkolása közben
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja vissza az adott időponthoz tartozó blokk-Blobok egy készletét egy adott időpontban az időponthoz tartozó visszaállítás használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071647"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Időponthoz tartozó visszaállítás végrehajtása a blob-adatok blokkolása közben

Az időponthoz tartozó visszaállítással visszaállíthat egy vagy több blokkos blobot egy korábbi állapotba. Ez a cikk azt ismerteti, hogyan engedélyezhető a tárolási fiók időponthoz való visszaállítása és a visszaállítási művelet végrehajtása.

Ha többet szeretne megtudni az időponthoz tartozó visszaállításról, tekintse meg [az időponthoz való visszaállítást a blokkos Blobok esetében](point-in-time-restore-overview.md).

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokkos Blobok műveleteinek visszaállítását. A tárolók műveletei nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból a [tároló törlése](/rest/api/storageservices/delete-container) művelet meghívásával, a tároló nem állítható vissza visszaállítási művelettel. A teljes tároló törlése helyett törölje az egyes blobokat, ha később szeretné visszaállítani őket. A Microsoft emellett azt is javasolja, hogy a tárolók és Blobok törlését a véletlen törlés elleni védelem érdekében engedélyezze. További információ: a [tárolók helyreállítható törlése (előzetes verzió)](soft-delete-container-overview.md) és [a Blobok Soft delete](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Időponthoz tartozó visszaállítás engedélyezése és konfigurálása

Az időponthoz való visszaállítás engedélyezése és konfigurálása előtt engedélyezze a Storage-fiókra vonatkozó előfeltételeket: Soft DELETE, Change feed és blob Versioning. Az egyes funkciók engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Blobok helyreállítható törlésének engedélyezése](./soft-delete-blob-enable.md)
- [A módosítási csatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)

> [!IMPORTANT]
> A Soft DELETE, a Change feed és a blob verziószámozásának engedélyezése további díjakat eredményezhet. További információ: a [Blobok](soft-delete-blob-overview.md)helyreállítható törlése, [a hírcsatornák támogatásának módosítása az Azure Blob Storageban](storage-blob-change-feed.md)és a [blob verziószámozása](versioning-overview.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal időponthoz való visszaállításának konfigurálásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A **Beállítások** területen válassza **az adatvédelem** lehetőséget.
1. Jelölje be az **időponthoz való visszaállítás bekapcsolása** beállítást. Ha ezt a beállítást választja, a rendszer a Blobok, verziószámozás és módosítási csatorna törlését is engedélyezi.
1. Adja meg a maximális visszaállítási pontot az időponthoz tartozó visszaállításhoz napokban. Ennek a számnak legalább egy nappal kisebbnek kell lennie, mint a blob törléséhez megadott megőrzési időtartam.
1. Mentse a módosításokat.

Az alábbi képen egy olyan Storage-fiók látható, amely az időponthoz tartozó visszaállításhoz van konfigurálva, és egy hét napja visszaállítási ponttal rendelkezik, valamint egy megőrzési időszakot, amely miatt a blobos törlés 14 napig nem kötelező.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="A Azure Portal időponthoz való visszaállításának konfigurálását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az időponthoz való visszaállítás PowerShell használatával történő konfigurálásához először telepítse az az [. Storage](https://www.powershellgallery.com/packages/Az.Storage) modul 2.6.0 vagy újabb verzióját. Ezután hívja meg az [enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) parancsot, hogy engedélyezze a tárolási fiók időponthoz való visszaállítását.

A következő példa engedélyezi a helyreállítható törlést, és beállítja a helyreállítható törlés megőrzési időtartamát, lehetővé teszi a hírcsatornák és verziószámozások módosítását, majd engedélyezi az időponthoz való visszaállítást. A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel történő időpontra történő visszaállításhoz először telepítse az Azure CLI-es vagy újabb verzióját. Ezután hívja meg az az [Storage Account blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) parancsot az időponthoz tartozó visszaállítás és a Storage-fiók egyéb szükséges adatvédelmi beállításainak engedélyezéséhez.

A következő példa lehetővé teszi a helyreállítható törlést, és beállítja a helyreállítható törlés megőrzési időtartamát 14 napig, lehetővé teszi a hírcsatornák és verziószámozások módosítását, valamint lehetővé teszi az időponthoz való visszaállítást 7 napos visszaállítási időszakra. A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Visszaállítási pont kiválasztása

A visszaállítási pont az a dátum és időpont, ameddig az adatok visszaállíthatók. Az Azure Storage mindig UTC dátum/idő értéket használ a visszaállítási pontként. A Azure Portal azonban lehetővé teszi a visszaállítási pont helyi idő szerinti megadását, majd ezt a dátum/idő értéket egy UTC dátum/idő értékre alakítja a visszaállítási művelet elvégzéséhez.

Ha a PowerShell vagy az Azure CLI használatával hajt végre visszaállítási műveletet, a visszaállítási pontot UTC dátum/idő értékként kell megadnia. Ha a visszaállítási pont az UTC-időérték helyett egy helyi időértékkel van megadva, akkor a visszaállítási művelet bizonyos esetekben a várt módon működhet. Ha például a helyi idő UTC mínusz öt óra, akkor a helyi idő értékének megadásával egy visszaállítási pont fog megjelenni, amely a megadott értéktől számított öt órával korábbi. Ha a tartományba tartozó, az öt órás időszak során visszaállított értékekre nem történt változás, akkor a visszaállítási művelet ugyanazt az eredményt fogja eredményezni, függetlenül attól, hogy melyik időértéket adta meg. A nem várt eredmények elkerülése érdekében ajánlott UTC-időt megadni a visszaállítási ponthoz.

## <a name="perform-a-restore-operation"></a>Visszaállítási művelet végrehajtása

A Storage-fiókban lévő összes tárolót visszaállíthatja, vagy egy vagy több tárolóban is visszaállíthatja a Blobok egy tartományát. A Blobok köre lexicographically van definiálva, vagyis a szótári sorrendben. Egy visszaállítási műveletben legfeljebb tíz lexicographical-tartomány támogatott. A tartomány kezdete magában foglalja a tartományt, a tartomány végét pedig kizárólagos.

A kezdési tartományhoz és a befejezési tartományhoz megadott tároló mintázatnak legalább három karaktert kell tartalmaznia. A tároló nevének a blob nevéből való elkülönítéséhez használt perjel (/) nem számít bele ebbe a minimumba.

Lexicographical-tartományokban nem használhatók helyettesítő karakterek. A helyettesítő karakterek szabványos karakterekként lesznek kezelve.

A és a tárolóban lévő Blobok visszaállításához `$root` `$web` explicit módon megadhatja őket egy visszaállítási műveletnek átadott tartományban. A `$root` és a `$web` tárolók csak akkor állíthatók vissza, ha explicit módon vannak megadva. Más rendszertárolók nem állíthatók vissza.

A rendszer csak a blokk blobokat állítja vissza. Az oldal blobok és a hozzáfűzési Blobok nem szerepelnek a visszaállítási műveletekben. További információ a hozzáfűző Blobokkal kapcsolatos korlátozásokról: az [időponthoz való visszaállítás a blokk Blobok](point-in-time-restore-overview.md)esetében.

> [!IMPORTANT]
> Ha visszaállítási műveletet hajt végre, az Azure Storage blokkolja a művelet időtartamára visszaállított tartományokban lévő Blobok adatműveleteit. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. Ezért az olyan műveletek, mint például a tárolók listázása a Azure Portalban előfordulhat, hogy nem a várt módon hajtják végre a visszaállítási műveletet.
>
> A másodlagos hely olvasási műveletei a visszaállítási művelet során folytatódnak, ha a Storage-fiók földrajzilag replikálódik.
>
> Az adatok egy halmazának visszaállításához szükséges idő a visszaállítási időszakban végrehajtott írási és törlési műveletek számától függ. Például egy 1 000 000 objektummal rendelkező, naponta hozzáadott 3 000-objektummal rendelkező fiók, valamint a naponta törölt 1 000-objektumok esetében körülbelül két óra elteltével vissza kell állítani egy 30 napos pontot a múltban. A megőrzési időtartam és a helyreállítás több mint 90 nappal a múltban nem ajánlott olyan fiók esetében, amely ezen a változási aránnyal rendelkezik.

### <a name="restore-all-containers-in-the-account"></a>A fiókban lévő összes tároló visszaállítása

A Storage-fiókban lévő összes tárolót visszaállíthatja úgy, hogy egy adott időpontban visszaadja azokat az előző állapotába.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha a Storage-fiókban lévő összes tárolót és blobot vissza szeretné állítani a Azure Portal, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz tartozó tárolók listájához.
1. Az eszköztáron válassza a **tárolók visszaállítása**, majd az **összes visszaállítása** lehetőséget.
1. A **minden tároló visszaállítása** panelen adja meg a visszaállítási pontot a dátum és idő megadásával.
1. A jelölőnégyzet bejelölésével erősítse meg, hogy folytatni kívánja a folytatást.
1. A visszaállítási művelet elindításához válassza a **visszaállítás** lehetőséget.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="A tárolók egy adott visszaállítási pontra történő visszaállítását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a Storage-fiókban lévő összes tárolót és blobot a PowerShell-lel szeretné visszaállítani, hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a visszaállítási pontot UTC dátum/idő értékként. Alapértelmezés szerint a **Restore-AzStorageBlobRange** parancs aszinkron módon fut, és egy **PSBlobRestoreStatus** típusú objektumot ad vissza, amely a visszaállítási művelet állapotának ellenőrzéséhez használható.

A következő példa aszinkron módon visszaállítja a Storage-fiókban lévő tárolókat az állapotukra 12 órával a jelen pillanat előtt, és ellenőrzi a visszaállítási művelet egyes tulajdonságait:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

A visszaállítási művelet szinkron módon történő futtatásához adja meg a **-WaitForComplete** paramétert a parancson. Ha a **-WaitForComplete** paraméter jelen van, a PowerShell megjelenít egy üzenetet, amely tartalmazza a művelet VISSZAÁLLÍTÁSi azonosítóját, majd a végrehajtást a visszaállítási művelet befejeződéséig blokkolja. Ne feledje, hogy a visszaállítási művelethez szükséges időtartam a visszaállítani kívánt adatok mennyiségétől függ, és egy nagy visszaállítási művelet akár egy órát is igénybe vehet.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha a Storage-fiókban lévő összes tárolót és blobot vissza szeretné állítani az Azure CLI-vel, hívja meg az az [Storage blob Restore](/cli/azure/storage/blob#az_storage_blob_restore) parancsot, és adja meg a visszaállítási pontot UTC dátum/idő értékként.

A következő példa aszinkron módon visszaállítja a Storage-fiókban lévő összes tárolót az állapotukra 12 órával a megadott dátum és idő előtt. A visszaállítási művelet állapotának megtekintéséhez hívja az az [Storage Account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Egy visszaállítási művelet tulajdonságainak megtekintéséhez hívja az az [Storage Account show](/cli/azure/storage/account#az_storage_account_show) parancsot, és bontsa ki a **blobRestoreStatus** tulajdonságot. Az alábbi példa bemutatja, hogyan ellenőrizhető az **állapot** tulajdonság.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Az az **Storage blob Restore** parancs szinkron módon történő futtatásához és a végrehajtás letiltásához, amíg a visszaállítási művelet be nem fejeződik, hagyja ki a `--no-wait` paramétert.

---

### <a name="restore-ranges-of-block-blobs"></a>Blokk-Blobok tartományának visszaállítása

A Blobok egy vagy több lexicographical egy tárolóban vagy több tárolóban is visszaállíthatja, hogy a blobokat egy adott időpontban visszaadja az előző állapotába.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha egy vagy több tárolóban lévő Blobok egy tartományát szeretné visszaállítani a Azure Portal, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz tartozó tárolók listájához.
1. Válassza ki a visszaállítani kívánt tárolót vagy tárolókat.
1. Az eszköztáron válassza a **tárolók visszaállítása**, majd a **kijelöltek visszaállítása** lehetőséget.
1. A **kijelölt tárolók visszaállítása** panelen adja meg a visszaállítási pontot a dátum és idő megadásával.
1. Adja meg a visszaállítani kívánt tartományokat. A perjelek (/) használatával megszabhatja a tároló nevét a blob előtagja alapján.
1. Alapértelmezés szerint a **kijelölt tárolók visszaállítása** ablaktábla egy olyan tartományt határoz meg, amely a tárolóban lévő összes blobot tartalmazza. Ha nem szeretné visszaállítani a teljes tárolót, törölje ezt a tartományt. Az alapértelmezett tartomány az alábbi képen látható.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Képernyőfelvétel a törlendő alapértelmezett blob-tartományról az egyéni tartomány megadását megelőzően":::

1. A jelölőnégyzet bejelölésével erősítse meg, hogy folytatni kívánja a folytatást.
1. A visszaállítási művelet elindításához válassza a **visszaállítás** lehetőséget.

Az alábbi képen egy tartományon futó visszaállítási művelet látható.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="A Blobok tartományának egy vagy több tárolóban való visszaállítását bemutató képernyőfelvétel":::

A rendszerképben látható visszaállítási művelet a következő műveleteket hajtja végre:

- Visszaállítja a *container1* teljes tartalmát.
- Visszaállítja a blobokat a lexicographical tartomány *blob1* a *container2* *blob5* keresztül. Ez a tartomány a blobokat olyan nevekkel állítja vissza, mint például a *blob1*, a *blob11*, a *blob100*, a *blob2* és így tovább. Mivel a tartomány vége kizárólagos, visszaállítja azokat a blobokat, amelyek nevei a *blob4*-vel kezdődnek, de nem állítja vissza azokat a blobokat, amelyek nevei a *blob5*-vel kezdődnek.
- Visszaállítja az összes blobot a *container3* és a *container4*. Mivel a tartomány vége kizárólagos, ez a tartomány nem állítja vissza a *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Blobok egyetlen tartományának visszaállításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a paraméterhez tartozó lexicographical és blob-nevek tartományát `-BlobRestoreRange` . Ha például egy *container1* nevű tárolóban szeretné visszaállítani a blobokat, megadhat egy tartományt, amely a *container1* kezdetű, és a *container2*-val végződik. A kezdő és a záró tartományokban megnevezett tárolók nem kötelezőek. Mivel a tartomány vége kizárólagos, még akkor is, ha a Storage-fiók tartalmaz egy *container2* nevű tárolót, csak a *container1* nevű tároló lesz visszaállítva:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Ha egy tárolóban lévő Blobok részhalmazát kívánja visszaállítani, egy perjel (/) használatával válassza el a tároló nevét a blob előtag-mintából. A következő tartomány például kijelöli a blobokat egyetlen tárolóban, amelynek neve a *d* – *f* betűvel kezdődik:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ezután adja meg a tartományt a **Restore-AzStorageBlobRange** parancs számára. Adja meg a visszaállítási pontot a paraméter UTC **datetime** értékének megadásával `-TimeToRestore` . Az alábbi példa a megadott tartományba tartozó blobokat a jelen pillanat előtti 3 nappal visszaállítja.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Alapértelmezés szerint a **Restore-AzStorageBlobRange** parancs aszinkron módon fut. Amikor aszinkron módon kezdeményezi a visszaállítási műveletet, a PowerShell azonnal megjeleníti a művelet tulajdonságainak táblázatát:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

A blokkos Blobok több tartományának visszaállításához adja meg a paraméter tartományának tömbjét `-BlobRestoreRange` . Az alábbi példa két tartományt határoz meg a *container1* és a *container4* teljes tartalmának visszaállításához 24 órája, és az eredményt egy változóba menti:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

A visszaállítási művelet szinkron módon történő futtatásához és a végrehajtás befejezéséhez a parancsban adja meg a **-WaitForComplete** paramétert.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blobok egy tartományának visszaállításához hívja meg az az [Storage blob Restore](/cli/azure/storage/blob#az_storage_blob_restore) parancsot, és adja meg a paraméterhez tartozó tároló-és blob-nevek lexicographical-tartományát `--blob-range` . Több tartomány megadásához adja meg a `--blob-range` paramétert az egyes különböző tartományokhoz.

Ha például egy *container1* nevű tárolóban szeretné visszaállítani a blobokat, megadhat egy tartományt, amely a *container1* kezdetű, és a *container2*-val végződik. A kezdő és a záró tartományokban megnevezett tárolók nem kötelezőek. Mivel a tartomány vége kizárólagos, még akkor is, ha a Storage-fiók tartalmaz egy *container2* nevű tárolót, a rendszer csak a *container1* nevű tárolót állítja vissza.

Ha egy tárolóban lévő Blobok részhalmazát kívánja visszaállítani, egy perjel (/) használatával válassza el a tároló nevét a blob előtag-mintából. Az alább látható példa aszinkron módon visszaállítja a Blobok egy tartományát egy olyan tárolóban, amelynek neve a betűvel kezdődik `d` `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Az az **Storage blob Restore** parancs szinkron módon történő futtatásához és a végrehajtás letiltásához, amíg a visszaállítási művelet be nem fejeződik, hagyja ki a `--no-wait` paramétert.

---

## <a name="next-steps"></a>Következő lépések

- [Időponthoz való visszaállítás a blokk Blobok esetében](point-in-time-restore-overview.md)
- [Helyreállítható törlés](./soft-delete-blob-overview.md)
- [Csatorna módosítása](storage-blob-change-feed.md)
- [BLOB verziószámozása](versioning-overview.md)