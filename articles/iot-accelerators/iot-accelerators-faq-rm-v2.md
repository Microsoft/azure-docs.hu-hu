---
title: Távoli figyelés megoldásgyorsító – gyakori kérdések – Azure |} A Microsoft Docs
description: Távoli figyelési megoldásgyorsító kapcsolatos gyakori kérdések
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 746d21c7ff4d5b939eea7690193ac07425b4001c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602630"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Távoli figyelési megoldásgyorsító kapcsolatos gyakori kérdések

Lásd még az általános [– gyakori kérdések](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Ez mennyibe az új távoli figyelési megoldás kiépítése?

Az új megoldásgyorsító kétféle telepítési lehetőséget kínál:

* A *alapszintű* beállítást keres a kisebb fejlesztési költség és ügyfelei számára szeretne létrehozni egy bemutatót vagy a koncepció igazolása fejlesztők számára készült.
* A *standard* lehetőség, amely egy éles használatra kész infrastruktúra üzembe helyezését, aki a vállalatok számára.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hogyan biztosítható meg, hogy e tarthatja a saját költségeket, miközben fejleszteni saját megoldás-e?

Amellett, hogy a két differenciált üzembe helyezés, az új távoli figyelési megoldás egy engedélyezheti vagy tilthatja le a szimulált eszközök az igény szerinti beállítása megfelelő-e. A szimuláció letiltása csökkenti a a megoldás, és így a teljes költség a betöltött adatokat.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Mi a különbség az alapszintű és standard szintű üzembe helyezési beállítások? Hogyan válasszak a két telepítési lehetőségek között?

Különböző igényekre különböző telepítési lehetőségek válaszol. Az alapszintű üzemelő példányhoz első lépései és fejleszthet PoC és kisméretű próbakörnyezetek célja. A minimálisan szükséges erőforrásokat és a egy alacsonyabb költségű egy korszerű architektúrát biztosít. A normál központi telepítés célja, hogy hozhat létre, és a egy éles használatra kész megoldás testreszabása, és biztosítja a szükséges elemeket, vegye figyelembe, hogy a telepítésben. A megbízhatóság és a méretezési csoport alkalmazás mikroszolgáltatások Docker-tárolókként épülnek, és egy orchestrator (alapértelmezés szerint a Kubernetes) használatával üzembe helyezett. Az orchestrator telepítési, méretezését és felügyeletét, az alkalmazás feladata. Válassza a beállítást, a jelenlegi igények alapján. Használhat egy, a másik vagy mindkettőt, attól függően, a projekt szakasza.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hogyan konfigurálható a dinamikus térképen az irányítópult?

További információkért lásd: [eszközök megtekintéséhez a térképen a dinamikus frissítési leképezés kulcs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol található információ a távoli figyelési megoldás előző verzióját?

A távoli figyelési megoldásgyorsító előző verziója néven volt ismert, az IoT Suite távoli figyelési előre konfigurált megoldás. Az archivált dokumentáció annak [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távoli figyelési megoldásgyorsító funkcióinak megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Okosgyár-megoldásgyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
