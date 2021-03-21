---
title: Adatok átvitele az Azure import/export használatával Azure Filesba | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási feladatokat a Azure Portal az adatok Azure Filesba történő átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: b62c3c4be4fdffd9f509b86d248cd028518ae89a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181941"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Az Azure Import/Export szolgáltatás használata az adatok Azure Filesba történő importálásához

Ez a cikk részletesen ismerteti, hogyan használható az Azure import/export szolgáltatás a nagy mennyiségű információ biztonságos importálásához a Azure Filesba. Az adatimportáláshoz a szolgáltatáshoz olyan támogatott lemezmeghajtók szállítására van szükség, amelyek az Ön adatait tartalmazzák az Azure-adatközpontokban.

Az import/export szolgáltatás csak Azure Files importálását támogatja az Azure Storage-ba. A Azure Files exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot az adatok Azure Filesba való átviteléhez, gondosan tekintse át és hajtsa végre az alábbi előfeltételek listáját. A következőket kell tennie:

- Az import/export szolgáltatással való használatra aktív Azure-előfizetéssel kell rendelkeznie.
- Rendelkeznie kell legalább egy Azure Storage-fiókkal. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md). További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](../storage/common/storage-account-create.md).
- Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül.
- Egy [támogatott operációsrendszer-verzióval](storage-import-export-requirements.md#supported-operating-systems)rendelkező Windows rendszerre van telepítve.
- [Töltse le a WAImportExport 2-es verzióját](https://aka.ms/waiev2) a Windows rendszerre. Bontsa ki az alapértelmezett mappát `waimportexport` . Például: `C:\WaImportExport`.
- Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen kapcsolatba Azure Data Box Operations csapatával a következő címen: `adbops@microsoft.com` .
    - A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
    - Nyomkövetési szám létrehozása az exportálási feladatokhoz.
    - Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
    - Ha nem rendelkezik hordozófrekvencia-fiókkal, ugorjon a következőre:
        - [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy
        - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>1. lépés: a meghajtók előkészítése

Ez a lépés egy naplófájlt hoz létre. A naplófájl olyan alapvető adatokat tárol, mint például a meghajtó sorozatszáma, a titkosítási kulcs és a Storage-fiók adatai.

A meghajtók előkészítéséhez végezze el a következő lépéseket.

1. A lemezmeghajtók összekapcsolása a Windows rendszerű SATA-összekötők használatával.
2. Hozzon létre egyetlen NTFS-kötetet az egyes meghajtókon. Rendeljen meghajtóbetűjelet a kötethez. Ne használja a csatolási.
3. Módosítsa a *dataset.csv* fájlt abban a gyökérkönyvtárban, ahol az eszköz található. Attól függően, hogy egy fájlt vagy mappát vagy mindkettőt szeretne importálni, vegyen fel bejegyzéseket az alábbi példához hasonló *dataset.csv* fájlban.

   - **Fájl importálása**: az alábbi példában a másolandó adatlemez az F: meghajtón található. A fájl *MyFile1.txt*  a rendszer a *MyAzureFileshare1* gyökerébe másolja. Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiókban jön létre. A mappa szerkezete megmarad.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Mappa importálása**: a *MyFolder2* alatt lévő összes fájl és mappa rekurzív módon másolódik a fájlmegosztás. A mappa szerkezete megmarad.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Az importált mappákhoz vagy fájlokhoz tartozó fájlokban több bejegyzés is elvégezhető.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     További információ [az ADATKÉSZLET CSV-fájljának előkészítéséről](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Módosítsa a *driveset.csv* fájlt abban a gyökérkönyvtárban, ahol az eszköz található. Adja hozzá az alábbi példákhoz hasonló bejegyzéseket a *driveset.csv* fájlban. A driveset-fájl tartalmazza a lemezek listáját és a hozzá tartozó meghajtóbetűjeleket, így az eszköz megfelelően kiválaszthatja az előkészített lemezek listáját.

    Ez a példa azt feltételezi, hogy két lemez van csatlakoztatva, és alapszintű NTFS-kötetek G:\ és H:\ létrehozva. A H:\is nem titkosított, amíg a G: már titkosítva van. Az eszköz formázza és titkosítja a H:\ tároló lemezt. csak (és nem G: \) ).

   - **Nem titkosított lemez esetén**: a *titkosítás* megadásával engedélyezheti a BitLocker titkosítást a lemezen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Egy már titkosított lemez esetén**: adja meg a *AlreadyEncrypted* , és adja meg a BitLocker-kulcsot.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Több bejegyzés is elvégezhető a több meghajtóhoz tartozó fájlokban. További információ [a DRIVESET CSV-fájl előkészítéséről](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Az `PrepImport` adatlemezre másolással és előkészítéssel megadhatja az adatátvitelt. Ahhoz, hogy az első másolási munkamenet a címtárakat és/vagy fájlokat egy új másolási munkamenettel másolja, futtassa a következő parancsot:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Alább látható egy importálási példa.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. A (z) paraméterrel megadott nevű naplófájl jön `/j:` létre a parancssor minden futtatásához. Minden előkészíteni kívánt meghajtóhoz tartozik egy naplófájl, amelyet az importálási feladatok létrehozásakor fel kell tölteni. A naplófájlok nélküli meghajtókat nem dolgozza fel a rendszer.

    > [!IMPORTANT]
    > - A lemez-előkészítés befejezése után ne módosítsa a lemezmeghajtón vagy a naplófájlban lévőket.

További mintákért lépjen a [minták a Journal-fájlokhoz](#samples-for-journal-files)elemre.

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

   1. Töltse fel az előző 1. lépésben létrehozott naplófájlokat [: Készítse elő a meghajtókat](#step-1-prepare-the-drives).
   1. Válassza ki a cél Azure-régiót a rendeléshez.
   1. Válassza ki az importáláshoz használandó Storage-fiókot.

      A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.

   1. Ha nem szeretné menteni a részletes naplót, törölje a **részletes napló mentése a "waimportexport" blob-tárolóban** lehetőséget.


   ![Importálási feladatok létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Válassza a **Tovább: szállítás >** a folytatáshoz.

4. A **szállításban**:

    1. Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával `adbops@microsoft.com`  , és tekintse meg a használni kívánt szolgáltatóval kapcsolatos információkat.
    1. Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az importálási feladatok befejezését követően.
    1. Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót.

        > [!TIP]
        > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

    ![Importálási feladatok létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

5. A sorrend összegzése:

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
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
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

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: a meghajtók szállítása az Azure-adatközpontba

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: a feladatok frissítése a követési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: az adatok feltöltésének ellenőrzése az Azure-ban

A feladatot nyomon követheti befejezésre. A feladatok befejezése után ellenőrizze, hogy az adatok fel lettek-e töltve az Azure-ba. Csak a feltöltés sikeres ellenőrzése után törölje a helyszíni adatok törlését.

## <a name="samples-for-journal-files"></a>Naplófájlok mintái

**További meghajtók hozzáadásához** hozzon létre egy új driveset-fájlt, és futtassa az alábbi parancsot az alábbiak szerint.

Ha a későbbi másolási munkameneteket nem a *InitialDriveset. csv* fájlban megadott lemezmeghajtóra szeretné másolni, adjon meg egy új driveset *. csv* fájlt, és adja meg a paraméter értékét `AdditionalDriveSet` . Használja **ugyanazt a naplófájl** -nevet, és adjon meg egy **új munkamenet-azonosítót**. A AdditionalDriveset CSV-fájl formátuma megegyezik a InitialDriveSet formátumával.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Alább látható egy importálási példa.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Ha további adatfájlokat szeretne hozzáadni ugyanahhoz a driveset, használja a PrepImport parancsot a további másolási munkamenetek számára további fájlok/könyvtár másolásához.

Ha a későbbi másolási munkameneteket a *InitialDriveset.csv* fájlban megadott merevlemez-meghajtókra szeretné másolni, adja meg **ugyanazt a naplónevet** , és adjon meg egy **új munkamenet-azonosítót**. nincs szükség a Storage-fiók kulcsainak megadására.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Alább látható egy importálási példa.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Következő lépések

* [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)