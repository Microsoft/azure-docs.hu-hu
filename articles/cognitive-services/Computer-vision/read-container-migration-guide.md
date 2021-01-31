---
title: Migrálás az olvasási v3. x OCR-tárolóba
titleSuffix: Azure Cognitive Services
description: Útmutató a v3 olvasási OCR-tárolóba való áttelepítéshez
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: baa91e21979022064aaf13aca6079f8d092d673e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221153"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrálás az olvasási v3. x OCR-tárolóba

Ha az OCR-tároló Computer Vision 2. verzióját használja, ebből a cikkből megtudhatja, hogyan frissítheti az alkalmazást a tároló 3. x verziójának használatára. 


## <a name="configuration-changes"></a>Konfigurációs változások

* `ReadEngineConfig:ResultExpirationPeriod` már nem támogatott. Az olvasási tároló egy beépített cron-feladattal rendelkezik, amely eltávolítja a 48 óra utáni kérelemmel kapcsolatos eredményeket és metaadatokat.
* `Cache:Redis:Configuration` már nem támogatott. A gyorsítótár nem használatos a v3. x tárolóban, ezért nem kell beállítania.

## <a name="api-changes"></a>API-változások

Az olvasási v 3.2 tároló a Computer Vision API 3. verzióját használja, és a következő végpontokkal rendelkezik:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Az alkalmazások a felhőalapú olvasási API-k 3. verziójának használatára való frissítésével kapcsolatos részletes információkért tekintse meg az [Computer Vision v3 REST API áttelepítési útmutatót](./upgrade-api-versions.md) . Ezek az adatok a tárolóra is érvényesek. Vegye figyelembe, hogy a szinkronizálási műveletek csak a tárolók esetében támogatottak.

## <a name="memory-requirements"></a>Memóriakövetelmények

A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz. Az alábbi táblázat az egyes olvasási tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

|Tároló  |Minimális | Ajánlott  |
|---------|---------|------|
|Olvasás 3,2 – előzetes verzió | 8 mag, 16 GB memória         | 8 mag, 24 GB memória |

Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `--memory` Docker Run parancs részeként használt és beállításoknak felel meg.

## <a name="storage-implementations"></a>Tárolási implementációk

>[!NOTE]
> A MongoDB már nem támogatott a tároló 3. x verziójában. Ehelyett a tárolók támogatják az Azure Storage és az offline fájlrendszerek használatát.

| Implementálás |    Szükséges futásidejű argumentum (ok) |
|---------|---------|
|Fájl szintje (alapértelmezett)    | Nincs szükség futásidejű argumentumokra. `/share` a rendszer a könyvtárat fogja használni. |
|Azure-blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Üzenetsor-megvalósítások

A tároló v3. x verziójában a RabbitMQ jelenleg nem támogatott. A támogatott háttér-implementációk a következők:

| Implementálás | Futásidejű argumentum (ok) | Rendeltetésszerű használat |
|---------|---------|-------|
| A memóriában (alapértelmezett) | Nincs szükség futásidejű argumentumokra. | Fejlesztés és tesztelés |
| Azure Queues | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Production |
| RabbitMQ    | Nem érhető el | Production |

A hozzáadott redundancia érdekében az olvasási v3. x tároló egy láthatósági időzítőt használ annak biztosítására, hogy a kérések sikeresen feldolgozhatók összeomlás esetén, ha egy többtárolós beállítás fut. 

Állítsa be az időzítőt a `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` használatával, amely azt az időpontot állítja be, hogy egy üzenet láthatatlan legyen, ha egy másik feldolgozó feldolgozza azt. Ha el szeretné kerülni a lapok redundáns feldolgozását, javasoljuk, hogy az időtúllépési időtartamot 120 másodpercre állítsa be. Az alapértelmezett érték 30 másodperc.

| Alapértelmezett érték | Javasolt érték |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Következő lépések

* A [tárolók konfigurálásának](computer-vision-resource-container-config.md) áttekintése konfigurációs beállításokhoz
* A nyomtatott és a kézírásos szöveg felismerésével kapcsolatos további információkért tekintse át [Computer Vision áttekintést](overview.md)
* A tároló által támogatott metódusokkal kapcsolatos részletekért tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* A Computer Vision funkcióival kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](FAQ.md) című témakört.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata