---
title: Azure Batch Pool-méretezési esemény
description: A Batch-készlet automatikus skálázási eseményének referenciája, amelyet a rendszer a készlet automatikus méretezésének végrehajtása után bocsát ki. A napló tartalma a készletre vonatkozó, automatikusan méretezhető képleteket és kiértékelési eredményeket jeleníti meg.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852133"
---
# <a name="pool-autoscale-event"></a>Készlet automatikus skálázása esemény

 Ezt az eseményt a rendszer a készlet automatikus skálázásának végrehajtása után bocsátja ki. A napló tartalma a készletre vonatkozó, automatikusan méretezhető képleteket és kiértékelési eredményeket jeleníti meg.

 Az alábbi példa egy készlet automatikus skálázási eseményének törzsét mutatja be egy készlet automatikus méretezése miatt, amely nem elegendő mintaadatok miatt nem sikerült.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`timestamp`|DateTime|Az automatikus skálázás végrehajtásának időbélyege.|
|`formula`|Sztring|Az automatikus skálázáshoz definiált képlet.|
|`results`|Sztring|A képletben használt összes változó kiértékelésének eredményei.|
|[`error`](#error)|Összetett típus|Az automatikus skálázás részletes hibája.|

###  <a name="error"></a><a name="error"></a> hiba

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`code`|Sztring|Az automatikus skálázási hiba azonosítója. A kódok Invariant típusúak, és programozott módon való felhasználásra készültek.|
|`message`|Sztring|Az automatikus skálázási hibát leíró üzenet, amelynek célja, hogy megfelelő legyen a felhasználói felületen való megjelenítéshez.|
|`values`|Tömb|A név-érték párok listája, amely az automatikus skálázási hiba további részleteit ismerteti.|
