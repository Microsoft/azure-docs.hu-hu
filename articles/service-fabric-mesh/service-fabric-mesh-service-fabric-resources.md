---
title: Az Azure Service Fabric Resource Model bemutatása
description: Ismerkedjen meg a Service Fabric erőforrás-modellel, amely a Service Fabric Mesh-alkalmazások definiálásának egyszerűsített módszere.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626909"
---
# <a name="introduction-to-service-fabric-resource-model"></a>A Service Fabric Resource Model bemutatása

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A Service Fabric erőforrás-modell egy egyszerű módszert ismertet a Service Fabric Mesh-alkalmazást alkotó erőforrások definiálásához. Az egyes erőforrások bármilyen Service Fabric környezetbe üzembe helyezhetők.  A Service Fabric erőforrás-modell a Azure Resource Manager modellel is kompatibilis. Ebben a modellben jelenleg a következő típusú erőforrások támogatottak:

- Alkalmazások és szolgáltatások
- Hálózatok
- Átjárók
- Titkok és titkok/értékek
- Kötetek

A rendszer minden erőforrást deklaratív módon ismertet egy erőforrás-fájlban, amely egy egyszerű YAML vagy JSON-dokumentum, amely leírja a Mesh alkalmazást, és a Service Fabric platformon van kiépítve.

## <a name="applications-and-services"></a>Alkalmazások és szolgáltatások

Az alkalmazás-erőforrás a háló alkalmazás üzembe helyezésének, verziószámozásának és élettartamának egysége. A szolgáltatás egy vagy több olyan szolgáltatási erőforrásból áll, amely a szolgáltatást jelképezi. Az egyes szolgáltatási erőforrások egy vagy több kódból állnak, amelyek leírják mindazt, ami a programkódhoz tartozó tároló rendszerképének futtatásához szükséges.

![Alkalmazások és szolgáltatások][Image1]

A szolgáltatási erőforrások deklarálják a következőket:

- Tároló neve, verziója és beállításjegyzéke
- Az egyes tárolók számára szükséges CPU-és memória-erőforrások
- Hálózati végpontok
- Más erőforrásokra, például hálózatokra, kötetekre és titkos kulcsokra mutató hivatkozások 

A szolgáltatás-erőforrás részeként definiált összes kód csomag egy csoportként van telepítve és aktiválva. A szolgáltatási erőforrás azt is leírja, hogy a szolgáltatás hány példánya fusson, valamint más erőforrásokra (például hálózati erőforrásra) is hivatkozik.

Ha egy Mesh-alkalmazás több szolgáltatásból áll, azok nem garantáltan ugyanazon a csomóponton futnak együtt. Emellett az alkalmazás frissítése során az egyetlen szolgáltatás verziófrissítésének sikertelensége esetén az összes szolgáltatás visszakerül a korábbi verzióra.

Ahogy azt korábban is említettük, az egyes alkalmazás-példányok életciklusa egymástól függetlenül kezelhető. Például egy alkalmazás példánya a többi alkalmazás példányaitól függetlenül frissíthető. Általában a szolgáltatások számát egy alkalmazásban tartja elég kicsivé, ahogy az alkalmazásba helyezett több szolgáltatás, annál nehezebbé válik az egyes szolgáltatások egymástól független kezelése.

## <a name="networks"></a>Hálózatok

A hálózati erőforrás különállóan telepíthető erőforrás, amely független olyan alkalmazás-vagy szolgáltatási erőforrástól, amely függőségként hivatkozik rá. Az alkalmazások hálózatának létrehozására szolgál. A különböző alkalmazások több szolgáltatása is lehet ugyanazon hálózat része.  További információért olvassa el a [hálózatkezelés Service Fabric Mesh-alkalmazásokban](service-fabric-mesh-networks-and-gateways.md)című témakört.

> [!NOTE]
> Az aktuális előnézet csak egy-egy hozzárendelést támogat az alkalmazások és hálózatok között

![Hálózat és átjáró][Image2]

## <a name="gateways"></a>Átjárók
Az átjáró-erőforrások két hálózatot kapcsolnak össze, és a forgalmat irányítják.  Az átjáró lehetővé teszi, hogy a szolgáltatások kommunikálnak a külső ügyfelekkel, és bejövő forgalmat biztosítson a szolgáltatás (ok) ba.  Az átjárók a háló alkalmazás a saját, meglévő virtuális hálózattal való összekapcsolására is használhatók. További információért olvassa el a [hálózatkezelés Service Fabric Mesh-alkalmazásokban](service-fabric-mesh-networks-and-gateways.md)című témakört.

![Hálózat és átjáró][Image2]

## <a name="secrets"></a>Titkos kulcsok

A titkok erőforrásai olyan alkalmazás-vagy szolgáltatási erőforrástól függetlenül telepíthetők, amely függőségként hivatkozik rá. A szolgáltatás a titkok biztonságos kézbesítésére szolgál az alkalmazásokban. A különböző alkalmazások több szolgáltatása is hivatkozhat ugyanazon titok értékeire.

## <a name="volumes"></a>Kötetek

A tárolók gyakran teszik elérhetővé az ideiglenes lemezeket. Az ideiglenes lemezek elmúló jellegűek, ezért új ideiglenes lemezt kap, és elveszíti az adatokat, amikor a tároló összeomlik. A többi tárolóval rendelkező ideiglenes lemezekkel kapcsolatos információkat is nehéz megosztani. A kötetek olyan könyvtárak, amelyek az állapot megőrzéséhez használható tároló-példányokon belül vannak csatlakoztatva. A kötetek általános célú fájlmegosztást biztosítanak, és lehetővé teszik a fájlok olvasását/írását a normál lemez I/O-fájl API-jai használatával. A mennyiségi erőforrás egy deklaratív módszer, amely leírja, hogyan van csatlakoztatva egy könyvtár és a hozzá tartozó tároló (Azure Files kötet vagy Service Fabric megbízható kötet).  További információért olvassa el a [tárolás állapotot](service-fabric-mesh-storing-state.md#volumes).

![A diagram egy lemezes kötetre áramló szolgáltatást mutat be, amely Service Fabric megbízható kötetre, majd egy replikált helyi lemezre, valamint Azure Files kötetre, majd a hálózati tárterületre áramlik.][Image3]

## <a name="programming-models"></a>Programozási modellek
A szolgáltatási erőforráshoz csak a tároló rendszerképének futtatására van szükség, amely az erőforráshoz társított kód csomag (ok) ra hivatkozik. Bármilyen, bármilyen nyelven írt kódot futtathat a tárolón belül bármely keretrendszer használatával anélkül, hogy tudnia kellene vagy használni kellene Service Fabric Mesh-specifikus API-kat. 

Az alkalmazás kódja Service Fabric hálón kívül is hordozható marad, és az alkalmazás központi telepítései konzisztensek maradnak a szolgáltatások megvalósításához használt nyelvtől vagy keretrendszertől függetlenül. Az Service Fabric Mesh erőforrás-telepítési modell nem azonos a ASP.NET Core, a go, vagy csak a folyamatok és parancsfájlok készletével. 

## <a name="packaging-and-deployment"></a>Csomagolás és üzembe helyezés

Az erőforrás-modellen alapuló Service Fabric Mesh-alkalmazások a Docker-tárolóként vannak csomagolva.  Service Fabric Mesh egy megosztott, több-bérlős környezet, és a tárolók magas szintű elkülönítést biztosítanak.  Ezeket az alkalmazásokat JSON-formátum vagy YAML-formátum (ezt követően JSON-re konvertálva) írják le. Amikor Mesh-alkalmazást telepít az Azure Service Fabric Meshba, az alkalmazás leírására használt JSON egy Azure Resource Manager sablon. Az erőforrások az Azure-erőforrásokra vannak leképezve.  Ha egy Mesh-alkalmazást Service Fabric fürtre (önálló vagy Azure-beli) helyez üzembe, az alkalmazás leírásához használt JSON formátuma egy Azure Resource Manager sablonhoz hasonlít.  A üzembe helyezést követően a háló alkalmazások a HTTP-interfészeken vagy az Azure CLI-n keresztül felügyelhetők. 


## <a name="next-steps"></a>Következő lépések 
Ha többet szeretne megtudni a Service Fabric Meshról, olvassa el az áttekintést:
- [Service Fabric Mesh – áttekintés](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
