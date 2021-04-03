---
title: A Service Fabric programozási modelljének áttekintése
description: 'Service Fabric két keretrendszert kínál a szolgáltatások létrehozásához: a Actor Framework és a Services Framework. Az egyszerűség és a szabályozás terén különböző kompromisszumokat biztosítanak.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575720"
---
# <a name="service-fabric-programming-model-overview"></a>A Service Fabric programozási modelljének áttekintése

A Service Fabric többféle módszert kínál a szolgáltatások írására és kezelésére. A szolgáltatások használhatják a Service Fabric API-kat, hogy teljes mértékben kihasználhassa a platform funkcióit és alkalmazási keretrendszereit. A szolgáltatások a Service Fabric-fürtön üzemeltetett tárolókban futtatott bármely nyelven vagy kódban írt lefordított végrehajtható program is lehet.

## <a name="guest-executables"></a>Vendég végrehajtható fájlok

A [vendég végrehajtható fájl](service-fabric-guest-executables-introduction.md) egy meglévő, tetszőlegesen végrehajtható (bármilyen nyelven írt) végrehajtható fájl, amely szolgáltatásként futtatható az alkalmazásban. A vendég végrehajtható fájlok közvetlenül nem hívhatják a Service Fabric SDK API-kat. Azonban továbbra is kihasználják a platform által kínált szolgáltatásokat, például a szolgáltatások felderíthetővé tételét, az egyéni állapotot és a betöltési jelentéskészítést azáltal, hogy az Service Fabric által elérhető REST API-kat Emellett teljes körű alkalmazás-életciklus-támogatással rendelkeznek.

Ismerkedés a vendég-végrehajtható fájlokkal az első [vendég végrehajtható alkalmazás](service-fabric-deploy-existing-app.md)üzembe helyezésével.

## <a name="containers"></a>Tárolók

Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric [tárolókban](service-fabric-containers-overview.md)is telepíthetnek szolgáltatásokat. Service Fabric támogatja a Linux-tárolók és Windows-tárolók telepítését a Windows Server 2016-es és újabb verzióiban. A tároló lemezképeit bármely tároló adattárból lehívhatja, és üzembe helyezheti a gépre. A meglévő alkalmazásokat vendégként is üzembe helyezheti, Service Fabric állapot nélküli vagy állapot-nyilvántartó megbízható szolgáltatásokat vagy Reliable Actors tárolókban, és az ugyanazon alkalmazásban lévő tárolókban található folyamatokban és szolgáltatásokban is összekeverheti a szolgáltatásokat.

[További információ a szolgáltatások containerizing Windows vagy Linux rendszeren](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

A Reliable Services a Service Fabric platformmal integrált és a platform összes funkciójának előnyeit kihasználó, kis súlyú keretrendszer. Reliable Services minimális API-készletet biztosítanak, amelyek lehetővé teszik a Service Fabric futtatókörnyezet számára a szolgáltatások életciklusának kezelését, és amelyek lehetővé teszik, hogy a szolgáltatások a futtatókörnyezettel kommunikálhatnak. Az alkalmazás-keretrendszer minimális, így teljes körűen szabályozható a tervezési és megvalósítási döntések, és bármely más alkalmazás-keretrendszer, például ASP.NET Core üzemeltetésére használható.

A Reliable Services állapot nélküli lehet, hasonlóan a legtöbb szolgáltatási platformhoz, például a webkiszolgálókhoz, amelyekben a szolgáltatás minden példánya egyenlő, és az állapot egy külső megoldásban, például az Azure DB-ben vagy az Azure-Table Storageban is megmarad.

A Service Fabric kizárólagos értéke Reliable Services is lehet, hogy az állapotot a megbízható gyűjtemények használatával közvetlenül a szolgáltatásban őrzi meg. Az állapotot a rendszer a replikáción keresztül, a particionálással elosztva, a Service Fabric által automatikusan felügyelt módon végzi el.

[Az első megbízható szolgáltatás megírásával többet is](service-fabric-reliable-services-quick-start.md) [megtudhat a Reliable Servicesról](service-fabric-reliable-services-introduction.md) vagy az első lépésekről.

## <a name="aspnet-core"></a>ASP.NET Core

A ASP.NET Core egy nyílt forráskódú, többplatformos keretrendszer a modern, felhőalapú internetkapcsolattal rendelkező alkalmazások, például a Web Apps, a IoT-alkalmazások és a mobil háttérrendszer létrehozásához. A Service Fabric integrálható ASP.NET Coreekkel, így olyan állapot nélküli és állapot-nyilvántartó ASP.NET Core is írhat, amely kihasználja a megbízható gyűjtemények előnyeit és Service Fabric speciális előkészítési képességeit.

[További információ a Service Fabric ASP.net Coreáról](service-fabric-reliable-services-communication-aspnetcore.md) vagy az [első ASP.net Core Service Fabric alkalmazás megírásáról](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

A Reliable Servicesra épülő megbízható Actor Framework egy alkalmazás-keretrendszer, amely a számítási [modell](https://en.wikipedia.org/wiki/Actor_model)alapján implementálja a [virtuális színész](https://research.microsoft.com/en-us/projects/orleans/) mintáját. A *megbízható szereplők keretrendszere* a számítási és az állapot-független egységeket használja az egyszálas végrehajtással. A megbízható Actors keretrendszer beépített kommunikációt biztosít a szereplők számára, és előre beállított állapot-megőrzést és kibővíthető konfigurációkat tartalmaz.

Mivel a Reliable Actors Reliable Servicesre épülő alkalmazás-keretrendszer, teljes mértékben integrálva van a Service Fabric platformmal, és a platform által kínált összes funkció előnyeit élvezheti.

[Tudjon meg többet a Reliable Actorsról](service-fabric-reliable-actors-introduction.md) , vagy kezdje el az [első megbízható Actor szolgáltatás megírásával](service-fabric-reliable-actors-get-started.md)

[Előtér-szolgáltatás létrehozása ASP.NET Core használatával](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Következő lépések

[A Service Fabric és a tárolók áttekintése](service-fabric-containers-overview.md)

[A Reliable Services áttekintése](service-fabric-reliable-services-introduction.md)

[A Reliable Actors áttekintése](service-fabric-reliable-actors-introduction.md)

[Service Fabric és ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
