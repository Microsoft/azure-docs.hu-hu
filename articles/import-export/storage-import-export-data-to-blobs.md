---
title: Az Azure import/export használata az adatok Azure-Blobokra történő átviteléhez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat Azure Portal az Azure-Blobokra irányuló és onnan érkező adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 74f5565ba9dfa48dabfe56c25e3ef30a8caafe14
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563283"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Az Azure import/export szolgáltatás használata az Azure-ba való adatimportálásra Blob Storage

Ez a cikk részletesen ismerteti, hogyan használható az Azure import/export szolgáltatás a nagy mennyiségű, az Azure Blob Storage-ba való biztonságos importáláshoz. Az Azure-Blobokra történő adatimportáláshoz a szolgáltatáshoz az adatait tartalmazó titkosított lemezmeghajtókat kell szállítani egy Azure-adatközpontba.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot az adatok Azure Blob Storageba való átviteléhez, gondosan tekintse át és végezze el az alábbi, a szolgáltatásra vonatkozó előfeltételeket tartalmazó listát.
A következőket kell tennie:

* Az import/export szolgáltatáshoz használható aktív Azure-előfizetéssel rendelkezik.
* Rendelkeznie kell legalább egy Azure Storage-fiókkal tárolóval. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md).
  * További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](../storage/common/storage-account-create.md).
  * A Storage-tárolóval kapcsolatos információkért nyissa meg a Storage-tároló [létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)című témakört.
* Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül.
* Egy [támogatott operációsrendszer-verzióval](storage-import-export-requirements.md#supported-operating-systems)rendelkező Windows rendszerre van telepítve.
* Engedélyezze a BitLockert a Windows rendszeren. Lásd: [a BitLocker engedélyezése](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Töltse le a legújabb WAImportExport 1-es verzióját](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre. Az eszköz legújabb verziója olyan biztonsági frissítésekkel rendelkezik, amelyek lehetővé teszik a BitLocker-kulcs külső oltalmazójának és a frissített feloldási mód funkciójának használatát.

  * Bontsa ki az alapértelmezett mappát `waimportexportv1` . Például: `C:\WaImportExportV1`.
* Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen kapcsolatba Azure Data Box Operations csapatával a következő címen: `adbops@microsoft.com` .
  * A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
  * Nyomkövetési szám létrehozása az exportálási feladatokhoz.
  * Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
  * Ha nem rendelkezik Carrier-fiókkal, lépjen a következőre:
    * [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy
    * [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1. lépés: a meghajtók előkészítése

Ez a lépés egy naplófájlt hoz létre. A naplófájl olyan alapvető adatokat tárol, mint például a meghajtó sorozatszáma, a titkosítási kulcs és a Storage-fiók adatai.

A meghajtók előkészítéséhez végezze el a következő lépéseket.

1. Csatlakoztathatja a lemezmeghajtókat a Windows rendszerhez a SATA-összekötők használatával.
2. Hozzon létre egyetlen NTFS-kötetet az egyes meghajtókon. Rendeljen meghajtóbetűjelet a kötethez. Ne használja a csatolási.
3. Engedélyezze a BitLocker-titkosítást az NTFS-köteten. Ha Windows Server rendszert használ, használja a [Windows server 2012 R2 rendszeren a BitLocker engedélyezésének](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)útmutatását.
4. Az Adatmásolás titkosított kötetre. A drag and drop vagy a Robocopy vagy bármely ilyen másolási eszköz használatával. A Journal-(*. jrn*) fájl ugyanabban a mappában jön létre, ahol az eszközt futtatja.

   Ha a meghajtó zárolva van, és fel kell oldania a meghajtó zárolását, a zárolás lépései a használati esettől függően eltérőek lehetnek.

   * Ha egy előre titkosított meghajtóhoz (WAImportExport eszközt nem használt a titkosításhoz) adott hozzá adatkészletet, a meghajtó zárolásának feloldásához használja az előugró ablakban a BitLocker-kulcsot (a megadott numerikus jelszót).

   * Ha a WAImportExport eszköz által titkosított meghajtóhoz adott meg egy olyan meghajtót, amelyet a következő paranccsal lehet feloldani a meghajtó zárolásának feloldásához:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Nyisson meg egy PowerShell-vagy parancssori ablakot rendszergazdai jogosultságokkal. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:

    `cd C:\WaImportExportV1`
6. A meghajtó BitLocker-kulcsának beszerzéséhez futtassa a következő parancsot:

    `manage-bde -protectors -get <DriveLetter>:`
7. A lemez előkészítéséhez futtassa a következő parancsot. **Az adatmérettől függően a lemez előkészítése több óráig is eltarthat.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    A rendszer ugyanabban a mappában hozza létre a naplófájlt, ahol az eszközt futtatta. Két másik fájl is létrejön – egy *. XML* fájl (az eszköz futtatására szolgáló mappa) és egy *drive-manifest.xml* fájl (a mappa, ahol az adat található).

    A használt paramétereket a következő táblázat ismerteti:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j     |A naplófájl neve, a. jrn kiterjesztéssel. Egy naplófájl jön létre egy meghajtón. Javasoljuk, hogy a lemez sorozatszámát használja a naplófájl neveként.         |
    |/ID     |A munkamenet-azonosító. A parancs minden példányához használjon egyedi munkamenet-számot.      |
    |/t:     |A szállítandó lemez meghajtóbetűjele. Például: meghajtó `D` .         |
    |BK     |A meghajtó BitLocker-kulcsa. A numerikus jelszava a kimenetből `manage-bde -protectors -get D:`      |
    |/srcdir:     |A szállítandó lemez meghajtóbetűjele, majd a `:\` . Például: `D:\`.         |
    |/dstdir:     |A cél tároló neve az Azure Storage-ban.         |
    |/blobtype:     |Ezzel a beállítással adható meg, hogy milyen típusú blobokat kíván importálni az alkalmazásba. A Block Blobok esetében a blob típusa `BlockBlob` és az oldal Blobok `PageBlob` .         |
    |/skipwrite:     | Azt határozza meg, hogy a lemezre való másoláshoz és a lemezen lévő meglévő adatsorokhoz nincs szükség új adatfeldolgozásra.          |
    |/enablecontentmd5:     |Ha a beállítás engedélyezve van, biztosítja, hogy az MD5 kiszámítva legyen, és az `Content-md5` egyes Blobok tulajdonságként legyen beállítva. Ezt a lehetőséget csak akkor használja, ha a `Content-md5` mezőt az Azure-ba való feltöltés után szeretné használni. <br> Ez a beállítás nincs hatással az adatintegritás-ellenőrzésre (alapértelmezés szerint ez történik). A beállítással megnövelhető az adatok felhőbe való feltöltéséhez szükséges idő.          |
8. Ismételje meg az előző lépést minden egyes leszállításra szoruló lemez esetében. A rendszer a megadott névvel rendelkező naplófájlt hoz létre a parancssor minden futtatásához.

    > [!IMPORTANT]
    > * A Journal-fájllal együtt egy `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájl is jön létre ugyanabban a mappában, ahol az eszköz található. Ha a naplófájl túl nagy, a rendszer a naplófájl helyett a. xml fájlt használja.
   > * A portál által engedélyezett naplófájl maximális mérete 2 MB. Ha a naplófájl túllépi ezt a korlátot, a rendszer hibaüzenetet küld.

## <a name="step-2-create-an-import-job"></a>2. lépés: importálási feladatok létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot a Azure Portal.

1. Jelentkezzen be a következőre: https://portal.azure.com/ .
2. **Importálási/exportálási feladatok** keresése.

   ![Importálási/exportálási feladatok keresése](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Válassza a **+ Új** lehetőséget.

   ![Új létrehozásához válassza az új lehetőséget. ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Az **alapjaiban**:

   1. Válasszon egy előfizetést.
   1. Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és hozzon létre egy újat.
   1. Adjon meg egy leíró nevet az importálási feladatokhoz. A név használatával követheti nyomon a feladatok állapotát.
      * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
      * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.

   1. Válassza **az Importálás az Azure-ba** lehetőséget.

    ![Importálási feladatok létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Válassza a **Next (tovább): feladatok részletei >** a folytatáshoz.

5. A **feladatok részletei**:

   1. Töltse fel az előző 1. lépésben létrehozott naplófájlokat [: Készítse elő a meghajtókat](#step-1-prepare-the-drives). Ha `waimportexport.exe version1` használta, töltsön fel egy fájlt minden előkészített meghajtóra. Ha a naplófájl mérete meghaladja a 2 MB-ot, akkor használhatja a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájlt is a Journal-fájllal.
   1. Válassza ki a cél Azure-régiót a rendeléshez.
   1. Válassza ki az importáláshoz használandó Storage-fiókot.
      
      A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.
   1. Ha nem szeretné menteni a részletes naplót, törölje a **részletes napló mentése a "waimportexport" blob-tárolóban** lehetőséget.

   ![Importálási feladatok létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Válassza a **Tovább: szállítás >** a folytatáshoz.

6. A **szállításban**:

   1. Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával a `adbops@microsoft.com`  használni kívánt szolgáltatóra vonatkozó információkkal.
   1. Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az importálási feladatok befejezését követően. Ha nem rendelkezik fiókkal, hozzon létre egy [FedEx](https://www.fedex.com/us/oadr/) vagy [DHL](https://www.dhl.com/) Carrier-fiókot.
   1.  Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót.

       > [!TIP]
       > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

   ![Importálási feladatok létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

7. A sorrend összegzése:

   1. Tekintse át a **feltételeket**, majd válassza az "Elfogadom, hogy a megadott információk helyesek, és fogadja el a feltételeket és a kikötéseket" lehetőséget. Az érvényesítés Ekkor megtörténik.
   1. Tekintse át az összegzésben megadott feladattal kapcsolatos információkat. Jegyezze fel a feladatok nevét és az Azure-adatközpontok szállítási címeit, hogy a lemezeket vissza lehessen szállítani az Azure-ba. Ezeket az információkat később a szállítási címkén lehet használni.
   1. Válassza a **Létrehozás** lehetőséget.

     ![Importálási feladatok létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépések végrehajtásával hozhat létre importálási feladatot az Azure CLI-ben.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Feladat létrehozása

1. Az az [import-export](/cli/azure/ext/import-export/import-export) bővítmény hozzáadásához használja az az [Extension Add](/cli/azure/extension#az_extension_add) parancsot:

    ```azurecli
    az extension add --name import-export
    ```

1. Használhat meglévő erőforráscsoportot, vagy létrehozhat egyet. Erőforráscsoport létrehozásához futtassa az [az group create](/cli/azure/group#az_group_create) parancsot:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Használhat meglévő Storage-fiókot, vagy létrehozhat egyet. A Storage-fiók létrehozásához futtassa az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) parancsot:

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. A lemezek szállítására szolgáló helyek listájának lekéréséhez használja az az [import-export Location List](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) parancsot:

    ```azurecli
    az import-export location list
    ```

1. A régió helyeinek lekéréséhez használja az az [import-export Location show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) parancsot:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Importálási feladatok létrehozásához futtassa a következőt az [import-export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) paranccsal:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

1. A myierg erőforráscsoport összes feladatának megtekintéséhez használja az az [import-export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) parancsot:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. A feladat frissítéséhez vagy a feladat megszakításához futtassa az az [import-export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) parancsot:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Az alábbi lépések végrehajtásával hozhat létre importálási feladatot a Azure PowerShellban.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Míg az az **. ImportExport** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Feladat létrehozása

1. Használhat meglévő erőforráscsoportot, vagy létrehozhat egyet. Erőforráscsoport létrehozásához futtassa a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Használhat meglévő Storage-fiókot, vagy létrehozhat egyet. A Storage-fiók létrehozásához futtassa a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmagot:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. A lemezek szállítására szolgáló helyszínek listájának lekéréséhez használja a [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) parancsmagot:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Használja a `Get-AzImportExportLocation` parancsmagot a `Name` paraméterrel a régió helyeinek lekéréséhez:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Az importálási feladatok létrehozásához futtassa a következő [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) példát:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

1. A [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) parancsmag használatával tekintse meg a myierg erőforráscsoport összes feladatát:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. A feladat frissítéséhez vagy a feladat megszakításához futtassa az [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) parancsmagot:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>3. lépés (nem kötelező): az ügyfél által felügyelt kulcs konfigurálása

Hagyja ki ezt a lépést, és folytassa a következő lépéssel, ha a Microsoft által felügyelt kulccsal szeretné védetté tenni a meghajtók BitLocker-kulcsait. Ha saját kulcsot szeretne beállítani a BitLocker-kulcs védeleméhez, kövesse az [ügyfél által felügyelt kulcsok konfigurálása a Azure Key Vault az Azure-beli Importálás/Exportálás az Azure Portalban](storage-import-export-encryption-key-portal.md)című témakör utasításait.

## <a name="step-4-ship-the-drives"></a>4. lépés: a meghajtók szállítása

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5. lépés: a feladatok frissítése a nyomkövetési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6. lépés: az adatok feltöltésének ellenőrzése az Azure-ban

A feladatot nyomon követheti befejezésre. A feladatok befejezése után ellenőrizze, hogy az adatok fel lettek-e töltve az Azure-ba. Csak a feltöltés sikeres ellenőrzése után törölje a helyszíni adatok törlését.

## <a name="next-steps"></a>Következő lépések

* [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
