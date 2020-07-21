---
title: Azure Service Fabric monitorozás – ajánlott eljárások
description: Ajánlott eljárások és tervezési szempontok a fürtök és alkalmazások Azure Service Fabric használatával történő figyeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529250"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Az Azure Service Fabric monitorozási és diagnosztikai eljárásai

A [monitorozás és a diagnosztika](./service-fabric-diagnostics-overview.md) kritikus fontosságú a számítási feladatok bármilyen felhőalapú környezetben való fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. Például nyomon követheti az alkalmazások felhasználásának módját, a Service Fabric platform által végrehajtott műveleteket, az erőforrások kihasználtságát a teljesítményszámlálók segítségével, valamint a fürt általános állapotát. Ezeket az információkat felhasználhatja a problémák diagnosztizálásához és kijavításához, és megakadályozhatja, hogy a későbbiekben is megtörténjenek.

## <a name="application-monitoring"></a>Alkalmazások monitorozása

Az alkalmazás figyelése nyomon követi, hogy az alkalmazás mely szolgáltatásait és összetevőit használja. Figyelje az alkalmazásokat, és győződjön meg arról, hogy a felhasználók által érintett problémák merülnek fel. Az alkalmazások figyelése az alkalmazás és a szolgáltatásai fejlesztésének felelőssége, mivel az alkalmazás üzleti logikája egyedi. Javasoljuk, hogy az alkalmazás figyelését [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), az Azure alkalmazás-figyelési eszközével állítsa be.

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A Service Fabric egyik célja, hogy az alkalmazások a hardverhiba miatt rugalmasak legyenek. Ez a cél a platform rendszerszolgáltatásai által a fürt más csomópontjaira történő észlelési képességgel és a gyors feladatátvételsel kapcsolatos lehetőségekkel érhető el. Mi a teendő, ha a rendszerszolgáltatások problémái vannak? Vagy ha a számítási feladatok üzembe helyezésére vagy áthelyezésére tett kísérlet során megsértették a szolgáltatások elhelyezésére vonatkozó szabályokat? A Service Fabric diagnosztikát biztosít ezekhez és egyéb problémákhoz, hogy megbizonyosodjon arról, hogy a Service Fabric platform hogyan kommunikál az alkalmazásokkal, szolgáltatásokkal, tárolókkal és csomópontokkal.

Windows-fürtök esetén ajánlott beállítani a fürt figyelését [diagnosztikai ügynökkel](./service-fabric-diagnostics-event-aggregation-wad.md) és [Azure monitor naplókkal](./service-fabric-diagnostics-oms-setup.md).

Linux-fürtök esetében az Azure platform és az infrastruktúra figyelésére szolgáló Azure Monitor naplók is az ajánlott eszköz. A Linux platform diagnosztika különböző konfigurációt igényel, mint [Service Fabric Linux-fürt eseményei a syslog-ben](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Infrastruktúrák monitorozása

A fürt szintű események figyeléséhez [Azure monitor naplók](./service-fabric-diagnostics-oms-agent.md) használata javasolt. Miután konfigurálta a Log Analytics-ügynököt a munkaterülettel az előző hivatkozásban leírtak szerint, olyan teljesítménymutatókat gyűjthet, mint például a CPU-kihasználtság, a .NET-teljesítményszámlálók, például a processzor kihasználtsági szintje, Service Fabric a teljesítményszámlálók, például a megbízható szolgáltatástól származó kivételek száma, valamint a tároló metrikái, például a CPU-kihasználtság.  Az stdout-ba vagy a stderr-be kell írnia a tároló naplóit, hogy azok elérhetők legyenek Azure Monitor naplókban.

## <a name="watchdogs"></a>Házőrző

A watchdog általában egy különálló szolgáltatás, amely figyeli az állapotot és a terhelést a szolgáltatások között, Pingeli a végpontokat, és nem várt állapotú eseményeket jelez a fürtben. Ez segít megakadályozni, hogy az észlelt hibák csak egyetlen szolgáltatás teljesítményén alapulnak. A watchdog is jó hely a kód futtatására, amely olyan javító műveleteket hajt végre, amelyek nem igényelnek felhasználói beavatkozást, például a naplófájlok bizonyos időközönkénti törlését. A syslog-ben [Service Fabric Linux-fürt eseményeiben](https://github.com/Azure-Samples/service-fabric-watchdog-service)tekintse meg a minta watchdog szolgáltatás implementációját.

## <a name="next-steps"></a>Következő lépések

* Az alkalmazások kialakításának első lépései: az [alkalmazás szintű események és a naplók generálása](service-fabric-diagnostics-event-generation-app.md).
* Hajtsa végre az alkalmazáshoz való Application Insights beállításának lépéseit, [és figyelje meg, hogyan diagnosztizálhatja a ASP.net Core alkalmazást a Service Fabricon](service-fabric-tutorial-monitoring-aspnet.md).
* További információ a platform figyeléséről és a Service Fabric által szolgáltatott eseményekről: [platform szintű esemény és napló létrehozása](service-fabric-diagnostics-event-generation-infra.md).
* Azure Monitor logs-integráció konfigurálása a Service Fabric: [fürt Azure monitor naplóinak beállítása](service-fabric-diagnostics-oms-setup.md)
* Megtudhatja, hogyan állíthat be Azure Monitor naplókat figyelési tárolók számára: [figyelés és diagnosztika Windows-tárolók számára az Azure Service Fabricban](service-fabric-tutorial-monitoring-wincontainers.md).
* Példa diagnosztikai problémákra és megoldásokra a Service Fabric: [gyakori forgatókönyvek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Ismerje meg az Azure-erőforrásokra vonatkozó általános monitorozási javaslatokat: [ajánlott eljárások – monitorozás és diagnosztika](/azure/architecture/best-practices/monitoring).
