---
title: Replikák és példányok az Azure Service Fabric
description: Ismerje meg Service Fabric replikáit és példányait, beleértve az életciklusuk és a függvények áttekintését.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710745"
---
# <a name="replicas-and-instances"></a>Replikák és példányok 
Ez a cikk áttekintést nyújt az állapot-nyilvántartó szolgáltatások replikáinak életciklusáról és az állapot nélküli szolgáltatások példányairól.

## <a name="instances-of-stateless-services"></a>Állapot nélküli szolgáltatások példányai
Az állapot nélküli szolgáltatás egy példánya a fürt egyik csomópontján futó szolgáltatás-logika másolata. A partíción belüli példányokat a **InstanceId** egyedileg azonosítja. Egy példány életciklusa a következő ábrán látható:

![Példány életciklusa](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Inbuild (IB)
Miután a fürterőforrás-kezelő meghatározta a példány elhelyezését, ez az életciklus-állapotba kerül. A példány elindult a csomóponton. Az alkalmazás-gazdagép elindult, a példány létrejön, majd megnyílik. Az indítást követően a példány üzemkész állapotba vált. 

Ha a példányhoz tartozó alkalmazás-gazdagép vagy-csomópont összeomlik, az eldobott állapotba vált.

### <a name="ready-rd"></a>Kész (RD)
A Ready (kész) állapotú példány a csomóponton működik. Ha ez a példány megbízható szolgáltatás, a **RunAsync** meghívása megtörtént. 

Ha a példányhoz tartozó alkalmazás-gazdagép vagy-csomópont összeomlik, az eldobott állapotba vált.

### <a name="closing-cl"></a>Bezárás (CL)
A záró állapotban az Azure Service Fabric folyamatban van a példány leállítása a csomóponton. A Leállítás oka számos ok lehet, például egy alkalmazás frissítése, terheléselosztás vagy a törölt szolgáltatás. A Leállítás befejezése után a rendszer eldobott állapotba vált.

### <a name="dropped-dd"></a>Elvetve (DD)
Az eldobott állapotban a példány már nem fut a csomóponton. Ezen a ponton Service Fabric megtartja a példány metaadatait, ami végül is törlődik.

> [!NOTE]
> A **ForceRemove** kapcsoló használatával bármely állapotból eldobott állapotba lehet áttérni `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Állapot-nyilvántartó szolgáltatások replikái
Az állapot-nyilvántartó szolgáltatás replikája a fürt egyik csomópontján futó szolgáltatás logikájának másolata. Emellett a replika megőrzi a szolgáltatás állapotának másolatát. Két kapcsolódó fogalom ismerteti az állapot-nyilvántartó replikák életciklusát és viselkedését:
- Replika életciklusa
- Replika szerepkör

A következő vitafórum a megőrzött állapot-nyilvántartó szolgáltatásokat ismerteti. Az illékony (vagy memóriában lévő) állapot-nyilvántartó szolgáltatások esetében a lefelé és az eldobott állapotok egyenértékűek.

![Replika életciklusa](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Inbuild (IB)
A kiépített replika olyan replika, amely a replikakészlet összekapcsolására lett létrehozva vagy felkészülve. A replika szerepkörtől függően az IB különböző szemantikai beállításokkal rendelkezik. 

Ha az alkalmazás gazdagépe vagy egy kiépített replika csomópontja összeomlik, a rendszer a lefelé irányuló állapotba vált.

   - **Elsődleges inbuild replikák**: az elsődleges inbuild a partíciók első replikái. Ez a replika általában a partíció létrehozásakor fordul elő. Az elsődleges inbuild replikák akkor is felmerülhetnek, ha egy partíció összes replikája újraindul vagy el van dobva.

   - **IdleSecondary inbuild replikái**: ezek a fürterőforrás-kezelő által létrehozott új replikák, vagy a már meglévő replikák, amelyeket vissza kell adni a készletbe. Ezek a replikák az elsődlegesek szerint vannak kialakítva vagy létrehozva, mielőtt csatlakozni tudnak a másodpéldányhoz a ActiveSecondary, és részt vesznek a műveletek Kvórumának visszaigazolásában.

   - **ActiveSecondary inbuild replikák**: ez az állapot bizonyos lekérdezésekben figyelhető meg. Ez egy olyan optimalizálás, amelyben a replikakészlet nem változik, de a replikát létre kell venni. Maga a replika követi a normál állapotú gépekre való áttérést (a replika szerepköreinek című szakaszban leírtak szerint).

### <a name="ready-rd"></a>Kész (RD)
A kész replika olyan replika, amely részt vesz a replikációban és a kvórum-visszaigazolási műveletekben. A Ready (kész) állapot az elsődleges és az aktív másodlagos replikára vonatkozik.

Ha az alkalmazás gazdagépe vagy egy kész replika csomópontja összeomlik, a rendszer a lefelé irányuló állapotba vált.

### <a name="closing-cl"></a>Bezárás (CL)
A replika a következő helyzetekben lép be a záró állapotba:

- A **replika kódjának leállítása**: Service Fabric lehet, hogy le kell állítania egy replika futtatási kódját. Előfordulhat, hogy ez a Leállítás számos okból kifolyólag lehetséges. Előfordulhat például, hogy egy alkalmazás, háló vagy infrastruktúra-frissítés miatt vagy a replika által jelentett hiba miatt fordulhat elő. A replika bezárásának befejeződése után a replika a leállási állapotba kerül. A lemezen tárolt replikához társított megőrzött állapot nem törlődik.

- **A replika eltávolítása a fürtből**: Service Fabric lehet, hogy el kell távolítania a megőrzött állapotot, és le kell állítania a futó kódot egy replikához. A Leállítás több okból is előfordulhat, például a terheléselosztás.

### <a name="dropped-dd"></a>Elvetve (DD)
Az eldobott állapotban a példány már nem fut a csomóponton. A csomóponton még nem maradt állapot. Ezen a ponton Service Fabric megtartja a példány metaadatait, ami végül is törlődik.

### <a name="down-d"></a>Lefelé (D)
A leállási állapotban a replika kódja nem fut, de a replika megőrzött állapota létezik ezen a csomóponton. A replikák számos okból leállíthatók – például az éppen leállt csomópont, egy összeomlás a replika kódjában, az alkalmazás frissítése vagy a replika hibái.

A lekéréses replikát szükség szerint Service Fabric megnyitva, például amikor a frissítés befejeződik a csomóponton.

A replika szerepkör nem releváns a leállási állapotban.

### <a name="opening-op"></a>Megnyitás (OP)
A lekéréses replika a nyitó állapotba kerül, amikor Service Fabric újra vissza kell állítania a replikát. Ez az állapot például akkor lehet, ha az alkalmazáshoz tartozó kód frissítése egy csomóponton fejeződik be. 

Ha az alkalmazás gazdagépe vagy egy nyitó replika csomópontja összeomlik, a rendszer a lefelé irányuló állapotba vált.

A replika szerepkör nem vonatkozik a megnyitási állapotra.

### <a name="standby-sb"></a>Készenlét (SB)
A készenléti replika egy megőrzött szolgáltatás replikája, amely leállt, és ekkor megnyitották. Ezt a replikát a Service Fabric is használhatja, ha hozzá kell adnia egy másik replikát a kópiakészlet számára (mivel a replika már rendelkezik az állapot egy részével, és a létrehozási folyamat gyorsabb). A StandByReplicaKeepDuration lejárta után a rendszer elveti a készenléti replikát.

Ha az alkalmazás gazdagépe vagy egy készenléti replika csomópontja összeomlik, a rendszer a lefelé irányuló állapotba vált.

A replika szerepkör nem vonatkozik a készenléti állapotra.

> [!NOTE]
> A rendszer a nem lefelé vagy eldobott replikákat *felszámítja.*
>

> [!NOTE]
> A **ForceRemove** kapcsoló használatával bármely állapotból eldobott állapotba lehet áttérni `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Replika szerepkör 
A replika szerepköre határozza meg a függvényt a másodpéldányban:

- **Elsődleges (P)**: az olvasási és írási műveletek végrehajtásáért felelős replikakészlet egyik elsődleges értéke. 
- **ActiveSecondary (ok)**: ezek olyan replikák, amelyek az elsődlegestől az állapot-frissítéseket fogadják, majd visszaküldik a visszaigazolásokat. A kópiakészlet több aktív formátumú másodlagos zónák is rendelkezik. Ezen aktív formátumú másodlagos zónák száma határozza meg a szolgáltatás által kezelhető hibák számát.
- **IdleSecondary (I)**: ezeket a replikákat az elsődlegesen építették. A rendszer az elsődlegestől az aktív másodlagoshoz való előléptetés előtt fogadja az állapotot. 
- **Nincs (N)**: ezek a replikák nem rendelkeznek felelősséggel a kópiakészlet-készletben.
- **Ismeretlen (U)**: Ez a replika kezdeti szerepe, mielőtt **changerole művelet** API-hívást kap a Service Fabricból.

A következő ábra szemlélteti a replika szerepkör átváltását, és néhány példát arra, hogy mely esetekben fordulhat elő:

![Replika szerepkör](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: új elsődleges replika létrehozása.
- U-> I: új tétlen replika létrehozása.
- U-> N: készenléti replika törlése.
- I-> S: a másodlagos üresjárat előléptetése aktív másodlagosra, hogy a nyugták hozzájáruljanak a kvórumhoz.
- I-> P: a másodlagos üresjárat előléptetése elsődlegesre. Ez olyan speciális újrakonfigurálások esetén fordulhat elő, amikor az üresjárati másodlagos a megfelelő jelölt elsődlegesnek.
- I-> N: az üresjárati másodlagos replika törlése.
- S-> P: az aktív másodlagos előléptetése elsődlegesre. Ennek oka lehet a fürterőforrás-kezelő által kezdeményezett elsődleges vagy elsődleges mozgás feladatátvétele. Előfordulhat például, hogy az alkalmazás frissítésére vagy terheléselosztásra válaszol.
- S-> N: az aktív másodlagos replika törlése.
- P-> S: az elsődleges replika lefokozása. Ennek oka lehet a fürterőforrás-kezelő által kezdeményezett elsődleges mozgás. Előfordulhat például, hogy az alkalmazás frissítésére vagy terheléselosztásra válaszol.
- P-> N: az elsődleges replika törlése.

> [!NOTE]
> A magasabb szintű programozási modellek, mint például a [Reliable Actors](service-fabric-reliable-actors-introduction.md) és a [Reliable Services](service-fabric-reliable-services-introduction.md), elrejtik a replikációs szerepkörök fogalmát a fejlesztőtől. A szereplők esetében a szerepkör fogalma szükségtelen. A szolgáltatások részben egyszerűbb a legtöbb forgatókönyv esetében.
>

## <a name="next-steps"></a>Következő lépések
Service Fabric fogalmakkal kapcsolatos további információkért tekintse meg a következő cikket:

[A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)

