---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: a7e34f077ce1b2541168df40f2806fdb24a63a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050755"
---
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API-ra való áttéréskor:

|Szolgáltatás | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek. | Egy számjegyű ezredmásodperces késés az olvasás/írás műveleteknél – az olvasási műveleteknél 10 ms alatti, az írási műveleteknél 15 ms alatti késés garantált az esetek 99%-ában bármilyen méret esetén, bárhol a világon. |
| Teljesítmény | Változó teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet. | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](../articles/cosmos-db/request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókok nem rendelkeznek felső korláttal az átviteli sebesség és a támogatás >10 000 000 művelet/mp (kiépített átviteli módban). |
| Globális terjesztés | Egyetlen régió egyetlen választható olvasható, másodlagos olvasási régióval a magas szintű rendelkezésre állásért. Nem kezdeményezhető feladatátvétel. | [Kulcsrakész globális terjesztés](../articles/cosmos-db/distribute-data-globally.md) legalább 1–30 régióból. Támogatja az [automatikus és manuális feladatátvételt](../articles/cosmos-db/high-availability.md) bármikor és bárhol a világon. |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek. | Az összes tulajdonság automatikus és teljes indexelése, nincs indexkezelés. |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban. | [Öt jól meghatározott konzisztencia-szint](../articles/cosmos-db/consistency-levels.md) a rendelkezésre állás, a késés, az átviteli sebesség és a konzisztencia kikapcsolásához az alkalmazás igényei alapján. |
| Díjszabás | Fogyasztás-alapú. | Elérhető mind a [fogyasztáson alapuló](../articles/cosmos-db/serverless.md) , mind a [kiosztott kapacitási](../articles/cosmos-db/set-throughput.md) módokban. |
| SLA-k | 99,99%-os rendelkezésre állás. | 99,99%-os rendelkezésre állású SLA minden enyhén korlátozott konzisztenciájú egyrégiós és többrégiós fiókhoz, valamint 99,999%-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz [Iparágvezető és átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/) az általános rendelkezésre állásra vonatkozóan. |
