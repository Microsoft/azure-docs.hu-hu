---
title: Az Azure Service Fabric Mesh áttekintése
description: Az Azure Service Fabric Mesh bemutatása. A Service Fabric Mesh használatával anélkül helyezheti üzembe és skálázhatja az alkalmazást, hogy aggódnia kellene az alkalmazás infrastrukturális igényei miatt.
author: georgewallace
ms.author: gwallace
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: 64880a9ac3d6d48ce6d39d0b3a7a3aff6587f328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626977"
---
# <a name="what-is-service-fabric-mesh"></a>Mi a Service Fabric Mesh?

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez a videó rövid áttekintést nyújt a Service Fabric Mesh szolgáltatásról.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh használatával üzembe helyezett alkalmazások futtatásakor és skálázásakor nem kell aggódnia a mögöttes infrastruktúra miatt.  A Service Fabric Mesh több ezer gép alkotta fürtökből áll.  A fürtök minden művelete rejtve van a fejlesztő elől. Töltse fel a kódot, és adja meg a szükséges erőforrásokat, a rendelkezésre állási követelményeket és az erőforrás-korlátokat.  A Service Fabric Mesh automatikusan lefoglalja az infrastruktúrát, és kezeli az infrastruktúrával kapcsolatos hibákat is, így biztosítva az alkalmazásai magas rendelkezésre állását. Csak az alkalmazás állapotával és válaszképességével kell törődnie, az infrastruktúrával nem.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ez a cikk áttekintést nyújt a Service Fabric Mesh fő előnyeiről.

## <a name="great-developer-experience"></a>Nagyszerű fejlesztői élmény

A Service Fabric Mesh a tárolókon futtatható bármely programozási nyelvet vagy keretrendszert támogatja. A Visual Studio 2019 és a Visual Studio Code-eszközök támogatása hatékony szerkesztési és hibakeresési élményt biztosít a .NET-és .NET Core-alkalmazásokhoz. 

A Service Fabric Mesh használatával a következőket teheti:

- Meglévő alkalmazások „átemelése” tárolókba, hogy modernizálhassa és nagy mennyiségben futtathassa az aktuális alkalmazásokat.
- Új mikroszolgáltatás-alkalmazások létrehozása és üzembe helyezése nagy mennyiségben az Azure-ban.  Integráció más Azure-szolgáltatásokkal vagy tárolókban futó meglévő alkalmazásokkal. Az egyes szolgáltatások egy biztonságos, hálózat elkülönített alkalmazás részét képezik. A szolgáltatáshoz erőforrás-irányítási szabályzatok vannak meghatározva a CPU-magok, a memória, a lemezterület és egyebek tekintetében.
- Meglévő alkalmazásokkal való integráció és bővítés az alkalmazások módosítása nélkül. A meglévő alkalmazás új alkalmazáshoz való csatlakoztatása saját virtuális hálózat használatával.  
- A meglévő Cloud Services-alkalmazások modernizálása a Service Fabric Mesh-be való áttelepítéssel.  

## <a name="simple-operational-lifecycle"></a>Egyszerű működési életciklus

Egyszerűen kezelheti a futó alkalmazásokat, figyelheti az alkalmazásokat és hibakeresést az éles környezetekben. Ez a felügyelet magában foglalja az alkalmazások frissítését és verziószámozását. Ezek az alkalmazások egyetlen mikroszolgáltatásból vagy a saját hálózatukon belül elkülönített több mikroszolgáltatásból is állhatnak. Az alkalmazások hatékonyan futnak gyors üzembe helyezés, elhelyezés és feladatátvételi idők mellett.

A Service Fabric Mesh használatával a következőket teheti:

- Az alkalmazásokat az infrastruktúra explicit kiépítése és kezelése nélkül helyezheti üzembe és kezelheti.  A Service Fabric Mesh kiépíti, frissíti, javítja és fenntartja a mögöttes infrastruktúrát Ön helyett.
- Folyamatos integrációt állíthat be az integrált eszközökkel az alkalmazások egyszerű csomagolása és üzembe helyezése érdekében.
- Azure Resource Manager erőforrások összes funkciójának kihasználása. Ilyen funkciók például a naplózási nyomvonal és az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)). Az Azure Service Fabric Mesh szolgáltatásában üzembe helyezett összes erőforrás Azure Resource Manager erőforrás. Ezen erőforrások közé tartoznak az alkalmazások, a szolgáltatások, a titkos kulcsok stb.
- Az [Azure Portal](https://portal.azure.com), Resource Manager-sablonok vagy Azure CLI-/PowerShell-kódtárak használatával helyezheti üzembe és kezelheti az erőforrásokat.
- Működésfigyelést és riasztást állíthat be az [Application Insights](/azure/application-insights/) (vagy a kívánt eszköz) használatával a működési és diagnosztikai nyomkövetések rögzítéséhez a platformról.
- Az alkalmazásmodellből küldött alkalmazásdiagnosztikai információkat érhet el az [Application Insights](/azure/application-insights/) vagy a kívánt eszköz használatával.
- Optimalizálja az erőforrás-használatot az alkalmazás-definícióban található szolgáltatások automatikus méretezési szabályainak megadásával.

## <a name="mission-critical-platform-capabilities"></a>Az üzletmenet szempontjából kritikus fontosságú platformképességek

A Service Fabric Mesh a fürtök egy gyűjteményét hozza létre, amelyek áthidalják az [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md) és/vagy a geopolitikai régiók határait. Service Fabric Mesh olyan alkalmazásokkal rendelkezik, amelyek olyan szándékokkal rendelkeznek, mint a méretezés, a hardverkövetelmények, a tartóssági követelmények és a biztonsági szabályzatok.  Az alkalmazás üzembe helyezésekor a Service Fabric Mesh megkeresi a futtatásához optimális helyet.

A Service Fabric Mesh használatával a következőket teheti:

- Használja ki a magas rendelkezésre állás, a le-/felskálázás, a felderíthetőség, a vezénylés, az üzenet-útválasztás, a megbízható üzenetkezelés, az állásidő nélküli frissítések, a biztonság/a titkos kódok kezelése, a vészhelyreállítás, az állapotkezelés, a konfigurációkezelés és az elosztott tranzakciók előnyeit.
- Az alkalmazások létrehozásakor több alkalmazásmodell közül választhat.
- A REST-végpontokon keresztül közzétett platformképességeket használhat a Swagger révén létrehozott nyelvspecifikus kötések használatával.
- [Rendelkezésre állási zónák](../availability-zones/az-overview.md) és több régió között helyezhet üzembe alkalmazásokat a földrajzi megbízhatóság érdekében.
- Az Azure által nyújtott összes biztonsági és megfelelőségi szolgáltatást használhatja.

## <a name="next-steps"></a>Következő lépések

Csupán néhány lépést kell elvégezni a mintaprojekt Visual Studióval való üzembe helyezéséhez. További információért lásd: [ASP.NET Core-webhely létrehozása](service-fabric-mesh-quickstart-dotnet-core.md). 

Válaszok a [gyakori kérdésekre](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
