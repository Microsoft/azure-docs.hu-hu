---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1dd78ba3799573e05e4ebbf55887bae3d9674b7c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310077"
---
Event Hubs rendezi az esemény-hubhoz eljuttatott események egy vagy több partícióba való rendezését. Ahogy újabb események érkeznek, a rendszer hozzáadja a sorozatot a végéhez. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

A partíciókat "véglegesítő naplóként" lehet meggondolni. A partíciók az esemény törzsét tartalmazó eseményeket, a felhasználó által definiált, az eseményt leíró, a metaadatokat, például a partíción belüli eltolást, az adatfolyam-sorozatot, valamint a szolgáltatás-oldali időbélyeget, amelyen elfogadták.

![Diagram, amely megjeleníti a régebbi és újabb események eseménysorozat.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>A partíciók használatának előnyei
Event Hubs úgy lett kialakítva, hogy segítse a nagy mennyiségű esemény feldolgozását, és a particionálás két módon segít:

- Annak ellenére, hogy Event Hubs egy Pásti szolgáltatás, van egy fizikai valóság, és az események sorrendjét megőrző napló fenntartása megköveteli, hogy ezeket az eseményeket a mögöttes tárolóban és a replikákban is megőrizze, ami egy ilyen napló teljesítményének felső határát eredményezi. A particionálás lehetővé teszi, hogy több párhuzamos naplót lehessen használni ugyanahhoz az Event hub-hoz, és így megszorozza a rendelkezésre álló nyers IO-átviteli kapacitást.
- A saját alkalmazásainak képesnek kell lenniük az Event hub-ba küldött események mennyiségének feldolgozására. Összetett lehet, és jelentős, kibővített, párhuzamos feldolgozási kapacitást igényel. Az események kezelésére szolgáló egyetlen folyamat kapacitása korlátozott, így több folyamatra van szükség. A partíciók azt jelentik, hogy a megoldás hogyan táplálja ezeket a folyamatokat, és gondoskodik arról, hogy minden eseménynek legyen egyértelmű feldolgozó tulajdonosa. 

### <a name="number-of-partitions"></a>Partíciók száma
A partíciók száma a létrehozáskor van megadva, és Event Hubs standardban 1 és 32 között kell lennie. A partíciók száma a dedikált Event Hubs kapacitás egységenként legfeljebb 2000 partíció lehet. 

Azt javasoljuk, hogy legalább annyi partíciót válasszon ki, amennyit várt, hogy az adott Event hub esetében az alkalmazás maximális terhelése alatt szükséges legyen a tartós [átviteli egységek (TU)](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-) . Egyetlen partícióval kell kiszámítani, amelynek kapacitása 1 TU (1 MB, 2 MB kimenő). A névtér vagy a fürt kapacitási egységei a partíciók számától függetlenül méretezhetők. Egy 32 partícióval rendelkező Event hub vagy 1 partícióval rendelkező Event hub esetében ugyanaz a költség, ha a névtér 1 TU kapacitásra van beállítva. 

Egy [dedikált Event Hubs-fürtben](../articles/event-hubs/event-hubs-dedicated-overview.md) lévő Event hub partícióinak száma [megnövelhető](../articles/event-hubs/dynamically-add-partitions.md) az Event hub létrehozása után, de a partíciók közötti elosztás a partíciók közötti eloszlásban változik, ha a partíciós kulcsok a partíciók változásaihoz való leképezése történik, ezért nehéz elkerülni az ilyen módosításokat, ha az adott alkalmazásban az események relatív sorrendje is fennáll.

A maximálisan megengedett értékhez tartozó partíciók számának beállítása csábító, de mindig vegye figyelembe, hogy az esemény-adatfolyamokat strukturálni kell, hogy valóban több partíciót is kihasználhassanak. Ha az összes esemény vagy csak néhány ALStream esetében abszolút megrendelés-megőrzésre van szüksége, akkor előfordulhat, hogy nem tudja kihasználni a sok partíciót. Emellett sok partíció is összetettebbvé teszi a feldolgozási oldalt. 


### <a name="mapping-of-events-to-partitions"></a>Az események partícióhoz való hozzárendelése
A partíciókulccsal a beérkező eseményadatok leképezhetők adott partíciókra az adatok elrendezése céljából. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.

A partíciós kulcs megadásával a kapcsolódó események együtt ugyanabban a partícióban és pontosan abban a sorrendben jelennek meg, amelyben elküldték őket. A partíciós kulcs egy olyan karakterlánc, amely az alkalmazási környezetből származik, és az események kapcsolatát azonosítja. Egy partíciós kulcs által azonosított események egy része egy *adatfolyam*. A partíciók több ilyen streamhez készült, multiplexes naplófájlok. 

> [!NOTE]
> Habár az eseményeket közvetlenül a partíciók számára is elküldheti, nem ajánlott, különösen akkor, ha a magas rendelkezésre állás fontos Önnek. Az Event hub rendelkezésre állását a partíció szintjére értékeli. További információ: [rendelkezésre állás és konzisztencia](../articles/event-hubs/event-hubs-availability-and-consistency.md).

