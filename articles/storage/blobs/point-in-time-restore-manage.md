---
title: Időponthoz időben való visszaállítás végrehajtása blokkblobadatokon
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthat vissza blokkblobokat az előző állapotukba egy adott időpontban az időponthoz adott időpontban.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869346"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Időponthoz időben való visszaállítás végrehajtása blokkblobadatokon

Az időponthoz időben való visszaállítás használatával visszaállíthat egy vagy több blokkblob-készletet egy korábbi állapotba. Ez a cikk azt ismerteti, hogyan engedélyezheti az időponthoz időben való visszaállítást egy tárfiókon, és hogyan hajthatja végre a visszaállítási műveletet.

További információ az időponthoz időben való visszaállításról: Időponthoz időben való visszaállítás [blokkblobok számára.](point-in-time-restore-overview.md)

> [!CAUTION]
> Az időponthoz időben való visszaállítás csak blokkblobok esetén támogatja a műveletek visszaállítását. A tárolókon található műveletek nem állíthatók vissza. Ha töröl egy tárolót a tárfiókból a Tároló törlése művelet hívásával, a tároló visszaállítási művelettel nem állítható vissza. [](/rest/api/storageservices/delete-container) Egy teljes tároló törlése helyett törölje az egyes blobokat, ha később vissza szeretné őket állítani. Emellett a Microsoft javasolja a tárolók és blobok soft delete funkcióját a véletlen törlés elleni védelem érdekében. További információ: [Tárolók soft delete for containers (preview) (Tárolók soft delete for containers (előzetes verzió)](soft-delete-container-overview.md) és Soft delete for blobs (Blobok soft delete) (Tárolók soft delete for containers (előzetes verzió) és [Soft delete for blobs (Blobok soft delete) (](soft-delete-blob-overview.md)

## <a name="enable-and-configure-point-in-time-restore"></a>Időponthoz való visszaállítás engedélyezése és konfigurálása

Az időponthoz időben való visszaállítás engedélyezése és konfigurálása előtt engedélyezze annak előfeltételeit a tárfiókhoz: helyreállítható törlés, változáscsatorna és blob verziószámozása. Az egyes funkciók engedélyezésével kapcsolatos további információkért tekintse meg az alábbi cikkeket:

- [Blobok helyreállítható törlésének engedélyezése](./soft-delete-blob-enable.md)
- [A változáscsatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blobok verziószámozásának engedélyezése és kezelése](versioning-enable.md)

> [!IMPORTANT]
> A soft delete, a change feed és a blob verziószámozásának engedélyezése további költségeket eredményezhet. További információkért lásd: Blobok soft delete (Blobok soft [delete)](soft-delete-blob-overview.md)(Blobok soft delete –, [change feed support in Azure Blob Storage](storage-blob-change-feed.md)) és [Blob versioning (Blobok verziószámozása).](versioning-overview.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az időponthoz időben való visszaállítás konfigurálni a Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A Beállítások **alatt** válassza az **Adatvédelem lehetőséget.**
1. Válassza **az Időponthoz időben való visszaállítás bekapcsolás** lehetőséget. Ha ezt a lehetőséget választja, a blobok, a verziószámozás és a változáscsatorna esetén is engedélyezve lesz a soft delete (blobok, verziószámozás és változáscsatorna) törlése.
1. Állítsa be az időponthoz való visszaállítás maximális helyreállítási pontját napokban. Ennek a számnak legalább egy naptal rövidebbnek kell lennie, mint a blobok soft delete (blobok soft delete) megőrzési időszaka.
1. Mentse a módosításokat.

Az alábbi képen egy hét nappal ezelőtti visszaállítási ponttal konfigurált tárfiók látható, a blobok helyreállítható törlésének 14 napos megőrzési ideje.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Képernyőkép az időponthoz időben való visszaállítás konfigurálásról a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az időponthoz időben való visszaállítás PowerShell-sel való konfigurálására először telepítse az [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) modul 2.6.0-s vagy újabb verzióját. Ezután hívja meg az [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) parancsot a tárfiók időponthoz időben való visszaállításának engedélyezéséhez.

Az alábbi példa engedélyezi a helyreállítható törlést, beállítja a helyreállítható törlés megőrzési idejét, engedélyezi a változáscsatorna és a verziószámozás, majd az időponthoz való visszaállítást. A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

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

Az időponthoz időben való visszaállítás Azure CLI-val való konfiguráláshoz először telepítse az Azure CLI 2.2.0-s vagy újabb verzióját. Ezután hívja meg [az az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) parancsot az időponthoz való visszaállítás és a tárfiók egyéb szükséges adatvédelmi beállításainak engedélyezéséhez.

Az alábbi példa engedélyezi a helyreállítható törlést, 14 napra állítja a helyreállítható törlés megőrzési idejét, engedélyezi a változáscsatornát és a verziószámozást, valamint 7 napos visszaállítást tesz lehetővé az időpontnak megfelelő visszaállítással. A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

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

A visszaállítási pont az adatok visszaállításának dátuma és időpontja. Az Azure Storage mindig UTC dátum/idő értéket használ visszaállítási pontként. A visszaállítási Azure Portal azonban lehetővé teszi a visszaállítási pont helyi idő szerinti megadását, majd átalakítja ezt a dátum-/időértéket UTC dátum-/időértékre a visszaállítási művelet végrehajtásához.

Amikor visszaállítási műveletet hajt végre a PowerShell vagy az Azure CLI használatával, a visszaállítási pontot UTC dátum/idő értékként kell megadnia. Ha a visszaállítási pont UTC időérték helyett helyi időértékkel van megadva, a visszaállítási művelet bizonyos esetekben továbbra is a várt módon viselkedhet. Ha például a helyi idő UTC mínusz öt óra, akkor a helyi idő értékének megadása olyan visszaállítási pontot fog visszaadni, amely öt órával korábban van a megadott értéknél. Ha az ötórás időszak alatt visszaállítható tartományban nem történt adatváltozás, akkor a visszaállítási művelet a megadott időtől függetlenül ugyanazt az eredményt fogja előállítani. A váratlan eredmények elkerülése érdekében ajánlott megadni a visszaállítási pont UTC-idejét.

## <a name="perform-a-restore-operation"></a>Visszaállítási művelet végrehajtása

Visszaállíthatja a tárfiók összes tárolóját, vagy egy vagy több tároló blobtartományát is. A blobok tartománya lexikálisan van definiálva, azaz szótári sorrendben. Visszaállítási műveletenként legfeljebb tíz lexikális tartomány támogatott. A tartomány kezdete befogadó, a tartomány vége pedig kizárólagos.

A kezdő- és zárótartományhoz megadott tárolóminta legalább három karaktert tartalmazhat. A tároló nevének blobnévtől való elválasztáshoz használt perjel (/) nem számít bele ebbe a minimumba.

A helyettesítő karakterek lexikális tartományokban nem támogatottak. A helyettesítő karakterek normál karakterekként vannak kezelve.

A és a tárolóban lévő blobok visszaállíthatóak, ha explicit módon megadja őket egy visszaállítási műveletnek `$root` `$web` átadott tartományban. A és a tárolók visszaállítása csak akkor `$root` történik `$web` meg, ha explicit módon meg vannak adva. Más rendszertárolók nem állíthatók vissza.

Csak a blokkblobok állíthatók vissza. A lapblobok és a hozzáfűző blobok nem szerepelnek a visszaállítási műveletekben. A hozzáfűző blobokkal kapcsolatos korlátozásokkal kapcsolatos további információkért lásd: Időponthoz időben való visszaállítás [blokkblobok számára.](point-in-time-restore-overview.md)

> [!IMPORTANT]
> Visszaállítási művelet végrehajtásakor az Azure Storage a művelet időtartama alatt letiltja az adatműveleteket a visszaállított tartományokban lévő blobokban. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. Emiatt előfordulhat, hogy a visszaállítási művelet végrehajtása közben az olyan műveletek, mint a tárolók Azure Portal a várt módon működnek.
>
> A másodlagos helyről végzett olvasási műveletek a visszaállítási művelet során folytathatók, ha a tárfiók georeplikált.
>
> Az adathalmazok visszaállításához szükséges idő a visszaállítási időszak alatt az írási és törlési műveletek számán alapul. Egy olyan fiók például, amely egymillió objektumot tartalmaz, és naponta 3000 objektumot ad hozzá, és naponta 1000 törölt objektumot tartalmaz, körülbelül két órára lesz szükség ahhoz, hogy a rendszer visszaállítsa a korábbi 30 napot. Ilyen változási arányú fiókok esetén nem ajánlott a 90 napnál hosszabb megőrzési idő és visszaállítás.

### <a name="restore-all-containers-in-the-account"></a>A fiókban található összes tároló visszaállítása

A tárfiókban található összes tárolót visszaállíthatja az előző állapotukba egy adott időpontban.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A tárfiókban lévő összes tároló és blob helyreállításához kövesse Azure Portal következő lépéseket:

1. Lépjen a tárfiók tárolóinak listájára.
1. Az eszköztáron válassza a **Tárolók visszaállítása,** majd **az Összes visszaállítása lehetőséget.**
1. A Minden **tároló visszaállítása panelen** adja meg a visszaállítási pontot egy dátum és idő megadásával.
1. Jelölje be a jelölőnégyzetet, és erősítse meg, hogy folytatni szeretné a műveletet.
1. A **visszaállítási** művelet megkezdéséhez válassza a Visszaállítás lehetőséget.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Képernyőkép az összes tároló adott visszaállítási pontra való visszaállításáról":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A tárfiókban lévő összes tároló és blob PowerShell-rel való visszaállításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a visszaállítási pontot UTC dátum/idő értékként. Alapértelmezés szerint a **Restore-AzStorageBlobRange** parancs aszinkron módon fut, és **PSBlobRestoreStatus** típusú objektumot ad vissza, amelyet a visszaállítási művelet állapotának ellenőrzéshez használhat.

Az alábbi példa aszinkron módon visszaállítja a tárfiókban található tárolókat a jelenlegi pillanat előtt 12 órával az állapotukba, és ellenőrzi a visszaállítási művelet néhány tulajdonságát:

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

A visszaállítási művelet szinkron futtatásához foglalja bele a **-WaitForComplete** paramétert a parancsba. Ha a **-WaitForComplete** paraméter megjelenik, a PowerShell megjelenít egy üzenetet, amely tartalmazza a művelet visszaállítási azonosítóját, majd letiltja a végrehajtást, amíg a visszaállítási művelet be nem fejeződik. Ne feledje, hogy a visszaállítási művelethez szükséges időtartam a visszaállítani kívánt adatok mennyiségétől függ, és egy nagy visszaállítási művelet akár egy órát is igénybe vehet.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiókban lévő összes tároló és blob Azure CLI-val való visszaállításához hívja meg az [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) parancsot, és adja meg a visszaállítási pontot UTC dátum/idő értékként.

Az alábbi példa aszinkron módon visszaállítja a tárfiók összes tárolóját a saját állapotába 12 órával a megadott dátum és idő előtt. A visszaállítási művelet állapotának ellenőrzéshez hívja meg [az az storage account show hívást:](/cli/azure/storage/account#az_storage_account_show)

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

A visszaállítási művelet tulajdonságainak ellenőrzéshez hívja meg [az az storage account show](/cli/azure/storage/account#az_storage_account_show) parancsot, és bontsa ki a **blobRestoreStatus tulajdonságot.** Az alábbi példa bemutatja, hogyan ellenőrizheti az **állapot tulajdonságot.**

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Ha az **az storage blob restore** parancsot szinkron módon kell futtatni, és a végrehajtást a visszaállítási művelet befejezéséig le kell tiltani, akkor ne futtassa a `--no-wait` paramétert.

---

### <a name="restore-ranges-of-block-blobs"></a>Blokkblobok tartományának visszaállítása

A blobok egy vagy több lexikális tartományát visszaállíthatja egy vagy több tárolón belül, hogy egy adott időpontban visszaállítsa ezeket a blobokat az előző állapotukba.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha egy vagy több tárolóban található blobtartományt vissza Azure Portal, kövesse az alábbi lépéseket:

1. Lépjen a tárfiók tárolóinak listájára.
1. Válassza ki a visszaállítani kívánt tárolót vagy tárolókat.
1. Az eszköztáron válassza a **Tárolók visszaállítása,** majd **a Kijelölt Visszaállítás lehetőséget.**
1. A Kiválasztott **tárolók visszaállítása panelen** adja meg a visszaállítási pontot egy dátum és idő megadásával.
1. Adja meg a visszaállítani kívánt tartományokat. Használjon perjelet (/) a tároló nevének a blobelőtagból való delineate-éhez.
1. Alapértelmezés szerint a **Kiválasztott tárolók visszaállítása** panel egy olyan tartományt ad meg, amely a tároló összes blobját tartalmazza. Törölje ezt a tartományt, ha nem szeretné visszaállítani a teljes tárolót. Az alapértelmezett tartomány az alábbi képen látható.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Képernyőkép az alapértelmezett törölni kívánt blobtartományról az egyéni tartomány megadása előtt":::

1. Jelölje be a jelölőnégyzetet, és erősítse meg, hogy folytatni szeretné a műveletet.
1. A **visszaállítási** művelet megkezdéséhez válassza a Visszaállítás lehetőséget.

Az alábbi képen egy visszaállítási művelet látható tartománykészleten.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Képernyőkép egy vagy több tárolóban található blobtartományok visszaállításáról":::

A képen látható visszaállítási művelet a következő műveleteket végzi el:

- Visszaállítja a container1 teljes *tartalmát.*
- Visszaállítja a blobok lexikográfiai tartományában lévő *blobokat a blob1-től* *a blob5-ig* a *2. tárolóban.* Ez a tartomány olyan nevekkel visszaállítja a blobokat, mint a *blob1, a* *blob11,* a *blob100,* a *blob2* stb. Mivel a tartomány vége kizárólagos, visszaállítja az olyan blobokat, amelyeknek a neve *a következővel* kezdődik: blob4 , de nem a *blob5* névvel kezdődik.
- Visszaállítja a container3 és *container4* összes *blobját.* Mivel a tartomány vége kizárólagos, ez a tartomány nem visszaállítja a *container5 tárolót.*

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Egyetlen blobtartomány visszaállításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adjon meg egy lexikografikus tároló- és blobnévtartományt a `-BlobRestoreRange` paraméterhez. Ha például egy *container1* nevű tárolóban található blobokat kell visszaállítania, megadhat egy olyan tartományt, amely a *container1* és a container2 névvel *kezdődik.* A kezdő és záró tartományokban megnevezett tárolóknak nem kell léteznie. Mivel a tartomány vége kizárólagos, még akkor is, ha a tárfiók tartalmaz egy *container2* nevű tárolót, csak a *container1* nevű tároló lesz visszaállítva:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

A helyreállítani kívánt tárolóban lévő blobok egy részkészletének megadásához perjel (/) használatával válassza el a tároló nevét a blobelőtag-mintával. A következő tartomány például egyetlen tárolóban lévő blobokat választ ki, amelyeknek a neve a *d–f* betűkkel *kezdődik:*

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ezután adja meg a **Restore-AzStorageBlobRange parancs tartományát.** Adja meg a visszaállítási pontot a paraméter UTC **Dátum/idő** értékének `-TimeToRestore` megadásával. Az alábbi példa a megadott tartományban lévő blobokat a jelenlegi pillanat előtt 3 nappal visszaállítja az állapotukba:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Alapértelmezés szerint a **Restore-AzStorageBlobRange** parancs aszinkron módon fut. Amikor aszinkron módon kezdeményez visszaállítási műveletet, a PowerShell azonnal megjeleníti a művelet tulajdonságainak táblázatát:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

A blokkblobok több tartományának visszaállításához adjon meg egy tartománytömböt a `-BlobRestoreRange` paraméterhez. Az alábbi példa két tartományt ad meg a *container1* és a *container4* teljes tartalmának a 24 órával ezelőtti állapotukba való visszaállításához, és az eredményt egy változóba menti:

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

A visszaállítási művelet szinkron futtatásához és végrehajtás közbeni blokkoláshoz a parancsban foglalja bele a **-WaitForComplete** paramétert.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blobtartomány visszaállításához hívja meg az [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) parancsot, és adjon meg egy lexikografikus tároló- és blobnév-tartományt a `--blob-range` paraméterhez. Több tartomány megadásához adja meg az egyes `--blob-range` tartományokhoz a paramétert.

Ha például egy *container1* nevű tárolóban lévő blobokat kell visszaállítania, megadhat egy olyan tartományt, amely a *container1* és a container2 névvel *végződik.* A kezdő és záró tartományokban megnevezett tárolóknak nem kell léteznie. Mivel a tartomány vége kizárólagos, még akkor is, ha a tárfiók tartalmaz egy *container2* nevű tárolót, csak a *container1* nevű tároló lesz visszaállítva.

A helyreállítani kívánt tárolóban lévő blobok részkészletének megadásához használjon perjelet (/) a tároló nevének és a blobelőtag-mintának a külön megadásához. Az alábbi példa aszinkron módon visszaállítja egy tároló blobtartományát, amelynek a neve a betűkkel kezdődik a `d` `f` használatával.

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Az **az storage blob restore** parancs szinkron futtatásához és a végrehajtás blokkoláshoz a visszaállítási művelet befejezéséig kihagyhatja a `--no-wait` paramétert.

---

## <a name="next-steps"></a>Következő lépések

- [Időponthoz időben való visszaállítás blokkblobok számára](point-in-time-restore-overview.md)
- [Soft delete (Nem végleges törlés)](./soft-delete-blob-overview.md)
- [Változáscsatorna](storage-blob-change-feed.md)
- [Blob verziószámozása](versioning-overview.md)