---
title: Azure Service Fabric-figyelési partnerek
description: Ismerje meg, hogyan figyelhetők meg az Azure Service Fabric-alkalmazások,-fürtök és-infrastruktúra a partner-figyelési megoldásokkal.
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 92ac0627f02ccefdd4c93aa51cac7c9dd7790460
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627727"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric-figyelési partnerek

Ez a cikk azt szemlélteti, hogyan figyelhetők meg az Service Fabric alkalmazások, fürtök és infrastruktúra egy maroknyi partneri megoldással. Az alábbi partnerekkel együttműködve integrált ajánlatokat hozhat létre Service Fabric számára.

## <a name="dynatrace"></a>Dynatrace

A Dynatrace szolgáltatással való integráció számos lehetőséget kínál a Service Fabric-fürtök figyelésére. A VMSS-példányok Dynatrace-OneAgent telepítése a teljesítményszámlálók és a Service Fabric üzembe helyezésének topológiáját biztosítja az alkalmazás szintjén. A Dynatrace szintén kiváló választás a helyszíni figyeléshez. Tekintse meg a [közleményben](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) felsorolt funkciókat és a Dynatrace engedélyezéséhez szükséges [utasításokat](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) a fürtön. 

## <a name="datadog"></a>Datadog

A datadoggal a Windows-és Linux-példányok esetében a VMSS bővítményt is tartalmaz. A Datadoggal használatával gyűjthet Windows-eseménynaplókat, és így Service Fabric platform-eseményeket gyűjthet a Windowsban. Tekintse meg a diagnosztikai [adatai datadoggal való](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)elküldésére vonatkozó utasításokat.

## <a name="appdynamics"></a>AppDynamics

A AppDynamics Service Fabric integrációja az alkalmazás szintjén történik. A környezeti változók frissítésével és az alkalmazás-Dynamics Nuget használatával a telemetria a AppDynamics-be küldheti. A .NET Service Fabric-alkalmazások AppDynamics való integrálásával kapcsolatban itt talál [útmutatást](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) .

## <a name="new-relic"></a>New Relic

Az új ereklye egy másik alkalmazás-Teljesítményfigyelő eszköz, amely Service Fabric alkalmazásokkal jól integrálható. Telepítheti az új ereklye NuGet-csomagokat, és meghatározott környezeti változókat adhat hozzá a jegyzékfájlokhoz, hogy az alkalmazás telemetria az új ereklye számára küldje el. Tekintse meg ezeket az [utasításokat](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) , hogy engedélyezze az új ereklye telemetria a .net Service Fabric alkalmazásaihoz.

## <a name="elk"></a>ELK 

A ELK stack nyílt forráskódú technológiák gyűjteménye: Elasticsearch, Logstash és Kibana. Ezen technológiák együttes használatával összegyűjtheti, tárolhatja és elemezheti Service Fabric monitorozási és diagnosztikai adatait. Ebben az oktatóanyagban megtudhatja, hogyan teheti ezt Service Fabric natív Java [-alkalmazásokkal](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

A Humio egy olyan naplózási szolgáltatás, amely a felhőben vagy a helyszínen valós időben Service Fabric a naplókat az alkalmazásokból és az eseményekről. Az élő megfigyelésen kívül a Humio legkorszerűbb elemzési és vizualizációs funkciókat kínál a diagnosztika adatainak megtekintésére és gyűjtésére. A Humio költséghatékony díjszabási terveket tartalmaz, és a méretezési folyamat megtartása mellett a gyors gyorsaságot is megőrzi. Közvetlenül integrálható Service Fabric platform eseményeivel és az alkalmazás telemetria. A Humio és a Service Fabric integrációról [itt](https://github.com/humio/service-fabric-humio)olvashat bővebben.

## <a name="next-steps"></a>Következő lépések

* A [monitorozás és a diagnosztika áttekintése](service-fabric-diagnostics-overview.md) Service Fabric
* Ismerje meg, hogyan [diagnosztizálhatja a gyakori forgatókönyveket](service-fabric-diagnostics-common-scenarios.md) az első féltől származó eszközökkel
