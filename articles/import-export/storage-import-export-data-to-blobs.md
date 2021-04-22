---
title: Az Azure Import/Export használata az adatok Azure-blobokba | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat Azure Portal azure-blobokba és -blobok között történő adatátvitelhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875682"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Az Azure Import/Export szolgáltatás használata adatok importálására Azure Blob Storage

Ez a cikk lépésről lépésre bemutatja, hogyan importálhat biztonságosan nagy mennyiségű adatot az Azure Blob Storage-ba az Azure Import/Export szolgáltatással. Az adatok Azure Blobsba való importálásához a szolgáltatás megköveteli, hogy az adatokat tartalmazó titkosított lemezmeghajtókat egy Azure-adatközpontba szállítsa.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot, amely adatokat továbbít a Azure Blob Storage, tekintse át és töltse ki a szolgáltatás előfeltételeinek alábbi listáját.
A következőt kell:

* Az Import/Export szolgáltatáshoz használható aktív Azure-előfizetéssel kell rendelkezik.
* Legalább egy Azure Storage-fiókkal és egy storage-tárolóval. Tekintse meg az Import/Export szolgáltatás által támogatott tárfiókok és [tártípusok listáját.](storage-import-export-requirements.md)
  * Az új tárfiókok létrehozásával kapcsolatos információkért lásd: [Tárfiók létrehozása.](../storage/common/storage-account-create.md)
  * A Storage-tárolóval kapcsolatos információkért kattintson [a Tároló létrehozása gombra.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* Megfelelő számú támogatott típusú [lemezzel kell.](storage-import-export-requirements.md#supported-disks)
* Támogatott operációsrendszer-verziót futtató Windows [rendszer.](storage-import-export-requirements.md#supported-operating-systems)
* Engedélyezze a BitLockert a Windows rendszeren. Lásd: [A BitLocker engedélyezése.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
* [Töltse le a LEGÚJABB WAImportExport 1-es verziót](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszeren. Az eszköz legújabb verziója olyan biztonsági frissítésekkel rendelkezik, amelyek lehetővé teszik a BitLocker-kulcs külső védője és a frissített feloldási mód funkció telepítését.

  * Csomagolja ki az alapértelmezett `waimportexportv1` mappába. Például: `C:\WaImportExportV1`.
* Legyen FedEx-/DHL-fiókja. Ha nem a FedEx/DHL szolgáltatót szeretné használni, lépjen kapcsolatba a Azure Data Box csapatával a `adbops@microsoft.com` oldalon.
  * A fióknak érvényesnek kell lennie, egyenleggel kell rendelkezik, és visszaküldési képességekkel kell rendelkezik.
  * Hozzon létre egy nyomkövetési számot az exportálási feladathoz.
  * Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
  * Ha nincs szolgáltatói fiókja, a következőt kell megtennie:
    * [FedEx-fiók létrehozása](https://www.fedex.com/en-us/create-account.html), vagy
    * [Hozzon létre egy DHL-fiókot.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtók előkészítése

Ez a lépés létrehoz egy naplófájlt. A naplófájl olyan alapvető információkat tárol, mint a meghajtó sorozatszáma, a titkosítási kulcs és a tárfiók adatai.

A meghajtók előkészítéséhez hajtsa végre a következő lépéseket.

1. Csatlakoztassa a lemezmeghajtókat a Windows rendszerhez SATA-összekötők segítségével.
2. Hozzon létre egyetlen NTFS-kötetet minden meghajtón. Rendeljen meghajtóbetűjelet a kötethez. Ne használjon csatlakozási pontokat.
3. Engedélyezze a BitLocker-titkosítást az NTFS-köteten. Windows Server rendszer használata esetén kövesse a [BitLocker engedélyezése Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)rendszeren.
4. Adatok másolása titkosított kötetre. Húzással, Robocopyval vagy bármilyen más másolási eszközzel. A naplófájl (*.jn*) ugyanabban a mappában jön létre, ahol az eszközt futtatja.

   Ha a meghajtó zárolva van, és fel kell oldania a meghajtó zárolását, a zárolás feloldásának lépései a használt esettől függően eltérőek lehetnek.

   * Ha adatokat adott hozzá egy előre titkosított meghajtóhoz (a WAImportExport eszközt nem használták titkosításra), a meghajtó zárolásának feloldásához használja az előugró ablakban található BitLocker-kulcsot (a megadott numerikus jelszót).

   * Ha adatokat adott hozzá a WAImportExport eszköz által titkosított meghajtóhoz, a következő paranccsal oldja fel a meghajtó zárolását:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Nyisson meg egy PowerShell- vagy parancssori ablakot rendszergazdai jogosultságokkal. A kibontott mappára való váltáshoz futtassa a következő parancsot:

    `cd C:\WaImportExportV1`
6. A meghajtó BitLocker-kulcsának lekért futtatásához futtassa a következő parancsot:

    `manage-bde -protectors -get <DriveLetter>:`
7. A lemez előkészítéséhez futtassa a következő parancsot. **Az adatok méretétől függően a lemez előkészítése több órát vagy napot is igénybe vehet.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    A naplófájl ugyanabban a mappában jön létre, ahol az eszközt futtatta. Két másik fájl is létrejön – egy *.xml* fájl (az eszközt tartalmazó mappa) és egy *drive-manifest.xml* (az adatokat tartalmazó mappa).

    A használt paraméterek leírását az alábbi táblázat tartalmazza:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j:     |A naplófájl neve .jn kiterjesztéssel. A rendszer meghajtónként létrehoz egy naplófájlt. Javasoljuk, hogy a naplófájl neveként a lemez sorozatszámát használja.         |
    |/id:     |A munkamenet-azonosító. A parancs minden példányához használjon egyedi munkamenetszámot.      |
    |/t:     |A leszállított lemez meghajtóbetűjele. Például: `D` meghajtó.         |
    |/bk:     |A meghajtó BitLocker-kulcsa. Numerikus jelszava a kimenetében `manage-bde -protectors -get D:`      |
    |/srcdir:     |A leszállított lemez meghajtóbetűjele, majd a `:\` következő: . Például: `D:\`.         |
    |/dstdir:     |A céltároló neve az Azure Storage-ban.         |
    |/blobtype:     |Ez a beállítás határozza meg, hogy milyen típusú blobokba szeretné importálni az adatokat. Blokkbloboknál a blob típusa , `BlockBlob` a lapblobok pedig `PageBlob` .         |
    |/skipwrite:     | Megadja, hogy nincs szükség új adatok másolására, és a lemezen lévő meglévő adatok elő lesznek készítve.          |
    |/enablecontentmd5:     |Ha engedélyezve van, a biztosítja, hogy az MD5 kiszámítása és tulajdonságként való beállítása `Content-md5` minden blobon meg legyen állítva. Ezt a beállítást csak akkor használja, ha az adatok Azure-ba való feltöltése után szeretné használni a `Content-md5` mezőt. <br> Ez a beállítás nincs hatással az adatintegritás-ellenőrzésre (ez alapértelmezés szerint megtörténik). A beállítás azonban megnöveli az adatok felhőbe való feltöltéséhez szükséges időt.          |
8. Ismételje meg az előző lépést minden olyan lemezen, amit ki kell szállítanunk. Létrejön egy naplófájl a megadott névvel a parancssor minden futtatásához.

    > [!IMPORTANT]
    > * A naplófájllal együtt a fájl ugyanabban a mappában is létrejön, `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ahol az eszköz található. A feladat létrehozásakor a naplófájl helyére az .xml fájl kerül, ha a naplófájl túl nagy.
   > * A portál által engedélyezett naplófájl maximális mérete 2 MB. Ha a naplófájl túllépi ezt a korlátot, a rendszer hibát ad vissza.

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

A következő lépésekkel hozhat létre importálási feladatot a Azure Portal.

1. Jelentkezzen be a (Bejelentkezés) https://portal.azure.com/ útjára.
2. Keressen rá az **importálási/exportálási feladatokra.**

   ![Keresés importálási/exportálási feladatokban](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Válassza a **+ Új** lehetőséget.

   ![Új létrehozásához válassza az Új lehetőséget ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Az **Alapok:**

   1. Válasszon egy előfizetést.
   1. Válasszon ki egy erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget,** és hozzon létre egy újat.
   1. Adjon egy leíró nevet az importálási feladatnak. A feladatok előrehaladásának nyomon követéséhez használja a nevet.
      * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
      * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.

   1. Válassza **az Importálás az Azure-ba lehetőséget.**

    ![Importálási feladat létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Válassza **a Tovább: Feladat részletei >** a folytatáshoz lehetőséget.

5. A **Feladat részletei között:**

   1. Töltse fel az előző [1. lépés: A](#step-1-prepare-the-drives)meghajtók előkészítése során létrehozott naplófájlokat. Ha használta, minden előkészített meghajtóhoz töltsön fel `waimportexport.exe version1` egy fájlt. Ha a naplófájl mérete meghaladja a 2 MB-ot, akkor használhatja a naplófájllal `<Journal file name>_DriveInfo_<Drive serial ID>.xml` együtt létrehozott fájlt is.
   1. Válassza ki a rendelés cél Azure-régióját.
   1. Válassza ki az importáláshoz a tárfiókot.
      
      A legördülő lista helye automatikusan ki lesz töltve a kiválasztott tárfiók régiója alapján.
   1. Ha nem szeretne részletes naplót menteni, törölje a Részletes napló mentése lehetőséget a **"waimportexport" blobtárolóban.**

   ![Importálási feladat létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   A **folytatáshoz** válassza a >lehetőséget.

6. A **Shipping (Szállítás) szolgáltatásban:**

   1. Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től különböző szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. A Azure Data Box szállítóval kapcsolatos információkért lépjen kapcsolatba az üzemeltetési csapattal a `adbops@microsoft.com`  következő elérhetőségen: .
   1. Adjon meg egy érvényes szolgáltatói fiókszámot, amit a szolgáltatónál hozott létre. A Microsoft ezt a fiókot használja a meghajtók vissza szállításhoz az importálási feladat befejezése után. Ha nincs fiókja, hozzon létre egy [FedEx-](https://www.fedex.com/us/oadr/) vagy [DHL-szolgáltatói](https://www.dhl.com/) fiókot.
   1.  Adja meg a teljes és érvényes kapcsolattartási nevet, telefonszámot, e-mail-címet, postai címet, várost, irányítószámot, államot/tartományt és országot/régiót.

       > [!TIP]
       > Ahelyett, hogy egyetlen felhasználónak ad meg e-mail-címet, adjon meg egy csoport e-mail-címet. Ez biztosítja, hogy akkor is megkapja az értesítéseket, ha egy rendszergazda távozik.

   ![Importálási feladat létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   A **folytatáshoz válassza** az Áttekintés + létrehozás lehetőséget.

7. A rendelés összegzésében:

   1. Tekintse át **a feltételeket,** majd válassza az "Elfogadom, hogy minden megadott információ helyes, és elfogadom a feltételeket és a feltételeket." A rendszer ezután ellenőrzést végez.
   1. Tekintse át az összefoglalásban megadott feladatadatokat. Jegyezze fel a feladat nevét és az Azure-adatközpont szállítási címét a lemezek Azure-ba való visszaszállítása érdekében. Ezt az információt később a fuvarlevelen használjuk fel.
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
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

1. Importálási feladat létrehozásához futtassa a következő [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) példát:

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
   > Ahelyett, hogy egyetlen felhasználónak ad meg e-mail-címet, adjon meg egy csoport e-mail-címet. Ez biztosítja, hogy akkor is megkapja az értesítéseket, ha egy rendszergazda távozik.

1. A [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) parancsmag használatával tekintse meg a myierg erőforráscsoport összes feladatát:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. A feladat frissítéséhez vagy megszakításhoz futtassa az [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) parancsmagot:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>3. lépés (nem kötelező): Ügyfél által felügyelt kulcs konfigurálása

Hagyja ki ezt a lépést, és lépjen a következő lépésre, ha a Microsoft által felügyelt kulccsal szeretné védeni a meghajtók BitLocker-kulcsait. Ha saját kulcsot konfigurál a BitLocker-kulcs védelméhez, kövesse az ügyfél által kezelt kulcsok konfigurálása [az Azure Import/Export](storage-import-export-encryption-key-portal.md)Azure Key Vault használatával a következő fájlban található Azure Portal.

## <a name="step-4-ship-the-drives"></a>4. lépés: A meghajtók szállítása

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5. lépés: A feladat frissítése követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6. lépés: Az Azure-ba való adatfeltöltés ellenőrzése

A feladat nyomon követése a befejezésig. A feladat befejezése után ellenőrizze, hogy az adatok fel vannak-e töltve az Azure-ba. A helyszíni adatokat csak akkor törölje, ha ellenőrizte, hogy a feltöltés sikeres volt-e.

## <a name="next-steps"></a>Következő lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Az importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
