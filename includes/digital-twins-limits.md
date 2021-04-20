---
author: baanders
description: fájlba foglalhatja a Azure Digital Twins korlátokat
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728020"
---
### <a name="functional-limits"></a>Működési korlátok

Az alábbi táblázat a tartomány működési korlátait Azure Digital Twins. 

> [!TIP]
> A funkcionális korlátokon belüli működésre vonatkozó modellezési javaslatokért tekintse meg a [modellek tervezésének ajánlott eljárásait.](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)

| Terület | Képesség | Alapértelmezett korlát | Állítható? |
| --- | --- | --- | --- |
| Azure-erőforrás | Egy Azure Digital Twins példányainak száma előfizetésenként | 10 | Yes |
| Digital Twins | Ikerpéldányok száma Azure Digital Twins példányban | 200,000 | Yes |
| Digital Twins | Egy ikereszköz bejövő kapcsolatainak száma | 5000 | No |
| Digital Twins | Egy ikerből kimenő kapcsolatok száma | 5000 | No |
| Digital Twins | Egyetlen ikereszköz maximális mérete (JSON-törzs egy PUT- vagy PATCH-kérelemben) | 32 KB | No |
| Digital Twins | A kérelem hasznos fájlméretének maximális mérete | 32 KB | No | 
| Útválasztás | Egyetlen példány végpontjainak Azure Digital Twins száma | 6 | No |
| Útválasztás | Egyetlen példány útvonalának Azure Digital Twins száma | 6 | Yes |
| Modellek | Modellek száma egy adott Azure Digital Twins példányban | 10,000 | Yes |
| Modellek | Az egyetlen API-hívásba feltölthetők modellek száma | 250 | No |
| Modellek | Egyetlen modell maximális mérete (JSON-törzs egy PUT- vagy PATCH-kérelemben) | 1 MB | No |
| Modellek | Az egy oldalon visszaadott elemek száma | 100 | No |
| Lekérdezés | Az egy oldalon visszaadott elemek száma | 100 | Igen |
| Lekérdezés | Kifejezések `AND`  /  `OR` száma egy lekérdezésben | 50 | Igen |
| Lekérdezés | Tömbelemek száma egy `IN`  /  `NOT IN` záradékban | 50 | Igen |
| Lekérdezés | Karakterek száma egy lekérdezésben | 8,000 | Igen |
| Lekérdezés | Száma `JOINS` egy lekérdezésben | 5 | Yes |

### <a name="rate-limits"></a>Sebességkorlátok

Az alábbi táblázat a különböző API-k sebességkorlátját tükrözi.

| API | Képesség | Alapértelmezett korlát | Állítható? |
| --- | --- | --- | --- |
| Models API | Kérelmek száma másodpercenként | 100 | Yes |
| Digital Twins API | Olvasási kérelmek másodpercenkénti száma | 1,000 | Yes |
| Digital Twins API | Javítási kérelmek másodpercenkénti száma | 1,000 | Yes |
| Digital Twins API | Létrehozási/törlési műveletek másodpercenkénti száma az összes **ikereszköz és kapcsolat között** | 50 | Yes |
| Digital Twins API | Létrehozási/frissítési/törlési műveletek másodpercenkénti száma egy ikeren vagy **annak** kapcsolatain | 10 | No |
| Lekérdezési API | Kérelmek száma másodpercenként | 500 | Yes |
| Lekérdezési API | [Lekérdezési egységek](../articles/digital-twins/concepts-query-units.md) száma másodpercenként | 4,000 | Yes |
| Event Routes API | Kérelmek száma másodpercenként | 100 | Yes |

### <a name="other-limits"></a>Egyéb korlátok

A DTDL-dokumentumokban az Azure Digital Twins-modellek adattípusára és mezőire vonatkozó korlátozásokat a GitHub specifikációs dokumentációjában találja: [*Digital Twins Definition Language (DTDL) – 2-es verzió.*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)
 
A lekérdezések késésével kapcsolatos részleteket és egyéb lekérdezési korlátozásokat az ikergráf lekérdezését bemutató bemutató [*oldalon talál.*](../articles/digital-twins/how-to-query-graph.md)
