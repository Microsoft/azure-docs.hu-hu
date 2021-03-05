---
title: Reagálás az Azure Blob Storage-eseményekre | Microsoft Docs
description: Azure Event Grid használata a blob Storage-eseményekre való előfizetéshez és reagáláshoz. Ismerje meg az esemény modelljét, az események szűrését és az események felhasználásának gyakorlatát.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: f07c249e3b7cb54283959df410d51ca18998f2cf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181516"
---
# <a name="reacting-to-blob-storage-events"></a>Reagálás Blob Storage-eseményekre

Az Azure Storage eseményei lehetővé teszik, hogy az alkalmazások reagálni tudjon az eseményekre, például a Blobok létrehozását és törlését. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra. A legjobb rész csak a ténylegesen felhasznált funkciókért kell fizetnie.

A blob Storage-események [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) használatával lesznek leküldve, például Azure Functions, Azure Logic apps vagy akár a saját http-figyelőhöz. A Event Grid megbízható esemény-kézbesítést biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek használatával.

Tekintse meg a blob Storage- [események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) című cikket a blob Storage által támogatott események teljes listájának megtekintéséhez.

Az általános blob Storage-események közé tartozik például a képek vagy a videók feldolgozása, a keresések indexelése vagy bármilyen fájl alapú munkafolyamat. Az aszinkron fájlfeltöltés nagyszerű illeszkedést biztosít az eseményekhez. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Ha a blob Storage-eseményeket szeretné kipróbálni, tekintse meg az alábbi rövid útmutatókat:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a cikket: |
|--|-|
|Azure Portal    |[Gyors útmutató: blob Storage-események átirányítása webes végpontra a Azure Portal](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Gyors útmutató: tárolási események továbbítása webes végponthoz a PowerShell használatával](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Gyors útmutató: tárolási események továbbítása webes végponthoz az Azure CLI-vel](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Ha az Azure functions használatával szeretné megtekinteni a blob Storage-eseményekre való reagálással kapcsolatos részletes példákat, tekintse meg a következő cikkeket:

- [Oktatóanyag: Azure Data Lake Storage Gen2-események használata egy Databricks-különbözeti tábla frissítéséhez](data-lake-storage-events.md).
- [Oktatóanyag: feltöltött képek átméretezésének automatizálása Event Grid használatával](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> Csak a **StorageV2 (általános célú v2)**, a **BlockBlobStorage** és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (általános célú v1)** *nem* támogatja a Event Grid integrációját.

## <a name="the-event-model"></a>Az esemény modellje

A Event Grid [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az üzeneteket az előfizetőknek. Ez a rendszerkép az esemény-közzétevők, az esemény-előfizetések és az eseménykezelők közötti kapcsolatot mutatja be.

![Event Grid modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Először fizessen elő egy végpontot egy eseményre. Ezt követően az esemény indításakor a Event Grid szolgáltatás elküldi az eseményre vonatkozó információkat a végpontnak.

A megtekintéshez tekintse meg a [blob Storage-események sémáját](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető cikket:

> [!div class="checklist"]
> * A blob Storage-események teljes listája és az egyes események aktiválásának módja.
> * Egy példa arra, amelyet a Event Grid az egyes eseményekhez küld.
> * Az egyes kulcs-érték párok célja, amely megjelenik az adatban.

## <a name="filtering-events"></a>Események szűrése

A blob-eseményeket az esemény típusa, a tároló neve vagy a létrehozott/törölt objektum neve alapján [lehet szűrni](/cli/azure/eventgrid/event-subscription) . A Event Grid lévő szűrők megegyeznek a tárgy elejével vagy végével, hogy a megfelelő tárgyú események lépjenek az előfizetőre.

A szűrők alkalmazásával kapcsolatos további információkért lásd: [Események szűrése Event Grid](../../event-grid/how-to-filter-events.md).

A blob Storage-események tárgya a formátumot használja:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Egy Storage-fiók összes eseményének megfeleltetéséhez üresen hagyhatja a tulajdonos szűrőit.

Egy előtaggal rendelkező tárolók készletében létrehozott Blobok eseményeinek egyeztetéséhez használjon a következőhöz `subjectBeginsWith` hasonló szűrőt:

```
/blobServices/default/containers/containerprefix
```

Ha egy adott tárolóban létrehozott Blobok eseményeinek egyeztetését szeretné megfeleltetni, használjon a következőhöz `subjectBeginsWith` hasonló szűrőt:

```
/blobServices/default/containers/containername/
```

Ha egy adott tárolóban létrehozott Blobok eseményeinek egyeztetéséhez a blob nevének előtagját kell megosztania, használjon a következőhöz `subjectBeginsWith` hasonló szűrőt:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Ha egy blob-utótagot megosztó tárolóban lévő Blobok eseményeit szeretné egyeztetni, használjon olyan `subjectEndsWith` szűrőt, mint például a ". log" vagy a ". jpg". További információ: [Event Grid fogalmak](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

A blob Storage-eseményeket kezelő alkalmazásoknak néhány ajánlott eljárást kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek némi késés után is megérkeznek, a ETAG mezők használatával megismerheti, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e. A ETAG mező használatának megismeréséhez tekintse meg a [Egyidejűség kezelése a blob Storage-ban](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)című témakört.
> * Mivel az üzenetek megérkeznek a sorrendbe, a Sequencer mezőivel megismerheti az események sorrendjét egy adott objektumra vonatkozóan. A Sequencer mező egy karakterlánc-érték, amely az események logikai sorozatot jelöli az adott blob nevénél. A standard sztringek összehasonlításával megtudhatja, hogy az adott blob nevében két esemény relatív sorozatot kell-e használni.
> * A tárolási események garantálják a legalább egyszeri kézbesítést az előfizetőknek, így biztosítva, hogy az összes üzenet le legyen erősítve. Azonban a háttérbeli csomópontok és szolgáltatások közötti újrapróbálkozások, illetve az előfizetések rendelkezésre állása miatt előfordulhat, hogy duplikált üzenetek történnek. Ha többet szeretne megtudni az üzenetek kézbesítéséről, és próbálkozzon újra, tekintse meg [Event Grid üzenet kézbesítését, és próbálkozzon újra](../../event-grid/delivery-and-retry.md).
> * A blobType mező segítségével megtudhatja, hogy milyen típusú műveletek engedélyezettek a blobon, és hogy melyik ügyféloldali függvénytár-típust kell használnia a blob eléréséhez. Az érvényes értékek `BlockBlob` vagy vagy `PageBlob` . 
> * A `CloudBlockBlob` blob eléréséhez használja a és a konstruktorok URL-mezőjét `CloudAppendBlob` .
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje a `CopyBlob` , vagy a `PutBlob` `PutBlockList` `FlushWithClose` REST API hívások eseményeit. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](../../event-grid/how-to-filter-events.md).


## <a name="next-steps"></a>Következő lépések

További információ a Event Gridről és a blob Storage-események kiosztásáról:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [BLOB Storage-események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB Storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
