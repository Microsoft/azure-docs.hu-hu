---
title: Nagy értékű adatkészletek lekérése ismétlődő exportálással
description: Ez a cikk segítséget nyújt a nagy mennyiségű adatok rendszeres exportálásához Azure Cost Management-ből származó exportálással.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509678"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Nagy értékű adatkészletek lekérése ismétlődő exportálással

Ez a cikk segítséget nyújt a nagy mennyiségű adatok rendszeres exportálásához Azure Cost Management-ből származó exportálással. Az exportálás az ajánlott módszer a nem összesített költségadatok lekérésére. Különösen akkor, amikor a használati adatokat tartalmazó fájlok túl nagyok ahhoz, hogy megbízhatóan meg lehessen őket hívni és letölteni a Usage Details API-val. Az exportált adatok a kiválasztott Azure Storage-fiókba lesznek helyezve. Ezt követően betöltheti az adatokat a saját rendszereibe, és igény szerint elemezheti őket. Az Exports az Azure Portalon való konfigurálásához lásd az [adatok exportálását](tutorial-export-acm-data.md) ismertető szakaszt.

Ha az exportálást különböző hatókörökben szeretné automatizálni, a következő szakaszban található minta API-kérelem jó kiindulási pont lehet. Az Exports API-val az általános környezeti konfiguráció részeként hozhat létre automatikus exportálást. Az automatikus exportálással biztosíthatja, hogy a szükséges adatok rendelkezésére álljanak. Az Azure használatának bővítésével párhuzamosan ezt a saját szervezeti rendszerében is használhatja.

## <a name="common-export-configurations"></a>Az exportálások gyakori konfigurációi

Az első exportálás létrehozása előtt állapítsa meg, milyen az adott forgatókönyv, és milyen konfigurációs beállításokkal valósulhat meg. Fontolja meg a következő exportálási beállítások alkalmazását:

- **Ismétlődés** – Meghatározza az exportálási feladat futtatásának gyakoriságát és a fájlok Azure Storage-fiókba való helyezésének idejét. Napi, heti és havi ütemezés választható. Próbálja meg úgy konfigurálni az ismétlődést, hogy az megfelelő legyen a szervezet belső rendszere által használt adatimportálási feladatokhoz.
- **Ismétlődési időtartam** – Meghatározza az exportálás érvényességének időtartamát. A fájlokat a rendszer csak az ismétlődési időtartamban exportálja.
- **Időkeret** – Meghatározza az egy adott futtatás során az exportálás által előállított adatok mennyiségét. Gyakran használt lehetőség az Ebben a hónapban és az Ez a hét.
- **Kezdő dátum** – Konfigurálja az exportálás ütemezésének kezdetét. Az exportálás létrehozása a Kezdő dátum értékeként megadott napon történik, majd ezt követően az Ismétlődés alapján.
- **Típus** – Az exportálásnak három típusa van:
  - Tényleges költség – Egy meghatározott időszak alatt keletkező és a számlán megjelenő összes használatot és költséget mutatja.
  - Amortizált költség – Egy meghatározott időszak alatt keletkező és a számlán megjelenő összes használatot és költséget mutatja a vonatkozó foglalásvásárlási költségekre alkalmazott amortizációval együtt.
  - Használat – A 2020. július 20. előtt létrehozott valamennyi exportálás Használat típusú. Frissítse az összes ütemezett exportálást Tényleges költség vagy Amortizált költség típusúra.
- **Oszlopok** – Meghatározza, hogy milyen adatmezőket tartalmazzon az exportált fájl. Ezek megegyeznek a Usage Details API-ban elérhető mezőkkel. További információkért lásd: [Usage Details API](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Napi tárgyhavi exportálás létrehozása egy előfizetéshez

Kérés URL-címe: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Nagyméretű Azure Storage-Blobok másolása

Az Azure-használati adatok az Azure Storage-fiókba blobként való exportálásának Cost Management segítségével ütemezhet. Az eredményül kapott blob méretek több mint gigabájt méretűek lehetnek. A Azure Cost Management csapat az Azure Storage csapatával dolgozott a nagyméretű Azure Storage-Blobok másolásának teszteléséhez. Az eredményeket a következő részben ismertetjük. A tárolási Blobok egyik Azure-régióból a másikba való másolása várhatóan hasonló eredményt vár.

A teljesítmény teszteléséhez a csapat blobokat vitt át az USA nyugati régiójában lévő Storage-fiókokba ugyanazon és más régióba. A csapat az azonos régióban másodpercenként 2 GB-os sebességű sebességet mért, a déli Kelet-Ázsia régióban lévő Storage-fiókok esetében pedig másodpercenként 150 MB-ra.

### <a name="test-configuration"></a>Konfiguráció tesztelése

A blob átviteli sebesség méréséhez a csapat létrehozott egy egyszerű .NET-konzolos alkalmazást, amely az Azure adatátviteli függvénytár (DLM) legújabb verziójára (v 2.0.1) hivatkozik az NuGet-on keresztül. A DLM az Azure Storage csapata által biztosított SDK, amely megkönnyíti a programozott hozzáférést az adatátviteli szolgáltatásokhoz. Ezután standard v2 Storage-fiókokat hoztak létre több régióban, és az USA nyugati régióját használják a forrásoldali régióként. A Storage-fiókokat tárolókkal látták el, ahol a 10 2 GB-os blokkos Blobok vannak tárolva. A tárolókat a DLM _TransferManager. CopyDirectoryAsync ()_ metódusával másolták más Storage-fiókba a _CopyMethod. ServiceSideSyncCopy_ kapcsolóval. A tesztek egy, a Windows 10 rendszert futtató számítógépen, 12 maggal és 1 – GbE hálózattal lettek elvégezve.

Alkalmazott Alkalmazásbeállítások:

- _TransferManager.Configurations. ParallelOperations_-  =  _környezet. ProcessorCount \* 32_. A csapat úgy találta, hogy a legnagyobb hatással van a teljes átviteli sebességre. 32-szor a magok száma a tesztelési ügyfél számára a legjobb teljesítményt biztosította.
- _ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Ha a maximális értékre állítja a beállítást, a rendszer hatékonyan továbbítja a párhuzamosságok átvitelének teljes ellenőrzését a fenti _ParallelOperations_ -beállításra.
- _TransferManager.Configurations. Blokkmérettel = 4 194 304_. A 4 MB-os adatátviteli sebességet befolyásolta, amely a legjobb tesztelési célúnak bizonyult.

További információ és mintakód: hivatkozások a [következő lépések](#next-steps) szakaszban.

### <a name="test-results"></a>Teszteredmények

| **Teszt száma** | **Régióba** | **Blobok** | **Idő (mp)** | **MB/s** | **Megjegyzések** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 párhuzamos átvitel 8 Storage-fiókkal: 4 Nyugat – 4 keleti átlag/átvitel |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 4 párhuzamos átvitel 1 Storage-fiókból egy másikba |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 8.8 Storage-fiókkal rendelkező párhuzamos átvitelek: a 4x2 keleti átlaga 4 nyugatra |
| 7 | Délkelet-Ázsia | 2 GB x 10 | 133 | 150 |   |
| 8 | Délkelet-Ázsia | 2 GB x 10 x 4 | 444 | 180 | 4 párhuzamos átvitel 1 Storage-fiókból egy másikba |

### <a name="sync-transfer-characteristics"></a>Szinkron átvitel jellemzői

Íme néhány jellemző a szolgáltatással kapcsolatos, a DML-sel használt, a használathoz kapcsolódó szolgáltatási oldali szinkronizálási átvitel jellemzőire:

- A DML egyetlen blobot vagy könyvtárat is továbbíthat. A címtár-átvitelhez keresési mintát használhat a blob előtagjának egyeztetéséhez.
- A blob-átvitelek blokkolása párhuzamosan történik. Az adatátviteli folyamat vége felé. Az egyes blob-blokkok továbbítása párhuzamosan történik.
- A rendszer aszinkron módon hajtja végre az átvitelt az ügyfélen. Az átvitel állapota időszakonként, egy _TransferContext_ -objektumban definiálható metódus visszahívásával is elérhető.
- Az átvitel ellenőrzőpontokat hoz létre az előrehaladás során, és egy _TransferCheckpoint_ objektumot tesz elérhetővé. Az objektum a legújabb ellenőrzőpontot jelöli a _TransferContext_ objektumon keresztül. Ha a _TransferCheckpoint_ az átvitel megszakítása vagy megszakadása előtt mentik, az átvitel akár hét napig is folytatható az ellenőrzőponton. Az átvitel bármely ellenőrzőpontról folytatható, nem csak a legújabb.
- Ha az adatátviteli ügyfél folyamata le van hajtva, és az ellenőrzőpont funkció megvalósítása nélkül újraindul.
  - A blob-átvitel befejeződése előtt az átvitel újraindul.
  - A Blobok némelyikének befejezése után az átvitel csak a nem teljes Blobok esetében indul újra.
- Az ügyfél-végrehajtás felfüggesztése szünetelteti az átvitelt.
- A blob-átvitel funkció absztrakt hibákat tartalmaz az ügyfél számára. Például a Storage-fiókok szabályozása általában nem eredményezi az átvitelt, de a művelet lelassítja az átvitelt.
- A szolgáltatás-oldali átvitelek alacsony erőforrás-használattal rendelkeznek a processzor és a memória, a hálózati sávszélesség és a kapcsolatok esetében.

### <a name="async-transfer-characteristics"></a>Aszinkron átvitel jellemzői

A _TransferManager. CopyDirectoryAsync ()_ metódust a _CopyMethod. ServiceSideAsyncCopy_ kapcsolóval hívhatja meg. A szinkronizálási adatátviteli mechanizmushoz hasonlóan működik az ügyfél szemszögéből, de a művelet a következő eltérésekkel rendelkezik:

- Az adatátviteli díjak sokkal lassabbak, mint az egyenértékű szinkronizálási átvitel (általában 10 MB/s vagy kevesebb).
- Az átvitel akkor is folytatódik, ha az ügyfél folyamata leáll.
- Bár az ellenőrzőpontok támogatottak, a _TransferCheckpoint_ használatával folytatott átvitel nem folytatódik az ellenőrzőpont idején, de az átvitel jelenlegi állapotában.

### <a name="test-summary"></a>Teszt összegzése

Az Azure Blob Storage a szolgáltatás-oldali szinkronizálási szolgáltatással támogatja a magas globális adatátviteli sebességet. A .NET-alkalmazások funkciójának használata egyszerű az adatátviteli függvénytár használatával. Cost Management exportálható úgy, hogy a több száz GB-nyi adat megbízható másolása egy Storage-fiókba egy óránál kevesebb idő alatt.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Microsoft Azure Storage adatátviteli függvénytár](https://github.com/Azure/azure-storage-net-data-movement) forrását.
- [Adatok átvitele az](../../storage/common/storage-use-data-movement-library.md)adatáthelyezési könyvtárral.
- Tekintse meg a [AzureDmlBackup minta alkalmazás](https://github.com/markjbrown/AzureDmlBackup) forrását.
- Olvassa el [a nagy átviteli sebességet az Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).