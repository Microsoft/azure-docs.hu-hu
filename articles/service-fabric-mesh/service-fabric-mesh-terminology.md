---
title: Az Azure Service Fabric Mesh terminológiája
description: Ez a cikk részletesen ismerteti az Azure Service Fabric Mesh által használt terminológiát, amely segít jobban megérteni a dokumentációban használt feltételeket.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626756"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh-terminológia

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk részletesen ismerteti az Azure Service Fabric Mesh által használt terminológiát, amely segít jobban megérteni a dokumentációban használt feltételeket.

## <a name="service-fabric"></a>Service Fabric

A [Service Fabric](../service-fabric/index.yml) egy nyílt forráskódú, elosztott rendszerplatform, amely megkönnyíti a méretezhető és megbízható szolgáltatások csomagolását, üzembe helyezését és felügyeletét. Service Fabric az a Orchestrator, amely a Service Fabric rácsvonalat is felruházza. Service Fabric lehetőségeket biztosít a Service-alkalmazások felépítéséhez és futtatásához. Bármilyen keretrendszert felhasználhat a szolgáltatások írásához, és kiválaszthatja, hogy az alkalmazás hol fusson több környezeti lehetőség közül.

## <a name="application-and-service-concepts"></a>Alkalmazások és szolgáltatások – fogalmak

**Service Fabric Mesh-alkalmazás**: a Service Fabric Mesh-alkalmazásokat az [erőforrás-modell](./service-fabric-mesh-service-fabric-resources.md) (YAML és JSON-erőforrások fájljai) írják le, és bármely olyan környezetben üzembe helyezhetők, ahol Service Fabric fut.

**Natív alkalmazás Service Fabric**: Service Fabric natív alkalmazások a [natív alkalmazás modelljében](../service-fabric/service-fabric-application-model.md) (XML-alapú alkalmazás-és szolgáltatás-jegyzékfájlok) vannak leírva.  Service Fabric natív alkalmazások nem futtathatók Service Fabric hálóban.

**Alkalmazás**: a Service Fabric Mesh-alkalmazás egy Mesh-alkalmazás üzembe helyezésének, verziószámozásának és élettartamának egysége. Az egyes alkalmazás-példányok életciklusa egymástól függetlenül kezelhető.  Az alkalmazások egy vagy több szolgáltatási kódú csomagból és beállításból állnak. Az alkalmazás az Azure Resource Model (RM) séma használatával van definiálva.  A szolgáltatások az alkalmazás-erőforrás tulajdonságaiként szerepelnek egy RM-sablonban.  Az alkalmazás által használt hálózatokat és köteteket az alkalmazás hivatkozik.  Egy alkalmazás létrehozásakor az alkalmazás, a szolgáltatás, a hálózat és a kötet (ek) modellezése a Service Fabric Resource Model használatával történik.

**Szolgáltatás**: az alkalmazás egyik szolgáltatása a szolgáltatást jelöli, és teljes és önálló funkciót hajt végre. Minden szolgáltatás egy vagy több olyan kódból áll, amely leírja mindazt, ami a programkódhoz tartozó tároló rendszerképének futtatásához szükséges.  Az alkalmazásban található szolgáltatás-replikák száma és kiskálázása is lehetséges.

**Kód csomag**: a kód csomagjai leírják a csomaghoz társított tároló rendszerképének futtatásához szükséges összes műveletet, beleértve a következőket:

* Tároló neve, verziója és beállításjegyzéke
* Az egyes tárolók számára szükséges CPU-és memória-erőforrások
* Hálózati végpontok
* A tárolóban csatlakoztatni kívánt kötetek, amelyek egy különálló kötet erőforrására hivatkoznak.

## <a name="deployment-and-application-models"></a>Üzembe helyezési és alkalmazási modellek 

A szolgáltatások üzembe helyezéséhez le kell írnia a futtatásuk módját. A Service Fabric három különböző üzembe helyezési modellt támogat:

### <a name="resource-model"></a>Erőforrásmodell
Service Fabric az erőforrások bármilyen módon üzembe helyezhetők, és a Service Fabric; beleértve az alkalmazásokat, a szolgáltatásokat, a hálózatokat és a köteteket. Az erőforrások egy JSON-fájllal vannak definiálva, amely a fürt végpontján helyezhető üzembe.  Service Fabric Mesh esetében az Azure Resource Model-séma használatos. A YAML-sémák a definíciós fájlok egyszerűbb létrehozásához is használhatók. Az erőforrások bármikor üzembe helyezhetők, Service Fabric futtatva. Az erőforrás-modell az Service Fabric-alkalmazások leírásának legegyszerűbb módja. Fő témája a tárolós szolgáltatások egyszerű üzembe helyezése és kezelése. További információért olvassa el [a Service Fabric Resource Model bemutatása](./service-fabric-mesh-service-fabric-resources.md)című témakört.

### <a name="native-model"></a>Natív modell
A natív alkalmazás modellje lehetővé teszi, hogy az alkalmazások teljes, alacsony szintű hozzáférést biztosítson Service Fabrichoz. Az alkalmazások és szolgáltatások regisztrált típusokként vannak definiálva az XML-jegyzékfájlokban.

A natív modell támogatja a Reliable Services keretrendszert, amely hozzáférést biztosít a Service Fabric Runtime API-khoz és a fürt felügyeleti API-khoz a C#-ban és a Java-ban. A natív modell szintén támogatja a tetszőleges tárolókat és végrehajtható fájlokat.

A natív modell nem támogatott a Service Fabric Mesh környezetben.  További információ: [programozási modell áttekintése](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker-összeállítás 
A [Docker-összeállítás](https://docs.docker.com/compose/) a Docker-projekt részét képezi. A Service Fabric korlátozott támogatást biztosít az alkalmazások Docker-összeállítási modellel történő üzembe helyezéséhez.

## <a name="environments"></a>Környezetek

A Service Fabric egy nyílt forráskódú platform technológia, amelyet számos különböző szolgáltatás és termék alapul. A Microsoft a következő lehetőségeket biztosítja:

 - **Service Fabric Mesh**: egy teljes körűen felügyelt szolgáltatás, amellyel Service Fabric alkalmazások futnak Microsoft Azure.
 - **Azure Service Fabric**: az Azure által üzemeltetett Service Fabric-fürt ajánlata. Integrációt biztosít Service Fabric és az Azure-infrastruktúra között, valamint a Service Fabric-fürtök frissítésével és konfigurálásával.
 - **Önálló Service Fabric**: telepítési és konfigurációs eszközök készlete, amelyekkel a [Service Fabric-fürtök bárhol üzembe helyezhetők](../service-fabric/service-fabric-deploy-anywhere.md) (helyszíni vagy bármely felhőalapú szolgáltatón). Az Azure nem kezeli.
 - **Service Fabric fejlesztési fürt**: a Service Fabric alkalmazások fejlesztéséhez Windows, Linux vagy Mac helyi fejlesztési élményt nyújt.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>A környezet, a keretrendszer és az üzembe helyezési modell támogatási mátrixa
A különböző környezetek különböző szintű támogatást biztosítanak a keretrendszerek és a telepítési modellek számára. A következő táblázat a támogatott keretrendszer-és telepítési modell-kombinációkat ismerteti.

| Alkalmazás típusa | Ismertette | Azure Service Fabric Mesh | Azure Service Fabric-fürtök (bármely operációs rendszer)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Service Fabric Mesh-alkalmazások | Erőforrás-modell (YAML & JSON) | Támogatott |Nem támogatott | Windows – támogatott, Linux és Mac – nem támogatott | Windows – nem támogatott |
|Natív alkalmazások Service Fabric | Natív alkalmazás modellje (XML) | Nem támogatott| Támogatott|Támogatott|Windows – támogatott|

A következő táblázat ismerteti a különböző alkalmazás-modelleket, valamint a Service Fabrichoz tartozó eszközöket.

| Alkalmazás típusa | Ismertette | Visual Studio | Eclipse | SFCTL | AZ PARANCSSORI FELÜLET | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh-alkalmazások | Erőforrás-modell (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott – csak Mesh környezet | Nem támogatott|
|Natív alkalmazások Service Fabric | Natív alkalmazás modellje (XML) | VS 2017 és VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

## <a name="next-steps"></a>Következő lépések

A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.

Válaszok a [gyakori kérdésekre](service-fabric-mesh-faq.md).
