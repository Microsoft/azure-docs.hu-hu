---
title: Az Azure Service Fabric áttekintése
description: A Service Fabric egy elosztott rendszerplatform, amely méretezhető, megbízható és könnyen felügyelt szolgáltatásokkal rendelkezik.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028631"
---
# <a name="overview-of-azure-service-fabric"></a>Az Azure Service Fabric áttekintése

Az Azure Service Fabric egy [elosztott rendszerplatform](#container-orchestration) , amely megkönnyíti a méretezhető és megbízható szolgáltatások és tárolók csomagolását, üzembe helyezését és felügyeletét. A Service Fabric a Felhőbeli natív alkalmazások [fejlesztésével és kezelésével](#application-lifecycle-management) kapcsolatos jelentős kihívásokat is tárgyalja.

Service Fabric kulcsfontosságú megkülönbözteti az állapot-nyilvántartó szolgáltatások kiépítésének erős fókuszát. Használhatja a Service Fabric [programozási modellt](#stateless-and-stateful-microservices) , vagy bármilyen nyelven vagy kódban írt, tároló állapotú szolgáltatást futtathat. Az Azure-on kívül [bárhol létrehozhat Service Fabric fürtöket](#any-os-any-cloud), beleértve a Windows Servert és a Linux-t a helyszínen és más nyilvános felhőkben is.

![A Service Fabric platform életciklus-felügyeletet, rendelkezésre állást, előkészítést, programozási modelleket, állapot-és monitorozási, fejlesztési és operatív eszközöket, valamint automatikus skálázást biztosít az Azure-ban, a helyszínen, más felhőkben és a fejlesztői gépen][Image1]

A Service Fabric képezi számos jelenleg elérhető Microsoft-szolgáltatás alapját, ilyen például az Azure SQL Database, az Azure Cosmos DB, a Cortana, a Microsoft Power BI, a Microsoft Intune, az Azure Event Hubs, az Azure IoT Hub, a Dynamics 365, a Skype Vállalati verzió, valamint több alapvető Azure-szolgáltatás.

## <a name="container-orchestration"></a>Tárolóvezénylés

A Service Fabric a Microsoft [tároló-Orchestrator](service-fabric-cluster-resource-manager-introduction.md) , amellyel a rendszer üzembe helyezheti és felügyelheti a gépeket egy fürtön keresztül, így a Microsoft szolgáltatásainak nagy léptékű futtatásával szerzett tapasztalatok is hasznosak. Service Fabric másodpercek alatt üzembe helyezhet alkalmazásokat, nagy sűrűséggel, több száz vagy akár több ezer alkalmazással vagy tárolóval. A Service Fabric használatával mindkét szolgáltatást összekeverheti a tárolókban található folyamatokban és szolgáltatásokban ugyanabban az alkalmazásban.

[További információ a Service Fabric](service-fabric-content-roadmap.md) alapvető fogalmakról, programozási modellekről, alkalmazás-életciklusról, tesztelésről, fürtökről és állapot-figyelésről.

## <a name="stateless-and-stateful-microservices"></a>Állapot nélküli és állapot-nyilvántartó szolgáltatások

Service Fabric egy kifinomult, könnyű futtatókörnyezetet biztosít, amely támogatja az állapot nélküli és állapot-nyilvántartó szolgáltatásokat. A Service Fabric kulcsfontosságú megkülönbözteti az állapot-nyilvántartó szolgáltatások kiépítésének robusztus támogatását Service Fabric [beépített programozási modellekkel](service-fabric-choose-framework.md) vagy a tároló állapot-nyilvántartó szolgáltatásokkal.

További információ a Service Fabric állapot-nyilvántartó szolgáltatások előnyeit kihasználó [alkalmazási forgatókönyvekről](service-fabric-application-scenarios.md) .

## <a name="application-lifecycle-management"></a>Alkalmazások életciklus-felügyelete

A Service Fabric támogatja a Felhőbeli alkalmazások teljes életciklusát és CI/CD-jét, beleértve a tárolók használatát, az üzembe helyezést, a napi figyelést, a felügyeletet és a karbantartást a végleges leszerelésig. A Service Fabric olyan CI-/CD-eszközökkel van integrálva, mint az [Azure Pipelines](https://www.visualstudio.com/team-services/), a [Jenkins](https://jenkins.io/index.html) és az [Octopus Deploy](https://octopus.com/), és bármely más közismert CI-/CD-eszközzel is használható.

Az alkalmazások életciklus-felügyeletével kapcsolatos további tudnivalókért tekintse meg az [alkalmazások életciklusával](service-fabric-application-lifecycle.md) kapcsolatos témakört. Meglévő alkalmazások Service Fabric való üzembe helyezésével kapcsolatban lásd: [vendég végrehajtható fájl telepítése](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Bármely operációs rendszeren vagy felhőben

Számos környezetben, például az [Azure-ban, akár a helyszínen](service-fabric-deploy-anywhere.md), [Windows Serveren vagy Linuxon](service-fabric-linux-windows-differences.md)is létrehozhat fürtöket Service Fabrichoz. Akár más nyilvános felhőkben is létrehozhat fürtöket. A Service Fabric SDK Fejlesztői környezete azonos az éles környezettel, és nincs emulátor. Ez azt jelenti, hogy a helyi fejlesztési fürtön milyen fut a fürtök más környezetekben való üzembe helyezése.

A [Windows-fejlesztéshez](service-fabric-get-started.md)a Service FABRIC .net SDK integrálva van a Visual Studióval és a PowerShell-lel. A [Linux-fejlesztés](service-fabric-get-started-linux.md)esetében a Service FABRIC Java SDK integrálva van az Eclipse-szel, a Yeoman pedig a Java, a .net Core és a Container-alkalmazások számára készült sablonok létrehozására szolgál.

## <a name="compliance"></a>Megfelelőség

Az Azure Service Fabric erőforrás-szolgáltató minden Azure-régióban elérhető, és megfelel az összes Azure-megfelelőségi tanúsítványnak, beleértve a következőket: SOC, ISO, PCI DSS, HIPAA és GDPR. A teljes listát lásd: [Microsoft megfelelőségi ajánlatok](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Következő lépések

Az első alkalmazás létrehozása és üzembe helyezése az Azure Service Fabricban:

> [!div class="nextstepaction"]
> [Service Fabric rövid útmutató][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
