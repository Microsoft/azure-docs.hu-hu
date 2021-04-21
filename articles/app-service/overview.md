---
title: Áttekintés
description: Ismerje meg, hogy az Azure App Service segítségével miként fejleszthet és üzemeltethet webalkalmazásokat.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767227"
---
# <a name="app-service-overview"></a>Az App Service áttekintése

*Azure App Service* egy HTTP-alapú szolgáltatás webalkalmazások, REST API-k és mobil háttéralkalmazások üzemeltetéséhez. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Az alkalmazások egyszerűen futnak és skálázhatók Windows- és [Linux-alapú](#app-service-on-linux)környezetekben is.

App Service az alkalmazáshoz Microsoft Azure, például a biztonsághoz, a terheléselosztáshoz, az automatikus skálázáshoz és az automatikus felügyelethez is. DevOps-képességeit, például az Azure DevOps, a GitHub, a Docker Hub és más források folyamatos üzembe helyezését, a csomagkezelést, az átmeneti környezeteket, az egyéni tartományt és a TLS/SSL-tanúsítványokat is kihasználhatja. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. A használt számítási erőforrásokat az a App Service _határozza_ meg, melyen az alkalmazásokat futtatja. További információ: A [Azure App Service áttekintése.](overview-hosting-plans.md)

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?

Az App Service:

* **Több nyelv** és keretrendszer – App Service első osztályú támogatást nyújt a ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP vagy Python nyelvhez. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](webjobs-create.md) futtathat háttérszolgáltatásként.
* **Felügyelt éles környezet** – App Service automatikusan javítja és karbantartja az operációs rendszer és a nyelvi [keretrendszereket.](overview-patch-os-runtime.md) Töltsön időt nagyszerű alkalmazások írásán, és hagyja, hogy az Azure foglalkozjon a platformmal.
* **Tárolóba ás Docker –** Dockerize your app and host a custom Windows or Linux container in App Service. Többtárolós alkalmazások futtatása a Docker Compose-sal. Docker-készségeit közvetlenül a App Service.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](deploy-continuous-deployment.md) állíthat be az Azure DevOps, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](deploy-staging-slots.md) segítségével küldheti ki a frissítéseket. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azure/) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** - Manuálisan vagy automatikusan is végezhet [felfelé skálázást](manage-scale-up.md) és [horizontális skálázást](../azure-monitor/autoscale/autoscale-get-started.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS-platformokhoz és helyszíni adatokhoz** – Több mint 50, nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz) készült [összekötő](../connectors/apis-list.md) közül választhat. Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](app-service-hybrid-connections.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/en-us/trustcenter). Felhasználók hitelesítése a [Azure Active Directory,](configure-authentication-provider-aad.md) [Google,](configure-authentication-provider-google.md) [Facebook,](configure-authentication-provider-facebook.md) [Twitter](configure-authentication-provider-twitter.md)vagy [Microsoft-fiók.](configure-authentication-provider-microsoft.md) [IP-címkorlátozásokat](app-service-ip-restrictions.md) hozhat létre és [kezelheti a szolgáltatásidentitásokat](overview-managed-identity.md).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) alkalmazássablon-listájának széles kínálatából választhat, például a WordPresst, a Joomlát vagy a Drupalt.
* **Visual Studio és Visual Studio Code-integráció** – Az Visual Studio és Visual Studio Code dedikált eszközei leegyszerűsítik a létrehozás, az üzembe helyezés és a hibakeresés munkáját.
* **API és mobil** funkciók – a App Service kulcsra ható CORS-támogatást nyújt a RESTful API-forgatókönyvekhez, és leegyszerűsíti a mobilalkalmazások forgatókönyvét a hitelesítés, az offline adatszinkronizálás, a leküldéses értesítések stb. engedélyezésével.
* **Kiszolgáló nélküli kód** – Kódrészleteteket vagy szkripteket futtathat igény szerint anélkül, hogy kifejezetten felügyelnie vagy kezelnie kellene az infrastruktúrát, és csak a kódja által ténylegesen használt számítási időért kell fizetnie (további információ: [Azure Functions](../azure-functions/index.yml)).

A App Service mellett az Azure más szolgáltatásokat is kínál, amelyek webhelyek és webalkalmazások üzemeltetéséhez használhatók. A legtöbb esetben a App Service a legjobb választás.  Mikroszolgáltatás-architektúra használata: [Azure Spring-Cloud Service](../spring-cloud/index.yml) vagy [Service Fabric.](https://azure.microsoft.com/documentation/services/service-fabric)  Ha nagyobb irányításra van szüksége a kódot futtató virtuális gépek felett, fontolja meg az [Azure Virtual Machines.](https://azure.microsoft.com/documentation/services/virtual-machines/) További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service Linux rendszeren

App Service webalkalmazásokat natív módon linuxos környezetben is képes a támogatott alkalmazáskészletek számára. Emellett egyéni Linux-tárolókat is futtathat (más néven Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Beépített nyelvek és keretrendszerek

App Service on Linux nyelvspecifikus beépített rendszerképeket támogat. Csak telepítse a kódot. Támogatott nyelvek: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core és Ruby. A legújabb nyelvek és támogatott verziók megtekintéséhez [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) futtassa a következőt: . Ha az alkalmazás által megkövetelt futásidejű környezet nem támogatott a beépített rendszerképekben, akkor egyéni tárolóval is üzembe helyezheti.

Az elavult futásidőket a rendszer rendszeres időközönként eltávolítja a Web Apps létrehozás és konfigurálás paneljről a Portálon. Ezek a futásidők rejtve maradnak a portálon, ha a karbantartó szervezet elavult, vagy jelentős biztonsági réseket talál. Ezek a lehetőségek rejtettek, hogy az ügyfeleket a legújabb futásidejűkhöz irányítják, ahol a leghatékonyabbak lesznek. 

Ha egy elavult futásidő rejtve van a portálon, az adott verziót használó összes meglévő hely továbbra is futni fog. Ha egy futásidőt teljesen eltávolít a App Service platformról, az Azure-előfizetés tulajdonosa(i) e-mailben értesítést kapnak az eltávolítás előtt.

Ha egy másik webalkalmazást kell létrehoznia egy elavult futásidejű verzióval, amely már nem jelenik meg a portálon, tekintse meg a nyelvi konfigurációs útmutatókat a webhely futásidejű verziójának lekért útmutatásért. Az Azure CLI használatával létrehozhat egy másik helyet ugyanazokkal a futásidővel. Másik lehetőségként a  Portál webalkalmazás panelén található Sablon exportálása gombbal exportálhatja a webhely ARM-sablonját. Ezt a sablont újra felhasználhatja új hely üzembe helyezéséhez ugyanazokkal a futásidővel és konfigurációval.

### <a name="limitations"></a>Korlátozások

- App Service on Linux tarifacsomag nem [](https://azure.microsoft.com/pricing/details/app-service/plans/) támogatott. 
- A Windows- és Linux-alkalmazásokat nem lehet ugyanabban a App Service vegyesen használni.  
- Korábban nem keverhet Windows- és Linux-alkalmazásokat ugyanabban az erőforráscsoportban. Azonban a 2021. január 21-én vagy azt követően létrehozott összes erőforráscsoport támogatja ezt a forgatókönyvet. A 2021. január 21. előtt létrehozott erőforráscsoportok esetében a vegyes platformon üzemelő példányok hozzáadásának lehetősége hamarosan elérhető lesz az Összes Azure-régióban (beleértve az országos felhőrégiókat is).
- Az Azure Portal csak a Linux-alkalmazásokhoz jelenleg használható funkciókat jeleníti meg. A funkciók engedélyezése után azok aktiválva vannak a portálon.
- A beépített rendszerképeken való üzembe helyezéskor a kód és a tartalom számára egy Azure Storage-alapú tárolókötetet rendel hozzá a webes tartalomhoz. A kötet lemezes késése nagyobb és változóbb, mint a tároló fájlrendszerének késése. Azok az alkalmazások, amelyek nagy írási jogosultságot igényelnek a tartalomfájlokhoz, hasznosak lehetnek az egyéni tároló lehetőségnek, amely a fájlokat a tartalomkötet helyett a tároló fájlrendszerében tárolja.

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
