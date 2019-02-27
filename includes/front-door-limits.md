---
title: fájl belefoglalása
description: fájl belefoglalása
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 167e774b00bcfdb7bc1e26202bd7826f525a6ace
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890402"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Bejárati ajtajának erőforrásokra előfizetésenként | 100 |
| Előtér-gazdagépek, beleértve az egyéni tartományok erőforrásonként | 100 |
| Erőforrásonként útválasztási szabályok | 100 |
| Erőforrásonként háttérkészletek | 50 |
| Háttérrendszerek háttérrendszer készletenként | 100 |
| Útválasztási szabályainak megfelelő elérésiút-minták | 25 |
| Egyéni webalkalmazási tűzfalszabályok házirend szerint | 10 |
| Webes alkalmazás-erőforrásonként tűzfalházirend | 100 |

### <a name="timeout-values"></a>Időtúllépési értékek
#### <a name="client-to-front-door"></a>Ügyfél számára a bejárati ajtó
- Bejárati ajtajának rendelkezik TCP kapcsolat üresjárati időkorlátot 61 másodpercben.

#### <a name="front-door-to-application-backend"></a>Az alkalmazás háttérrendszerével bejárati ajtajának
- Ha a válasz darabolt választ, 200-as visszaad Ha / az első adatrészletben fogadásakor.
- A HTTP-kérelem továbbíthatja a rendszer a háttérkiszolgálón, miután bejárati ajtajának 503-as hiba visszatér az ügyfél az első csomag háttérrendszerből, 30 másodperces várakozik.
- Az első csomag a háttérbeli fogadását követően bejárati ajtajának megvárja, 30 másodperces (üresjárati időkorlát) 503-as hiba visszatér az ügyfél.
- Háttérbeli TCP munkamenet időkorlátja bejárati ajtajának érték 30 perc.

### <a name="upload--download-data-limit"></a>Feltöltése / letöltése adatkorlát

|  | A darabolásos átvitel kódolási (irányuló) | HTTP-darabolás nélkül |
| ---- | ------- | ------- |
| **Letöltés** | Nincs korlátozva a letöltési mérete | Nincs korlátozva a letöltési mérete |
| **Feltöltés** |  Nincs korlátozva, amíg minden irányuló feltöltése nem 28.6 MB-nál kevesebb | A méret nem lehet 28.6 MB-nál nagyobb. |
