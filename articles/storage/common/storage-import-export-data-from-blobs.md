---
title: Az Azure import/export használata adatok exportálására az Azure-Blobokból | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre exportálási feladatokat Azure Portal az adatok Azure-Blobokból történő átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: c9ce265707743d98f6c93d3facca33e16d1b75ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513497"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Az Azure Import/Export szolgáltatás használata adatok Azure Blob-tárolóból való exportálására

Ez a cikk részletesen bemutatja, hogyan használható az Azure import/export szolgáltatás az Azure Blob Storage-ból származó nagy mennyiségű adatok biztonságos exportálásához. A szolgáltatáshoz üres meghajtók szállítása szükséges az Azure-adatközpontba. A szolgáltatás exportálja az adatait a Storage-fiókjából a meghajtókra, majd visszaküldi a meghajtókat.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy exportálási feladatot az adatok Azure-Blob Storageba való átviteléhez, gondosan tekintse át és végezze el az alábbi, a szolgáltatásra vonatkozó előfeltételeket tartalmazó listát.
A következőket kell tennie:

- Az import/export szolgáltatáshoz használható aktív Azure-előfizetéssel rendelkezik.
- Rendelkeznie kell legalább egy Azure Storage-fiókkal. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md). További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](storage-account-create.md).
- Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül.
- Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen kapcsolatba Azure Data Box Operations csapatával a következő címen: `adbops@microsoft.com` .
  - A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
  - Nyomkövetési szám létrehozása az exportálási feladatokhoz.
  - Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
  - Ha nem rendelkezik Carrier-fiókkal, lépjen a következőre:
    - [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy
    - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1. lépés: exportálási feladatok létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy exportálási feladatot a Azure Portal.

1. Jelentkezzen be a következőre: <https://portal.azure.com/> .
2. Lépjen az **összes szolgáltatás > Storage > importálási/exportálási feladatok lehetőségre**.

    ![Ugrás az importálási/exportálási feladatokra](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kattintson az **importálási/exportálási feladatok létrehozása**lehetőségre.

    ![Kattintson az Importálás/exportálás feladata elemre.](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Az **alapjaiban**:

    - Válassza **az Exportálás az Azure-ból**lehetőséget.
    - Adjon meg egy leíró nevet az exportálási feladatokhoz. Használja az Ön által választott nevet a feladatok előrehaladásának nyomon követéséhez.
        - A név csak kisbetűket, számokat, kötőjeleket és aláhúzást tartalmazhat.
        - A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.
    - Válasszon egy előfizetést.
    - Adjon meg vagy válasszon ki egy erőforráscsoportot.

        ![Alapvető beállítások](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. A **feladatok részletei**:

    - Válassza ki azt a Storage-fiókot, ahol az exportálandó adat található. Használjon egy olyan Storage-fiókot, amely közel van a helyhez.
    - A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.
    - Adja meg a Storage-fiókból az üres meghajtóra vagy meghajtókra exportálni kívánt blob-adatok számát.
    - Válassza ki a Storage-fiókban lévő összes blob-érték **exportálását** .

         ![Összes exportálása](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Megadhatja, hogy mely tárolók és Blobok exportálhatók.
        - Az **exportálandó blob megadásához**használja a következőt: **egyenlő** a választóval. Adja meg a blob relatív elérési útját, amely a tároló nevével kezdődik. A legfelső szintű tároló megadásához használja a *$root* .
        - Az **összes olyan blob megadása**, amelyik előtaggal kezdődik: használja a **Start with** választót. Itt adhatja meg az előtagot, amely a továbbítási perjel (/) kezdetű. Az előtag lehet a tároló nevének előtagja, a teljes tároló neve vagy a teljes tároló neve, amelyet a blob nevének előtagja követ. A hibák elkerülése érdekében érvényes formátumban kell megadnia a blob elérési útját, ahogy az a képernyőképen is látható. További információ: [az érvényes blob-elérési utakra vonatkozó példák](#examples-of-valid-blob-paths).

           ![Kiválasztott tárolók és Blobok exportálása](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Exportálhatja a blob-lista fájlját.

        ![Exportálás a blob-lista fájljából](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Ha az exportálandó blob az adatok másolása során használatban van, az Azure import/export szolgáltatás pillanatképet készít a blobról, és átmásolja a pillanatképet.

6. **Visszaszállítási adatok**:

    - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával a `adbops@microsoft.com` használni kívánt szolgáltatóra vonatkozó információkkal.
    - Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az exportálási feladatok befejezése után.
    - Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót.

        > [!TIP]
        > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

7. **Összefoglalás**:

    - Tekintse át a feladatok részleteit.
    - Jegyezze fel a feladatok nevét, és adja meg az Azure Datacenter szállítási címet az Azure-ba történő szállításhoz.

        > [!NOTE]
        > Mindig küldje el a lemezeket az adatközpontba a Azure Portal feltüntetve. Ha a lemezeket nem megfelelő adatközpontba szállítják, a rendszer nem dolgozza fel a feladatot.

    - Az exportálási feladatok létrehozásának befejezéséhez kattintson **az OK** gombra.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>2. lépés: a meghajtók szállítása

Ha nem ismeri a szükséges meghajtók számát, lépjen a [meghajtók számának ellenőrzése](#check-the-number-of-drives)elemre. Ha ismeri a meghajtók számát, folytassa a meghajtók szállításával.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. lépés: a feladatok frissítése a nyomkövetési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4. lépés: a lemezek fogadása

Amikor az irányítópult jelentést készít a feladatokról, a rendszer elküldi a lemezeket, és a szállítás nyomon követési száma elérhető a portálon.

1. Miután az exportált adattal rendelkező meghajtókat kapott, a BitLocker-kulcsokat a meghajtók zárolásának feloldásához kell kérnie. Lépjen az exportálási feladatokhoz a Azure Portal. Kattintson az **Importálás/exportálás** fülre.
2. Válassza ki, majd kattintson az exportálási feladatokra a listából. Nyissa meg a **titkosítást** , és másolja a kulcsokat.

   ![Az exportálási feladatokhoz tartozó BitLocker-kulcsok megtekintése](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. A lemezek zárolásának feloldásához használja a BitLocker kulcsait.

Az Exportálás befejeződött.

## <a name="step-5-unlock-the-disks"></a>5. lépés: a lemezek zárolásának feloldása

Ha a WAImportExport eszköz 1.4.0.300 használja, használja a következő parancsot a meghajtó zárolásának feloldásához:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file> /driveLetter:<Drive letter>`  

Íme egy példa a minta bemenetre.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Ha az eszköz korábbi verzióit használja, a BitLocker párbeszédpanellel oldhatja fel a meghajtót.

Ekkor törölheti a feladatot, vagy meghagyhatja. A feladatok 90 nap után automatikusan törlődnek.

## <a name="check-the-number-of-drives"></a>Meghajtók számának ellenőrzése

Ez a *választható* lépés segít megszabni az exportálási feladatokhoz szükséges meghajtók számát. Ezt a lépést a [támogatott operációsrendszer-verziókat](storage-import-export-requirements.md#supported-operating-systems)futtató Windows rendszereken hajtsa végre.

1. [Töltse le a WAImportExport 1-es verzióját](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre.
2. Bontsa ki az alapértelmezett mappát `waimportexportv1` . Például: `C:\WaImportExportV1`.
3. Nyisson meg egy PowerShell-vagy parancssori ablakot rendszergazdai jogosultságokkal. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:

   `cd C:\WaImportExportV1`

4. A kiválasztott blobokhoz szükséges lemezek számának ellenőrzéséhez futtassa a következő parancsot:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    A paramétereket a következő táblázat ismerteti:

    |Parancssori paraméter|Description|  
    |--------------------------|-----------------|  
    |**/logdir:**|Választható. A naplózási könyvtár. A részletes naplófájlokat a rendszer erre a könyvtárba írja. Ha nincs megadva, a rendszer az aktuális könyvtárat használja a napló könyvtáraként.|  
    |**SN**|Kötelező. Az exportálási feladatokhoz tartozó Storage-fiók neve.|  
    |**sk**|Csak akkor szükséges, ha nincs megadva tároló SAS. Az exportálási feladatokhoz tartozó Storage-fiókhoz tartozó fiók kulcsa.|  
    |**csas**|Csak akkor szükséges, ha nincs megadva a Storage-fiók kulcsa. A tároló SAS az exportálási feladatokba exportálandó Blobok listázásához.|  
    |**/ExportBlobListFile:**|Kötelező. Annak az XML-fájlnak az elérési útja, amely a Blobok elérési útját vagy a blob elérési útjának előtagjainak listáját tartalmazza. Az `BlobListBlobPath` importálási/exportálási szolgáltatás REST API [put-feladatok](/rest/api/storageimportexport/jobs) műveletének elemében használt fájlformátum.|  
    |**/DriveSize:**|Kötelező. Az exportálási feladatokhoz használandó meghajtók mérete, *például*500 GB, 1,5 TB.|  

    Tekintse meg [a PreviewExport parancs példáját](#example-of-previewexport-command).

5. Győződjön meg arról, hogy olvasható/írható az exportálási feladatokhoz szállítandó meghajtókra.

### <a name="example-of-previewexport-command"></a>Példa a PreviewExport parancs használatára

A következő példa a parancsot mutatja be `PreviewExport` :  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

A blob-lista exportálásakor a Blobok nevei és a blob-előtagok is szerepelhetnek az itt látható módon:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Az Azure import/export eszköz felsorolja az összes exportálandó blobot, és kiszámítja, hogyan csomagolja őket a megadott méretű meghajtókra, figyelembe véve a szükséges terhelést, majd a megbecsüli a Blobok tárolásához szükséges meghajtók számát és a meghajtó használati adatait.  

Íme egy példa a kimenetre, és a tájékoztató naplók kimaradtak:  

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

## <a name="examples-of-valid-blob-paths"></a>Példák érvényes blob-elérési utakra

A következő táblázat példákat mutat be a Blobok érvényes elérési útjaira:

   | Szelektor | BLOB elérési útja | Description |
   | --- | --- | --- |
   | Kezdete |/ |A Storage-fiókban lévő összes blob exportálása |
   | Kezdete |/$root/ |A gyökér tárolóban lévő összes blob exportálása |
   | Kezdete |/book |Minden blob exportálása minden olyan tárolóban, **amely előtaggal** kezdődik |
   | Kezdete |zene |A **tárolóban** lévő összes blob exportálása |
   | Kezdete |/music/love |Az összes olyan blob exportálása **a tárolóba, amely** a **Love** előtaggal kezdődik |
   | Egyenlő |$root/logo.bmp |A blob **logo.bmp** exportálása a gyökér tárolóba |
   | Egyenlő |videók/story.mp4 |BLOB- **story.mp4** exportálása a tároló- **videókban** |

## <a name="next-steps"></a>További lépések

- [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
- [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
