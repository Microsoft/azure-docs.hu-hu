---
title: Azure Batch készlet törlése kész esemény
description: A Batch-készlet törlésének befejezési eseménye. Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803731"
---
# <a name="pool-delete-complete-event"></a>Készlet törlése kész esemény

 Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.

 Az alábbi példa egy készlet törlésének befejezése eseményt mutatja.

```
{
   "id": "myPool1",
   "startTime": "2016-09-09T22:13:48.579Z",
   "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`startTime`|DateTime|A készlet törlésének időpontja.|
|`endTime`|DateTime|A készlet törlésének időpontja.|

## <a name="remarks"></a>Megjegyzések

A készlet-átméretezési művelet állapotával és hibakódokkal kapcsolatos további információkért lásd: [készlet törlése egy fiókból](/rest/api/batchservice/delete-a-pool-from-an-account).
