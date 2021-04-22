---
title: Adatok átvitele az Azure Import/Export használatával Azure Files | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási feladatokat a Azure Portal adatok átviteléhez a Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 9c13ffc597349cdd2b304889d142ca7c2f89c713
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861534"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Az Azure Import/Export szolgáltatás használata az adatok Azure Filesba történő importálásához

Ez a cikk lépésről lépésre bemutatja, hogyan importálhat biztonságosan nagy mennyiségű adatot a Azure Files. Az adatok importálásához a szolgáltatás megköveteli, hogy az adatokat tartalmazó támogatott lemezmeghajtókat egy Azure-adatközpontba szállítsa.

Az Import/Export szolgáltatás csak az Azure Storage Azure Files importálását támogatja. A Azure Files exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot, amely adatokat továbbít a Azure Files, tekintse át és töltse ki az alábbi előfeltételek listáját. A következőt kell:

- Legyen aktív Azure-előfizetése az Import/Export szolgáltatással való használathoz.
- Legyen legalább egy Azure Storage-fiókja. Tekintse meg az Import/Export szolgáltatás által támogatott tárfiókok [és tártípusok listáját.](storage-import-export-requirements.md) Az új tárfiókok létrehozásával kapcsolatos információkért lásd: [How to Create a Storage Account (Tárfiók létrehozása).](../storage/common/storage-account-create.md)
- A támogatott típusú lemezek száma [megfelelő.](storage-import-export-requirements.md#supported-disks)
- Támogatott operációsrendszer-verziót futtató Windows [rendszer.](storage-import-export-requirements.md#supported-operating-systems)
- Töltse le a [WAImportExport 2-es](https://aka.ms/waiev2) verzióját a Windows rendszeren. Csomagolja ki az alapértelmezett `waimportexport` mappába. Például: `C:\WaImportExport`.
- FedEx-/DHL-fiókkal kell Ha nem a FedEx/DHL szolgáltatót szeretné használni, lépjen kapcsolatba Azure Data Box üzemeltetési csapatával `adbops@microsoft.com` a(Azure Data Box).
    - A fióknak érvényesnek kell lennie, egyenleggel kell rendelkezik, és visszaküldési képességekkel kell rendelkezik.
    - Hozzon létre egy nyomkövetési számot az exportálási feladathoz.
    - Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
    - Ha nincs szolgáltatói fiókja, akkor a következőt kell látnia:
        - [FedEx-fiók létrehozása,](https://www.fedex.com/en-us/create-account.html)vagy
        - [Hozzon létre egy DHL-fiókot.](http://www.dhl-usa.com/en/express/shipping/open_account.html)


## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtók előkészítése

Ez a lépés létrehoz egy naplófájlt. A naplófájl olyan alapvető információkat tárol, mint a meghajtó sorozatszáma, a titkosítási kulcs és a tárfiók adatai.

A meghajtók előkészítéséhez kövesse az alábbi lépéseket.

1. Lemezmeghajtóink csatlakoztatása a Windows rendszerhez SATA-összekötők segítségével.
2. Minden meghajtón hozzon létre egy NTFS-kötetet. Rendeljen meghajtóbetűjelet a kötethez. Ne használjon csatlakozási pontokat.
3. Módosítsa a *dataset.csv* fájlt abban a gyökérmappában, ahol az eszköz található. Attól függően, hogy fájlt vagy mappát vagy mindkettőt szeretne importálni, adjon hozzá bejegyzéseket adataset.csv *a* következő példákhoz hasonló módon.

   - **Fájl importálása:** A következő példában a másolt adatok az F: meghajtón találhatóak. A rendszer *MyFile1.txt* a *MyAzureFileshare1 gyökérkönyvtárában található fájlba.* Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiókban jön létre. A mappastruktúra megmarad.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Mappa importálása: A** *MyFolder2* mappában található összes fájl és mappa rekurzív módon át lesz másva a fájlmegosztásba. A mappastruktúra megmarad.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Több bejegyzés is készülhet ugyanabban a fájlban az importált mappáknak vagy fájloknak megfelelően.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     További információ az [adatkészlet CSV-fájlának előkészítéséről.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)


4. Módosítsa a *driveset.csv* fájlt abban a gyökérmappában, ahol az eszköz található. Adjon hozzá bejegyzéseket a *driveset.csv* a következő példákhoz hasonló módon. A meghajtókat tároló fájl tartalmazza a lemezek listáját és a megfelelő meghajtóbetűk listáját, így az eszköz megfelelően ki tudja választani az elkészítni kívánt lemezek listáját.

    Ez a példa feltételezi, hogy két lemez van csatlakoztatva, és alapszintű NTFS-kötetek G:\ és H:\ létrejönnek. A H:\nincs titkosítva, amíg a G: már titkosítva van. Az eszköz formáz és titkosítja a H:\ meghajtót csak (és nem G: \) .

   - **Nem titkosított lemezek** esetén: A  BitLocker-titkosítás engedélyezéséhez adja meg a Titkosítást.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Már titkosított lemez** esetén: Adja meg az *AlreadyEncrypted* értéket, és adja meg a BitLocker-kulcsot.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Ugyanabban a fájlban több, több meghajtónak megfelelő bejegyzés is készülhet. További információ a [meghajtókészlet CSV-fájlának előkészítéséről.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)

5. A `PrepImport` kapcsolóval másolhatja és készítheti elő az adatokat a lemezmeghajtóra. A könyvtárak és/vagy fájlok új másolási munkamenetben való másolásának első másolási munkamenetéhez futtassa a következő parancsot:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Az alábbiakban egy importálási példa látható.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Létrejön egy naplófájl a paraméterrel megadott névvel a parancssor minden `/j:` futtatásához. Minden előkészíteni kívánt meghajtó rendelkezik egy naplófájllal, amely az importálási feladat létrehozásakor fel lesz töltve. A naplófájlok nélküli meghajtók nincsenek feldolgozva.

    > [!IMPORTANT]
    > - A lemez előkészítésének befejezése után ne módosítsa a lemezmeghajtók vagy a naplófájl adatait.

További példákat a Naplófájlok [mintái című cikk tartalmaz.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Importálási feladat létrehozásához kövesse az alábbi lépéseket a Azure Portal.
1. Jelentkezzen be a https://portal.azure.com/ oldalon.
2. Keressen rá az **importálási/exportálási feladatokra.**

    ![Keresés importálási/exportálási feladatokban](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Válassza a **+ Új** lehetőséget.

    ![Új létrehozásához válassza az Új lehetőséget ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Az **Alapvető funkciókban:**

   1. Válasszon egy előfizetést.
   1. Válasszon ki egy erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget,** és hozzon létre egy újat.
   1. Adjon egy leíró nevet az importálási feladatnak. A feladatok előrehaladásának nyomon követéséhez használja a nevet.
       * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.
   1. Válassza **az Importálás az Azure-ba lehetőséget.**

    ![Importálási feladat létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Válassza **a Tovább: Feladat részletei >** a folytatáshoz lehetőséget.

5. A **Feladat részletei között:**

   1. Töltse fel az előző [1. lépés: A](#step-1-prepare-the-drives)meghajtók előkészítése során létrehozott naplófájlokat.
   1. Válassza ki a rendelés cél Azure-régióját.
   1. Válassza ki az importáláshoz a tárfiókot.

      A legördülő lista helye automatikusan ki lesz töltve a kiválasztott tárfiók régiója alapján.

   1. Ha nem szeretne részletes naplót menteni, törölje a Részletes napló mentése lehetőséget a **"waimportexport" blobtárolóban.**


   ![Importálási feladat létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   A **folytatáshoz** válassza a >lehetőséget.

4. A **Shipping (Szállítás) szolgáltatásban:**

    1. Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től különböző szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box üzemeltetési csapatával a következő elérhetőségen: , hogy a használni tervezi-e a `adbops@microsoft.com`  szállítmányozó adatait.
    1. Adjon meg egy érvényes fiókszámot, amit a szolgáltatónál hozott létre. A Microsoft ezt a fiókot használja a meghajtók vissza szállításhoz az importálási feladat befejezése után.
    1. Adja meg a teljes és érvényes kapcsolattartási nevet, telefonszámot, e-mail-címet, postai címet, várost, irányítószámot, államot/tartományt és országot/régiót.

        > [!TIP]
        > Ahelyett, hogy egyetlen felhasználónak ad meg e-mail-címet, adjon meg egy csoport e-mail-címet. Ez biztosítja, hogy akkor is megkapja az értesítéseket, ha egy rendszergazda távozik.

    ![Importálási feladat létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   A **folytatáshoz válassza** az Áttekintés + létrehozás lehetőséget.

5. A rendelés összegzésében:

   1. Tekintse át **a feltételeket,** majd válassza az "Elfogadom, hogy minden megadott információ helyes, és elfogadom a feltételeket és a feltételeket." Ezt követően a rendszer érvényesítést hajt végre.
   1. Tekintse át az összefoglalásban megadott feladatadatokat. Jegyezze fel a feladat nevét és az Azure-adatközpont szállítási címét a lemezek Azure-ba való visszaszállítása érdekében. Ezek az információk később a fuvarlevelen is fel lesz használva.
   1. Válassza a **Létrehozás** lehetőséget.

        ![Importálási feladat létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépésekkel hozhat létre importálási feladatot az Azure CLI-ban.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Feladat létrehozása

1. Az [az extension add paranccsal](/cli/azure/extension#az_extension_add) adja hozzá [az az import-export bővítményt:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egyet. Erőforráscsoport létrehozásához futtassa az [az group create](/cli/azure/group#az_group_create) parancsot:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Használhat egy meglévő tárfiókot, vagy létrehozhat egyet. Tárfiók létrehozásához futtassa az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot:

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. A lemezek szállításához használható helyek listáját az az [import-export location list paranccsal kaphatja](/cli/azure/import-export/location#az_import_export_location_list) meg:

    ```azurecli
    az import-export location list
    ```

1. Az [az import-export location show paranccsal](/cli/azure/import-export/location#az_import_export_location_show) lekért helyek a régióhoz:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Az importálási feladat létrehozásához futtassa az [az import-export create](/cli/azure/import-export#az_import_export_create) parancsot:

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
   > Ahelyett, hogy egyetlen felhasználóhoz ad meg e-mail-címet, adjon meg egy csoport e-mail-címét. Ez biztosítja, hogy akkor is értesítéseket kap, ha egy rendszergazda távozik.


1. Az [az import-export list paranccsal](/cli/azure/import-export#az_import_export_list) tekintse meg a myierg erőforráscsoport összes feladatát:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. A feladat frissítéséhez vagy a feladat megszakításhoz futtassa [az az import-export update](/cli/azure/import-export#az_import_export_update) parancsot:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

A következő lépésekkel hozhat létre importálási feladatot a Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Bár az **Az.ImportExport** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Feladat létrehozása

1. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egyet. Erőforráscsoport létrehozásához futtassa a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Használhat egy meglévő tárfiókot, vagy létrehozhat egyet. Tárfiók létrehozásához futtassa a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmagot:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. A lemezek szállításához használható helyek listáját a [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) parancsmag használatával kaphatja meg:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Használja a `Get-AzImportExportLocation` parancsmagot a `Name` paraméterrel a régió helyének lekérdezésére:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Importálási feladat létrehozásához futtassa a [következő New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) példát:

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
   > Ahelyett, hogy egyetlen felhasználóhoz ad meg e-mail-címet, adjon meg egy csoport e-mail-címét. Ez biztosítja, hogy akkor is értesítéseket kap, ha egy rendszergazda távozik.

1. A [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) parancsmag használatával tekintse meg a myierg erőforráscsoport összes feladatát:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. A feladat frissítéséhez vagy a feladat megszakításhoz futtassa az [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) parancsmagot:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: A meghajtók szállítása az Azure-adatközpontba

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A feladat frissítése követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: Az Azure-ba való adatfeltöltés ellenőrzése

Nyomon követheti a feladatot a befejezésig. A feladat befejezése után ellenőrizze, hogy az adatok fel vannak-e töltve az Azure-ba. A helyszíni adatokat csak akkor törölje, ha ellenőrizte, hogy a feltöltés sikeres volt-e.

## <a name="samples-for-journal-files"></a>Naplófájlok mintái

További **meghajtók hozzáadásához hozzon** létre egy új meghajtókészletfájlt, és futtassa az alábbi parancsot.

Az *InitialDriveset .csv* fájlban megadottaktól más lemezmeghajtókra való későbbi másolási munkamenetekhez adjon meg egy új *.csv* meghajtókészletfájlt, és adja meg értékként a paramétert. `AdditionalDriveSet` Használja ugyanazt **a naplófájlnevet,** és adjon meg egy **új munkamenet-azonosítót.** Az AdditionalDriveset CSV-fájl formátuma megegyezik az InitialDriveSet formátummal.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Az alábbiakban egy importálási példa látható.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Ha további adatokat szeretne hozzáadni ugyanathoz a meghajtókészlethez, használja a PrepImport parancsot a későbbi másolási munkamenetekhez további fájlok/könyvtárak másolásához.

Az ezt követő másolási munkamenetekhez a fájlban megadott merevlemez-meghajtókraInitialDriveset.csvadja meg ugyanazt *a* naplófájlnevet, és adjon meg egy új **munkamenet-azonosítót;**  A tárfiókkulcsot nem szükséges meg adni.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Az alábbiakban egy importálási példa látható.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Következő lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Az importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)