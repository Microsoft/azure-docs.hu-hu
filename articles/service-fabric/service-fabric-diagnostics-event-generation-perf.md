---
title: Azure Service Fabric Teljesítményfigyelés
description: Az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához szükséges teljesítményszámlálók ismertetése.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75464700"
---
# <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A metrikákat össze kell gyűjteni a fürt teljesítményének és a rajta futó alkalmazásoknak a megismeréséhez. Service Fabric-fürtök esetében ajánlott a következő teljesítményszámlálók összegyűjtése.

## <a name="nodes"></a>Csomópontok

A fürtben lévő gépek esetében érdemes összegyűjteni a következő teljesítményszámlálók adatait, hogy jobban megértsék az egyes gépek terhelését, és a megfelelő fürtözött méretezési döntéseket hozzanak.

| Számláló kategóriája | Számláló neve |
| --- | --- |
| Logikai lemez | Logikai lemez – szabad terület |
| Fizikai lemez (lemezenként) | Lemez átlagos olvasási várólistájának hossza |
| Fizikai lemez (lemezenként) | Lemez átlagos írási várólistájának hossza |
| Fizikai lemez (lemezenként) | Átlagos írási idő (mp/olvasás) |
| Fizikai lemez (lemezenként) | Átlagos írási idő (mp/írás) |
| Fizikai lemez (lemezenként) | Olvasási sebesség (lemez/mp) |
| Fizikai lemez (lemezenként) | Lemez olvasási sebessége (bájt/s) |
| Fizikai lemez (lemezenként) | Írási sebesség (írás/mp) |
| Fizikai lemez (lemezenként) | Lemez írási sebessége (bájt/s) |
| Memória | Rendelkezésre álló memória (megabájt) |
| PagingFile | %-Os használat |
| Processzor (összesen) | A processzor kihasználtsága (%) |
| Folyamat (szolgáltatásként) | A processzor kihasználtsága (%) |
| Folyamat (szolgáltatásként) | AZONOSÍTÓ folyamat |
| Folyamat (szolgáltatásként) | Saját bájtok |
| Folyamat (szolgáltatásként) | Szálak száma |
| Folyamat (szolgáltatásként) | Felhasznált virtuális memória jelenlegi mérete (bájt) |
| Folyamat (szolgáltatásként) | Munkakészlet |
| Folyamat (szolgáltatásként) | Munkakészlet – privát |
| Hálózati adapter (összes példány) | Recd bájtok száma |
| Hálózati adapter (összes példány) | Küldött bájtok |
| Hálózati adapter (összes példány) | Bájtok összesen |
| Hálózati adapter (összes példány) | Kimeneti várólista hossza |
| Hálózati adapter (összes példány) | Kimenő csomagok elvetve |
| Hálózati adapter (összes példány) | Fogadott csomagok elvetve |
| Hálózati adapter (összes példány) | Kimenő hibák a csomagoknál |
| Hálózati adapter (összes példány) | Fogadott csomagok hibái |

## <a name="net-applications-and-services"></a>.NET-alkalmazások és-szolgáltatások

Gyűjtsön a következő számlálókat, ha .NET-szolgáltatásokat helyez üzembe a fürtön. 

| Számláló kategóriája | Számláló neve |
| --- | --- |
| .NET CLR memória (szolgáltatás) | Folyamatazonosító |
| .NET CLR memória (szolgáltatás) | Összes véglegesített bájt összesen |
| .NET CLR memória (szolgáltatás) | Összesen lefoglalt bájtok száma |
| .NET CLR memória (szolgáltatás) | Bájtok száma az összes halomban |
| .NET CLR memória (szolgáltatás) | Nagyméretű objektum halom mérete |
| .NET CLR memória (szolgáltatás) | # GC-kezelők |
| .NET CLR memória (szolgáltatás) | 0. generációs gyűjtemények száma |
| .NET CLR memória (szolgáltatás) | 1. generációs gyűjtemények száma |
| .NET CLR memória (szolgáltatás) | 2. generációs gyűjtemények száma |
| .NET CLR memória (szolgáltatás) | %-Os idő a GC-ben |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric egyéni teljesítményszámlálók

Service Fabric jelentős mennyiségű egyéni teljesítményszámlálókat generál. Ha telepítette az SDK-t, a Teljesítményfigyelő alkalmazásban megtekintheti a Windows rendszerű számítógép átfogó listáját (indítsa el > Teljesítményfigyelőt). 

A fürtön üzembe helyezett alkalmazásokban, ha Reliable Actors használ, adja hozzá a számlálókat `Service Fabric Actor` és a `Service Fabric Actor Method` kategóriákat (lásd: [Service Fabric Reliable Actors diagnosztika](service-fabric-reliable-actors-diagnostics.md)).

Ha Reliable Services vagy szolgáltatás-távelérést használ, `Service Fabric Service` `Service Fabric Service Method` a következőhöz hasonló módon kell összegyűjtenie a számlálókat: [figyelés a szolgáltatás távelérési szolgáltatásával](service-fabric-reliable-serviceremoting-diagnostics.md) és a [megbízható szolgáltatások teljesítményszámlálói](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Ha megbízható gyűjteményeket használ, javasoljuk, hogy vegye `Avg. Transaction ms/Commit` fel a-t a `Service Fabric Transactional Replicator` használatával a tranzakciós metrikák átlagos végrehajtási késésének összegyűjtéséhez.


## <a name="next-steps"></a>Következő lépések

* További információ [az események létrehozásáról a platform szintjén](service-fabric-diagnostics-event-generation-infra.md) Service Fabric
* Teljesítmény-metrikák összegyűjtése [log Analytics ügynökön](service-fabric-diagnostics-oms-agent.md) keresztül
