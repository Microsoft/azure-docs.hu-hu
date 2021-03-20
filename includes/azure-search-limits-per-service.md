---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83682609"
---
A keresési szolgáltatást a lemezterület korlátozza, vagy az indexek vagy indexelő maximális számának korlátozásával, attól függően, hogy melyik következik be először. A következő táblázat a dokumentumok tárolási korlátait tartalmazza. Az objektumok maximális korlátaihoz lásd: [erőforrások korlátozása](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Erőforrás | Ingyenes | <sup>1</sup> . alapszintű | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatói szerződés (SLA)<sup>2</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |Igen |Yes |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partíciók szolgáltatásonként |N/A |1 |12 |12 |12 |3 |12 |12 |
| Partíció mérete |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replikák |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> az alapszintű partíció egy rögzített partíciót tartalmaz. További keresési egységek is felhasználhatók a nagyobb lekérdezési kötetek replikáinak hozzáadására.

<sup>2</sup> a szolgáltatói szerződések a dedikált erőforrásokon lévő számlázható szolgáltatások esetében érvényesek. Az ingyenes szolgáltatások és az előzetes verziójú funkciók nem rendelkeznek SLA-val. Számlázható szolgáltatások esetén a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replika szükséges a lekérdezés (olvasási) SLA-hoz. Három vagy több replika szükséges a lekérdezéshez és az indexeléshez (írási/olvasási) SLA-hoz. A partíciók száma nem SLA-megfontolás. 