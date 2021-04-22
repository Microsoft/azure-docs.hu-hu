---
title: Adatok exportálása Az Azure Import/Export használatával az Azure-blobok | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre exportálási feladatokat Azure Portal Azure Blobsból történő adatátvitelhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875700"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Az Azure Import/Export szolgáltatás használata adatok Azure Blob-tárolóból való exportálására

Ez a cikk lépésről lépésre bemutatja, hogyan exportálhat biztonságosan nagy mennyiségű adatot az Azure Blob Storage-ból az Azure Import/Export szolgáltatás használatával. A szolgáltatás megköveteli, hogy üres meghajtókat szállítson az Azure-adatközpontba. A szolgáltatás exportálja az adatokat a tárfiókból a meghajtókra, majd visszaszedi a meghajtókat.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy exportálási feladatot, amely adatokat továbbít a Azure Blob Storage, alaposan tekintse át és töltse ki a szolgáltatás előfeltételeinek alábbi listáját.
A következőt kell:

- Rendelkezik aktív Azure-előfizetéssel, amely használható az Import/Export szolgáltatáshoz.
- Legyen legalább egy Azure Storage-fiókja. Tekintse meg az Import/Export szolgáltatás által támogatott tárfiókok [és tártípusok listáját.](storage-import-export-requirements.md) Az új tárfiókok létrehozásával kapcsolatos információkért lásd: [How to Create a Storage Account (Tárfiók létrehozása).](../storage/common/storage-account-create.md)
- A támogatott típusú lemezek száma [megfelelő.](storage-import-export-requirements.md#supported-disks)
- FedEx-/DHL-fiókkal kell Ha nem a FedEx/DHL szolgáltatót szeretné használni, lépjen kapcsolatba a Azure Data Box csapatával a `adbops@microsoft.com` oldalon.
  - A fióknak érvényesnek kell lennie, egyenleggel kell rendelkezik, és visszaküldési képességekkel kell rendelkezik.
  - Hozzon létre egy nyomkövetési számot az exportálási feladathoz.
  - Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
  - Ha nincs szolgáltatói fiókja, a következőt kell megtennie:
    - [FedEx-fiók létrehozása](https://www.fedex.com/en-us/create-account.html), vagy
    - [Hozzon létre egy DHL-fiókot.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-create-an-export-job"></a>1. lépés: Exportálási feladat létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az exportálási feladat létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. Jelentkezzen be a (Bejelentkezés) <https://portal.azure.com/> útjára.
2. Keressen rá az **importálási/exportálási feladatokra.**

    ![Importálási/exportálási feladatok keresése](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Válassza a **+ Új** lehetőséget.

    ![Új létrehozásához válassza az + Új lehetőséget ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Az **Alapok:**

   1. Válasszon egy előfizetést.
   1. Válasszon ki egy erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget,** és hozzon létre egy újat.
   1. Adjon egy leíró nevet az importálási feladatnak. A feladatok előrehaladásának nyomon követéséhez használja a nevet.
       * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.

   1. Válassza **az Exportálás az Azure-ból lehetőséget.**

    ![Az exportálási rendelés alapszintű beállításai](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Válassza **a Tovább: Feladat részletei >** a folytatáshoz lehetőséget.

5. A **Feladat részletei között:**

   1. Válassza ki azt az Azure-régiót, ahol az adatok jelenleg vannak.
   1. Válassza ki azt a tárfiókot, amelyből adatokat szeretne exportálni. A helyhez közeli tárfiókot használjon.

      A legördülő lista helye automatikusan ki lesz töltve a kiválasztott tárfiók régiója alapján.

   1. Adja meg a tárfiókból az üres meghajtóra vagy meghajtókra exportálni kívánt blobadatokat. Válasszon egyet a következő három módszer közül.

      - Válassza az **Összes blobadat** exportálása lehetőséget a tárfiókban.

        ![Az összes exportálása](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Válassza **a Kijelölt tárolók és blobok** lehetőséget, majd adja meg az exportálni kívánt tárolókat és blobokat. A kijelölési módszerek közül több is használható. A Hozzáadás **lehetőség** kiválasztása megnyit egy panelt a jobb oldalon, ahol hozzáadhatja a kijelölési sztringeket.

        |Beállítás|Leírás|
        |------|-----------|      
        |**Tárolók hozzáadása**|A tárolóban lévő összes blob exportálása.<br>Válassza **a Tárolók hozzáadása** lehetőséget, majd adja meg az egyes tárolók nevét.|
        |**Blobok hozzáadása**|Adja meg az exportálni kívánt blobokat.<br>Válassza **a Blobok hozzáadása lehetőséget.** Ezután adja meg a blob relatív elérési útját a tároló nevével kezdve. A *$root* adja meg a gyökértárolót.<br>A blob elérési útjait érvényes formátumban kell meg adnia, hogy elkerülje a feldolgozás során előforduló hibákat, ahogy az ezen a képernyőképen látható. További információ: Példák érvényes [blobútvonalakra.](#examples-of-valid-blob-paths)|
        |**Előtagok hozzáadása**|Használjon előtagot a hasonló nevű tárolók vagy hasonló nevű blobok egy tárolóban való kiválasztásához. Az előtag lehet a tároló nevének előtagja, a teljes tárolónév vagy egy teljes tárolónév, amelyet a blobnév előtagja követ. |

        ![Kijelölt tárolók és blobok exportálása](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Válassza az **Exportálás bloblista-fájlból (XML formátum)** lehetőséget, majd válasszon ki egy XML-fájlt, amely a tárfiókból exportálni kívánt blobok elérési útjait és előtagját tartalmazza. Létre kell helyeznie az XML-fájlt, és a tárfiók tárolójában kell tárolnia. A fájl nem lehet üres.

      > [!IMPORTANT]
      > Ha XML-fájl használatával választja ki az exportálni kívánt blobokat, győződjön meg arról, hogy az XML érvényes elérési utakat és/vagy előtagokat tartalmaz. Ha a fájl érvénytelen vagy egyik adat sem egyezik a megadott elérési útokkal, a rendelés részleges adatokkal végződik, vagy nem exportál adatokat.

       Az XML-fájlok tárolóhoz való hozzáadásáról lásd: [Rendelés exportálása XML-fájl használatával.](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)

      ![Exportálás bloblista-fájlból](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Ha az adatok másolása során egy exportálni szükséges blob használatban van, az Azure Import/Export szolgáltatás pillanatképet készít a blobról, és átmásolja a pillanatképet.

   A folytatáshoz válassza a **Tovább: >** lehetőséget.

6. A **Shipping (Szállítás) szolgáltatásban:**

    - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től különböző szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box üzemeltetési csapatával a következő elérhetőségen: , hogy a használni tervezi-e a `adbops@microsoft.com`  szállítmányozót.
    - Adjon meg egy érvényes fiókszámot, amit a szolgáltatónál hozott létre. A Microsoft ezzel a fiókkal szállítja vissza Önnek a meghajtókat az exportálási feladat befejezése után.
    - Adja meg a teljes és érvényes kapcsolattartási nevet, telefonszámot, e-mail-címet, postai címet, várost, irányítószámot, államot/tartományt, valamint az országot/régiót.

        > [!TIP]
        > Ahelyett, hogy egyetlen felhasználóhoz ad meg e-mail-címet, adjon meg egy csoport e-mail-címét. Ez biztosítja, hogy akkor is értesítéseket kap, ha egy rendszergazda távozik.

    A **folytatáshoz válassza az Áttekintés + létrehozás** lehetőséget.

7. Az **Áttekintés + létrehozás:**

   1. Tekintse át a feladat részleteit.
   1. Jegyezze fel a feladat nevét és a megadott Azure-adatközpont szállítási címét a lemezek Azure-ba való szállítására.

      > [!NOTE]
      > Mindig küldje el a lemezeket az adatközpontba, amely a Azure Portal. Ha a lemezeket nem a megfelelő adatközpontba szállítják, a feladat nem lesz feldolgozva.

   1. Tekintse át **az adatvédelmi** és a forrásadatok törlésére vonatkozó megrendelés feltételeit. Ha elfogadja a feltételeket, jelölje be a feltételek alatti jelölőnégyzetet. Megkezdődik a rendelés érvényesítése.

   ![Exportálási rendelés áttekintése és létrehozása](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Az ellenőrzés után válassza a **Létrehozás lehetőséget.**

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépésekkel hozhat létre exportálási feladatot a Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Feladat létrehozása

1. Az [az extension add paranccsal](/cli/azure/extension#az_extension_add) adja hozzá [az az import-export bővítményt:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. A lemezek fogadásához használt helyek listáját az az [import-export location list paranccsal kaphatja](/cli/azure/import-export/location#az_import_export_location_list) meg:

    ```azurecli
    az import-export location list
    ```

1. Futtassa a következő [az import-export create parancsot](/cli/azure/import-export#az_import_export_create) a meglévő tárfiókot használó exportálási feladat létrehozásához:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Ahelyett, hogy egyetlen felhasználónak ad meg e-mail-címet, adjon meg egy csoport e-mail-címet. Ez biztosítja, hogy akkor is megkapja az értesítéseket, ha egy rendszergazda távozik.

   Ez a feladat a tárfiók összes blobját exportálja. Ha az --export értékét lecseréli, megadhat egy exportálni kívánt **blobot:**

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Ez a paraméterérték exportálja a *logo.bmp* nevű blobot a gyökértárolóba.

   Lehetősége van arra is, hogy előtag használatával kijelöli a tárolóban lévő összes blobot. Cserélje le ezt az értéket az **--export értékre:**

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   További információ: Példák érvényes [blobútvonalakra.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Ha az exportálni szükséges blob használatban van az adatok másolása során, az Azure Import/Export szolgáltatás pillanatképet készít a blobról, és átmásolja a pillanatképet.

1. Az [az import-export list paranccsal](/cli/azure/import-export#az_import_export_list) tekintse meg a myierg erőforráscsoport összes feladatát:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. A feladat frissítéséhez vagy a feladat megszakításhoz futtassa [az az import-export update](/cli/azure/import-export#az_import_export_update) parancsot:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

A következő lépésekkel hozhat létre exportálási feladatot a Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Bár az **Az.ImportExport** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Feladat létrehozása

1. A [get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) parancsmag használatával lekért lista azon helyekről, amelyekről lemezeket fogadhat:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Futtassa a [következő New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) példát a meglévő tárfiókot használó exportálási feladat létrehozásához:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Ahelyett, hogy egyetlen felhasználóhoz ad meg e-mail-címet, adjon meg egy csoport e-mail-címét. Ez biztosítja, hogy akkor is értesítéseket kap, ha egy rendszergazda távozik.

   Ez a feladat a tárfiók összes blobját exportálja. Az **-ExportBlobListblobPath** elemnél lecserélve megadhatja az exportálni kívánt blobot:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Ez a paraméterérték exportálja alogo.bmp *nevű* blobot a gyökértárolóba.

   Lehetősége van arra is, hogy előtag használatával kijelöli a tárolóban lévő összes blobot. Cserélje le ezt az értéket az **-ExportBlobListblobPath értékre:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   További információ: Példák érvényes [blobútvonalakra.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Ha az exportálni szükséges blob használatban van az adatok másolása során, az Azure Import/Export szolgáltatás pillanatképet készít a blobról, és átmásolja a pillanatképet.

1. A [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) parancsmag használatával tekintse meg a myierg erőforráscsoport összes feladatát:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. A feladat frissítéséhez vagy a feladat megszakításhoz futtassa az [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) parancsmagot:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>2. lépés: A meghajtók szállítása

Ha nem tudja, hogy hány meghajtóra van szüksége, tekintse meg a Meghajtók számának [ellenőrzése jelölőnégyzetet.](#check-the-number-of-drives) Ha ismeri a meghajtók számát, folytassa a meghajtók szállítását.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. lépés: A feladat frissítése követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4. lépés: A lemezek fogadása

Amikor az irányítópult jelenti, hogy a feladat befejeződött, a lemezek kiszállításra kerülnek, és a szállítmány nyomkövetési száma elérhető a portálon.

1. Miután az exportált adatokat is megkapja a meghajtókat, be kell szereznie a BitLocker-kulcsokat a meghajtók zárolásának feloldásához. Ugrás az exportálási feladatra a Azure Portal. Kattintson **az Importálás/Exportálás fülre.**
2. Válassza ki az exportálási feladatot, és kattintson rá a listából. A Titkosítás **gombra ugrás** után másolja ki a kulcsokat.

   ![BitLocker-kulcsok megtekintése exportálási feladathoz](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. A BitLocker-kulcsokkal oldja fel a lemezek zárolását.

Az exportálás befejeződött.

## <a name="step-5-unlock-the-disks"></a>5. lépés: A lemezek zárolásának feloldása

A meghajtó zárolásának feloldásához használja a következő parancsot:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Itt egy példa a mintabemenetre.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Jelenleg törölheti vagy elhagyhatja a feladatot. A feladatok 90 nap után automatikusan törlődnek.

## <a name="check-the-number-of-drives"></a>A meghajtók számának ellenőrzése

Ez *a választható* lépés segít meghatározni az exportálási feladathoz szükséges meghajtók számát. Ezt a lépést egy támogatott operációsrendszer-verziót futtató Windows [rendszeren hajtsa végre.](storage-import-export-requirements.md#supported-operating-systems)

1. Töltse le a [WAImportExport 1-es](https://www.microsoft.com/download/details.aspx?id=42659) verzióját a Windows rendszeren.
2. Csomagolja ki az alapértelmezett `waimportexportv1` mappába. Például: `C:\WaImportExportV1`.
3. Nyisson meg egy PowerShell- vagy parancssori ablakot rendszergazdai jogosultságokkal. A kibontott mappára való váltáshoz futtassa a következő parancsot:

   `cd C:\WaImportExportV1`

4. A kiválasztott blobok szükséges lemezszámának ellenőrzéséhez futtassa a következő parancsot:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    A paraméterek leírását az alábbi táblázat tartalmazza:

    |Parancssori paraméter|Description|
    |--------------------------|-----------------|
    |**/logdir:**|Választható. A naplókönyvtár. A részletes naplófájlok ebbe a könyvtárba vannak írva. Ha nincs megadva, a rendszer az aktuális könyvtárat használja naplókönyvtárként.|
    |**/sn:**|Kötelező. Az exportálási feladathoz használt tárfiók neve.|
    |**/sk:**|Csak akkor szükséges, ha nincs megadva tároló SAS. Az exportálási feladathoz használt tárfiók fiókkulcsa.|
    |**/csas:**|Csak akkor szükséges, ha nincs megadva tárfiókkulcs. A tároló SAS-ét az exportálási feladatban exportálni kell a blobok listázására.|
    |**/ExportBlobListFile:**|Kötelező. Az exportálni kívánt blobok blobútvonal-listáját vagy blob-elérésiút-előtagját tartalmazó XML-fájl elérési útja. Az Import/Export szolgáltatás feladatműveletének elemében használt `BlobListBlobPath` fájlformátum REST API. [](/rest/api/storageimportexport/jobs)|
    |**/DriveSize:**|Kötelező. Az exportálási feladathoz használni kívánt meghajtók mérete, *például*, 500 GB, 1,5 TB.|

    Lásd a [PreviewExport parancs példáját.](#example-of-previewexport-command)

5. Ellenőrizze, hogy tud-e olvasni/írni az exportálási feladathoz kiszállított meghajtókra.

### <a name="example-of-previewexport-command"></a>Példa a PreviewExport parancs használatára

Az alábbi példa a parancsot `PreviewExport` mutatja be:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

A bloblista exportálási fájlja blobneveket és blobelőtagokat tartalmazhat, az itt látható módon:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Az Azure Import/Export eszköz felsorolja az összes exportálni kívánt blobot, és kiszámítja, hogyan csomagolja őket a megadott méretű meghajtókba, figyelembe véve a szükséges többletterhelést, majd megbecslést ad a blobok és a meghajtóhasználati információkhoz szükséges meghajtók számára.

Példa a kimenetre, információs naplók kihagyva:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Példák érvényes blobútvonalakra

Az alábbi táblázat példákat mutat be az érvényes blobútvonalakra:

   | Szelektor | Blob elérési útja | Description |
   | --- | --- | --- |
   | Ezzel kezdődik |/ |A tárfiók összes blobjának exportálása |
   | Ezzel kezdődik |/$root/ |A gyökértároló összes blobjának exportálása |
   | Ezzel kezdődik |/book |Minden olyan tárolóban lévő blob exportálása, amely a book előtaggal **kezdődik** |
   | Ezzel kezdődik |/music/ |Az összes blob exportálása a tárolókban – **zene** |
   | Ezzel kezdődik |/music/love |Exportálja az összes olyan blobot a **tárolókban, amelyek a** love előtaggal **kezdődnek** |
   | Egyenlő |$root/logo.bmp |Blobtároló logo.bmpexportálása a gyökértárolóban |
   | Egyenlő |videók/story.mp4 |**Blob-story.mp4** exportálása tárolóvideókban  |

## <a name="next-steps"></a>Következő lépések

- [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
- [Az importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
