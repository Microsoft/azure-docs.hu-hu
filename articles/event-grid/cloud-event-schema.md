---
title: CloudEvents v 1.0 séma Azure Event Grid
description: Ismerteti, hogyan használható a CloudEvents 1.0-s séma a Azure Event Grid eseményeihez. A szolgáltatás támogatja a felhőalapú események JSON-implementációjában lévő eseményeket.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324178"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>CloudEvents v 1.0 séma Azure Event Grid

Az [alapértelmezett esemény sémáján](event-schema.md)kívül Azure Event Grid natív módon támogatja a [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) és a [http protokoll kötésének](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON-implementációjában lévő eseményeket. A [CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az események leírásához.

A CloudEvents a felhőalapú események közzétételére és felhasználására szolgáló közös esemény sémájának használatával egyszerűsíti az együttműködési képességet. Ez a séma lehetővé teszi, hogy egységes eszközöket biztosítson, és szabványos módon irányítsa át & az események kezelését, valamint a külső esemény sémájának deszerializálásának univerzális módszereit. Közös sémával könnyebben integrálhatja a munkafolyamatokat a különböző platformokon.

A CloudEvents számos [közreműködő](https://github.com/cloudevents/spec/blob/master/community/contributors.md), például a Microsoft, a [Felhőbeli natív számítástechnikai alaprendszer](https://www.cncf.io/)segítségével építhető ki. Jelenleg 1,0-as verzióként érhető el.

Ez a cikk a CloudEvents sémát ismerteti Event Gridokkal.

## <a name="sample-event-using-cloudevents-schema"></a>Példa a CloudEvents sémát használó eseményre

Íme egy példa egy Azure Blob Storage eseményre CloudEvents formátumban:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[Itt](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)találja a CloudEvents 1.0-s verziójában elérhető mezők, típusok és definíciók részletes leírását.

A CloudEvents-sémában és a Event Grid sémában leszállított események fejlécének értékei ugyanazok, kivéve a következőt: `content-type` . A CloudEvents séma esetében a fejléc értéke a következő: `"content-type":"application/cloudevents+json; charset=utf-8"` . Event Grid séma esetében a fejléc értéke a következő: `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>CloudEvents Event Grid

A CloudEvents-sémában lévő események bemenetéhez és kimenetéhez Event Grid is használhatja. A CloudEvents-t használhatja rendszereseményekhez, például Blob Storage eseményekhez és IoT Hub eseményekhez és egyéni eseményekhez. Emellett átalakíthatja ezeket az eseményeket a huzalon oda-vissza.


| Bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátuma | CloudEvents formátuma
| Event Grid formátum  | CloudEvents formátuma
| Event Grid formátum  | Event Grid formátum

Az összes esemény sémája esetében a Event Grid érvényesítést igényel az Event Grid-témakörre való közzétételkor és az esemény-előfizetés létrehozásakor. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

## <a name="next-steps"></a>Következő lépések
Lásd: [a CloudEvents 1.0-s verziójának használata Event Grid használatával](cloudevents-schema.md).  
