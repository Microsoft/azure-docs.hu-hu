---
title: Áttekintés
description: Ismerje meg, hogy az Azure App Service segítségével miként fejleszthet és üzemeltethet webalkalmazásokat.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 668988ae34c2f97f3baca3f360c13e3ec3e30731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586349"
---
# <a name="app-service-overview"></a>Az App Service áttekintése

A *Azure app Service* egy HTTP-alapú szolgáltatás, amellyel webalkalmazásokat, REST API-kat és mobil back-végpontokat üzemeltet. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Az alkalmazások Windows-és [Linux](#app-service-on-linux)-alapú környezetekben is könnyedén futnak és méretezhetők.

App Service nem csupán az alkalmazáshoz Microsoft Azure, például a biztonság, a terheléselosztás, az automatikus skálázás és az automatizált felügyelet erejét adja hozzá. Kihasználhatja a DevOps képességeit, például a folyamatos üzembe helyezést az Azure DevOps, a GitHubból, a Docker hub-ból és más forrásokból, a csomagok felügyeletéről, az átmeneti környezetekről, az egyéni tartományról és a TLS/SSL-tanúsítványokról. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. A felhasznált számítási erőforrásokat a _app Service-csomag_ határozza meg, amelyet az alkalmazásaiban futtat. További információ: Azure App Service- [csomagok áttekintése](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?

A App Service főbb jellemzői:

* **Több nyelv és keretrendszer** – app Service rendelkezik a ASP.net, a ASP.net Core, a Java, a Ruby, a Node.js, a php vagy a Python első osztályú támogatásával. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](webjobs-create.md) futtathat háttérszolgáltatásként.
* **Felügyelt éles környezet** – app Service automatikusan [kijavításokat végez, és KARBANTARTJA az operációs rendszer és a nyelv keretrendszerét](overview-patch-os-runtime.md) . Töltsön fel időt a nagyszerű alkalmazások írásakor, és hagyja, hogy az Azure aggódnia legyen a platformmal kapcsolatban.
* **Tárolókra bontás és Docker** -Dockerize az alkalmazást, és egy egyéni Windows vagy Linux-tárolót üzemeltet a app Service. Több tárolóból álló alkalmazások futtatása a Docker-összeállítással. A Docker-képességek áttelepíthetők közvetlenül a App Servicera.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](deploy-continuous-deployment.md) állíthat be az Azure DevOps, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](deploy-staging-slots.md) segítségével küldheti ki a frissítéseket. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azure/) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** - Manuálisan vagy automatikusan is végezhet [felfelé skálázást](manage-scale-up.md) és [horizontális skálázást](../azure-monitor/autoscale/autoscale-get-started.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS-platformokhoz és helyszíni adatokhoz** – Több mint 50, nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz) készült [összekötő](../connectors/apis-list.md) közül választhat. Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](app-service-hybrid-connections.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/en-us/trustcenter). Felhasználók hitelesítése [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md)vagy [Microsoft-fiók](configure-authentication-provider-microsoft.md)használatával. [IP-címkorlátozásokat](app-service-ip-restrictions.md) hozhat létre és [kezelheti a szolgáltatásidentitásokat](overview-managed-identity.md).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) alkalmazássablon-listájának széles kínálatából választhat, például a WordPresst, a Joomlát vagy a Drupalt.
* A **Visual Studio és a Visual Studio Code Integration** – dedikált eszközök a Visual Studióban és a Visual Studio Code-ban egyszerűsíthető a létrehozás, az üzembe helyezés és a hibakeresés.
* Az **API-és mobil funkciók** – app Service kulcsrakész CORS-támogatást biztosít a REST API-forgatókönyvekhez, és egyszerűbbé teszi a Mobile apps-forgatókönyveket a hitelesítés, az offline adatszinkronizálás, a leküldéses értesítések és egyebek engedélyezésével.
* **Kiszolgáló nélküli kód** – Kódrészleteteket vagy szkripteket futtathat igény szerint anélkül, hogy kifejezetten felügyelnie vagy kezelnie kellene az infrastruktúrát, és csak a kódja által ténylegesen használt számítási időért kell fizetnie (további információ: [Azure Functions](../azure-functions/index.yml)).

App Service mellett az Azure más szolgáltatásokat is kínál, amelyek a webhelyek és a webalkalmazások üzemeltetésére használhatók. A legtöbb esetben App Service a legjobb választás.  A Service architektúrához az [Azure Spring-Cloud Service](../spring-cloud/index.yml) -t vagy [Service Fabrict](https://azure.microsoft.com/documentation/services/service-fabric)kell figyelembe venni.  Ha nagyobb mértékű vezérlésre van szüksége a kódot futtató virtuális gépek felett, vegye fontolóra az [Azure Virtual Machinesét](https://azure.microsoft.com/documentation/services/virtual-machines/). További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service Linux rendszeren

A App Service a webalkalmazásokat natív módon is üzemeltetheti Linux rendszeren a támogatott alkalmazási veremekhez. Emellett egyéni Linux-tárolókat is futtathat (más néven Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Beépített nyelvek és keretrendszerek

A Linuxon App Service számos különböző beépített rendszerképet támogat. Csak telepítse a kódot. A támogatott nyelvek a következők: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core és Ruby. [`az webapp list-runtimes --linux`](/cli/azure/webapp#az-webapp-list-runtimes)A Futtatás gombra kattintva megtekintheti a legújabb nyelveket és a támogatott verziókat. Ha az alkalmazás által igényelt futtatókörnyezet nem támogatott a beépített lemezképekben, akkor telepítheti azt egy egyéni tárolóval.

Az elavult futtatókörnyezeteket a rendszer rendszeres időközönként eltávolítja a portálon a Web Apps létrehozás és konfiguráció panelről. Ezek a futtatókörnyezetek rejtve maradnak a portálon, ha a karbantartó szervezet elavult vagy jelentős biztonsági réseket talált. Ezek a beállítások rejtettek, hogy az ügyfeleket a legújabb futtatókörnyezetekhez irányítsák, ahol a legsikeresebb lesz. 

Ha egy elavult futtatókörnyezet el van rejtve a portálról, akkor az adott verziót használó meglévő helyek továbbra is futnak. Ha egy futtatókörnyezet teljesen el lett távolítva a App Service platformról, az Azure-előfizetéshez tartozó tulajdonos (ok) e-mailben értesítést fog kapni az Eltávolítás előtt.

Ha egy olyan elavult futtatókörnyezet-verzióval rendelkező webalkalmazást kell létrehoznia, amely már nem jelenik meg a portálon, tekintse meg a nyelvi konfigurációs útmutatókat, amelyekkel megtudhatja, hogyan kérheti le a hely futtatókörnyezetének verzióját. Az Azure CLI használatával egy másik helyet is létrehozhat ugyanazzal a futtatókörnyezettel. Azt is megteheti, hogy a portál webalkalmazás paneljének **Exportálás sablonja** gombjára kattintva exportálja a hely ARM-sablonját. Újból felhasználhatja ezt a sablont egy olyan új hely üzembe helyezéséhez, amely azonos futtatókörnyezettel és konfigurációval rendelkezik.

### <a name="limitations"></a>Korlátozások

- A Linuxon App Service nem támogatott a [megosztott](https://azure.microsoft.com/pricing/details/app-service/plans/) díjszabási szinten. 
- A Windows-és Linux-alkalmazások nem kombinálhatók ugyanabban a App Service tervben.  
- A Windows-és Linux-alkalmazások nem kombinálhatók ugyanabban az erőforráscsoporthoz. Azonban a 2021. január 21. után létrehozott összes erőforráscsoport támogatja ezt a forgatókönyvet. A 2021. január 21. előtt létrehozott erőforráscsoportok esetén a vegyes platformon üzemelő példányok hozzáadásának lehetősége hamarosan elérhető lesz az Azure-régiókban (beleértve a nemzeti Felhőbeli régiókat is).
- A Azure Portal csak a Linux-alkalmazásokhoz jelenleg használható funkciókat jeleníti meg. Ha a funkciók engedélyezve vannak, azok aktiválva lesznek a portálon.
- Beépített rendszerképekre való üzembe helyezéskor a kód és a tartalom a webes tartalomhoz tartozó tárolási kötetet foglal le, amelyet az Azure Storage támogat. A kötet lemezének késése nagyobb és változó, mint a tároló fájlrendszerének késése. Azok az alkalmazások, amelyeknek a tartalomhoz való nagy olvasási hozzáférésre van szükségük, az egyéni tároló lehetőséggel is rendelkezhetnek, amely a tartalom kötetén található fájlokat a tároló fájlrendszerében helyezi el.

## <a name="next-steps"></a>Következő lépések

Hozza létre első webalkalmazását.

> [!div class="nextstepaction"]
> [ASP.NET Core (Windows vagy Linux rendszeren)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (Windows rendszeren)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (Windows vagy Linux rendszeren)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linuxon)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (Windows vagy Linux rendszeren)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (Windows vagy Linux rendszeren)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (on Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (Windows vagy Linux rendszeren)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Egyéni tároló (Windows vagy Linux)](tutorial-custom-container.md)
