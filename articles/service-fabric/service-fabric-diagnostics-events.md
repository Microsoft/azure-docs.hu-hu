---
title: Azure-Service Fabric események
description: Ismerje meg, hogy az Azure Service Fabric-fürt figyeléséhez milyen Service Fabrici események vannak megadva a mezőben.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 581bb3f16941fce660e0f464ed8ea9389b034b12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628808"
---
# <a name="service-fabric-events"></a>Service Fabric-események 

A Service Fabric platform számos strukturált eseményt ír elő a fő operatív tevékenységekhez a fürtön belül. Ez a tartomány a fürt frissítéseiről a replika elhelyezési döntéseire vonatkozik. Minden olyan esemény, amely Service Fabric a leképezéseket az alábbi entitások egyikére teszi elérhetővé a fürtben:
* Fürt
* Alkalmazás
* Szolgáltatás
* Partíció
* Replika 
* Tároló

A platform által megjelenített események teljes listájának megtekintéséhez [Service Fabric események listája](service-fabric-diagnostics-event-generation-operational.md).

Íme néhány példa olyan forgatókönyvekre, amelyeknek meg kell jelennie a fürtben lévő eseményeknek. 
* Csomópont-Életciklus eseményei: a csomópontok létrehozása, leállása, méretezése/kibontása, újraindítása és aktiválása/inaktiválása után ezek az események elérhetővé válnak, és segít azonosítani, hogy van-e valami probléma a géppel, vagy ha egy olyan API volt, amely az SF-n keresztül lett meghívva egy csomópont állapotának módosításához.
* Fürt frissítése: a fürt frissítésekor (SF-verzió vagy-konfiguráció módosítása) megtekintheti a frissítés kezdeményezését, átválthatja az egyes frissítési tartományokat, és befejezheti (vagy visszaállíthatja a visszaállítást). 
* Alkalmazás-frissítések: a fürt frissítéseihez hasonlóan az események széles körét is megtekintheti a frissítés során. Ezek az események hasznosak lehetnek a frissítés ütemezésének, a frissítés aktuális állapotának, valamint az események teljes sorrendjének a megismeréséhez. Ez akkor hasznos, ha szeretné megtekinteni, hogy a rendszer hogyan hajtotta végre a frissítéseket, vagy hogy a visszaállítást aktiválták-e.
* Alkalmazás/szolgáltatás központi telepítése/törlése: az egyes alkalmazásokhoz, szolgáltatásokhoz és tárolók létrehozásához és törléséhez, illetve a (z) vagy a rendszerbe való méretezéshez hasznos, például a replikák számának növelésére szolgáló esemény.
* Partíció áthelyezése (újrakonfigurálás): Ha egy állapot-nyilvántartó partíció egy újrakonfiguráláson (a replikakészlet változásán) keresztül halad át, egy eseményt naplóz a rendszer. Ez akkor hasznos, ha meg szeretné ismerni, hogy a partíciós replikák milyen gyakran változnak vagy feladatátvételt hajtanak végre, vagy nyomon követheti, hogy melyik csomópont futtatta az elsődleges replikát bármelyik időpontban.
* Chaos-események: Ha a Service Fabric [Chaos](service-fabric-controlled-chaos.md) szolgáltatását használja, akkor a szolgáltatás minden indításakor vagy leállításakor megjelennek az események, vagy ha hiba történik a rendszeren.
* Állapotadatok: Service Fabric állapotot tesz elérhetővé a figyelmeztetés vagy a hiba állapota jelentés létrehozásakor, vagy ha egy entitás vissza fog térni egy OK állapotba, vagy egy állapotjelentés lejár. Ezek az események nagyon hasznosak az entitások korábbi állapot-statisztikáinak nyomon követéséhez. 

## <a name="how-to-access-events"></a>Események elérése

A Service Fabric-események elérésének számos különböző módja van:
* Az események naplózása szabványos csatornákon keresztül történik (például ETW/Windows-eseménynaplók), és bármely olyan figyelési eszközzel megjeleníthető, amely támogatja ezeket, például a Azure Monitor naplókat. Alapértelmezés szerint a portálon létrehozott fürtökön be van kapcsolva a diagnosztika, és a Windows Azure Diagnostics ügynöke elküldi az eseményeket az Azure Table Storage szolgáltatásnak, de továbbra is integrálnia kell ezt a log Analytics-erőforrással. További információ a Azure Diagnostics- [ügynök](service-fabric-diagnostics-event-aggregation-wad.md) konfigurálásáról a fürt diagnosztikai konfigurációjának módosításához további naplók vagy teljesítményszámlálók és a [Azure monitor naplók integrációjának](service-fabric-diagnostics-event-analysis-oms.md) kiválasztásához
* A EventStore szolgáltatás REST API-jai, amelyek lehetővé teszik a fürt közvetlen lekérdezését vagy a Service Fabric ügyféloldali függvénytáron keresztül. Lásd: [EventStore API-k lekérdezése a fürt eseményeihez](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Következő lépések
* További információ a fürt figyeléséről – [a fürt és a platform figyelése](service-fabric-diagnostics-event-generation-infra.md).
* További információ a EventStore Service- [EventStore szolgáltatásról – áttekintés](service-fabric-diagnostics-eventstore.md)
