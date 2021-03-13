---
title: Az összes adatkészlet API beolvasása
description: Ezzel az API-val lekérheti az összes rendelkezésre álló adatkészletet a kereskedelmi piactér elemzéséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583801"
---
# <a name="get-all-datasets-api"></a>Az összes adatkészlet API beolvasása

A minden adatkészlet beolvasása API lekéri az összes rendelkezésre álló adatkészletet. Az adatkészletek felsorolják a táblákat, oszlopokat, metrikákat és időtartományokat.

**Kérelem szintaxisa**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Kérelem fejléce**

| **Fejléc** | **Típus** | **Leírás** |
| --- | --- | --- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |

**Elérési út paraméter**

Nincs

**Lekérdezési paraméter**

| **Paraméter neve** | **Típus** | **Kötelező** | **Leírás** |
| --- | --- | --- | --- |
| `datasetName` | sztring | No | Szűrés csak egy adatkészlet részleteinek lekéréséhez |

**Kérelem tartalma**

Nincs

**Szószedet**

Nincs

**Válasz**

A válasz tartalma a következőképpen van strukturálva:

Válasz kódja: 200, 400, 401, 403, 404, 500

Válasz hasznos adat például:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Szószedet**

Ez a tábla a válasz legfontosabb elemeit határozza meg:

| **Paraméter** | **Leírás** |
| --- | --- |
| `DatasetName` | Azon adatkészlet neve, amelyet ez a tömb objektum definiál |
| `SelectableColumns` | Az oszlopok kijelölése lehetőséggel megadható nyers oszlopok |
| `AvailableMetrics` | Összesítési/metrikai oszlopnevek, amelyek megadhatók az oszlopok kijelölése |
| `AvailableDateRanges` | Az adatkészlethez kapcsolódó jelentési lekérdezésekben használható dátumtartomány |
| `NextLink` | Hivatkozás a következő lapra, ha az adatlapok oldalszáma |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja A lehetséges értékek a következők 200, 400, 401, 403, 500 |
