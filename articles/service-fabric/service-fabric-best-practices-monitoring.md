---
title: Ajánlott eljárások figyelése az Azure Service Fabric |} A Microsoft Docs
description: Ajánlott eljárások a Service Fabric-fürtök és alkalmazások figyelését.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d90daaf18e5161053e00671b7667d05ec8e5db76
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533809"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

[A monitoring and diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) különösen fontos fejlesztése, tesztelése és bármilyen felhőalapú környezetben a számítási feladatok üzembe helyezése. Ha például nyomon követheti az alkalmazások használata, a Service Fabric platformot, az erőforrások kihasználtságát, a teljesítményszámlálók és az általános állapotát, a fürt által végrehajtott műveletek. Ezen információk használatával diagnosztizálása és a problémák megoldása és megelőzheti azok bekövetkezését a jövőben.

## <a name="application-monitoring"></a>Alkalmazások monitorozása

Az alkalmazásfigyelés követi nyomon, milyen szolgáltatásokat és összetevőket az alkalmazás használ. Ellenőrizze, hogy a felhasználókat is érinthetnék problémákat észlelt alkalmazások figyelésére. Az alkalmazásfigyelés feladata, azokat az alkalmazás és a hozzá tartozó szolgáltatások fejlesztéséhez, mert az alkalmazás üzleti logikája számára egyedi. Javasoljuk, hogy beállította-e az alkalmazásfigyelés [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), az Azure-alkalmazás figyelése az eszközt.

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A Service Fabric célok egyike, hogy alkalmazásokat hardverhibák rugalmas. A cél a platform system szolgáltatások képességére infrastruktúra problémákat, és gyors feladatátvételi számítási feladatok más csomópontokhoz a fürtben keresztül érhető el. De mi történik, ha a rendszer szolgáltatások problémákba? Vagy ha az próbál telepíteni, vagy helyezze át a számítási feladatok, a szolgáltatások elhelyezését szabályainak megsértette vannak? A Service Fabric ezek a diagnosztikai és egyéb problémákról, ellenőrizze, hogy a Service Fabric platformot és az alkalmazások, szolgáltatások, tárolók és csomópontok együttműködését kapcsolatos Centeren biztosít.

A Windows-fürtök esetén javasoljuk, hogy beállította-e a fürtfigyelést [Diagnosztikaiügynök](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) és [naplózza az Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Linux-fürtöket, az Azure Monitor naplóira akkor is az Azure-platform és infrastruktúra-figyelési ajánlott eszköz. Linux platform diagnosztikai igényelnek, amint a különböző konfigurációs [Linux-alapú Service Fabric cluster eseményeket a Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastruktúrák monitorozása

[Az Azure Monitor naplóira](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) fürt események figyelemmel kísérésére ajánlott. Miután konfigurál a Log Analytics-ügynököket a munkaterülethez fenti hivatkozáson ismertetett módon, azt fogja tudni teljesítménymutatókat, például CPU-kihasználtság, például a folyamat szintű CPU-kihasználtság, Service Fabric teljesítmény .NET teljesítményszámlálók gyűjtése számlálók például egy reliable Services és a tároló mérőszámokat, például a CPU-kihasználtság alóli kivételek száma.  Szüksége lesz, hogy az Azure Monitor naplóira elérhető lesz az stdout és stderr tárolónaplók írni.

## <a name="watchdogs"></a>Watchdogs

Általában egy figyelő szolgáltatása egy külön, állapotát és a terhelés figyeli a szolgáltatás, Pingeli a végpontok és a fürt váratlan állapotesemények jelentéseket. Ez megakadályozhatja a hibákat, amelyek nem ismeri alapján csak egyetlen szolgáltatás teljesítményét. Watchdogs egyúttal nagyszerű hely az állomás kódot, amely végrehajtja a javító műveleteket, például bizonyos időközönként törlése a naplófájlokat tároló felhasználói beavatkozást nem igénylő. Tekintse meg a minta megvalósítása figyelő a [Linux-alapú Service Fabric cluster eseményeket a Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>További lépések

* Ismerkedés az alkalmazások szándékkal: [Alkalmazás szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-app.md).
* Lépkedjen végig a lépéseket az alkalmazás az Application Insights beállítása [figyelése és diagnosztizálása a Service Fabric ASP.NET Core alkalmazás](service-fabric-tutorial-monitoring-aspnet.md).
* További információ a platform és a Service Fabric biztosít az Ön számára események figyelése: [Platform szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-infra.md).
* Az Azure Monitor naplók integráció konfigurálása a Service Fabric segítségével: [Állítsa be a fürt az Azure Monitor naplóira](service-fabric-diagnostics-oms-setup.md)
* Ismerje meg, hogyan állítható be az Azure Monitor naplóira tárolók: [Monitorozás és diagnosztika a Windows-tárolókhoz az Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Példa diagnostics-problémák és megoldások a Service Fabric lásd: [gyakori helyzetek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* További információ az Azure-erőforrások általános figyelési javaslatok: [Ajánlott eljárások – figyelési és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).