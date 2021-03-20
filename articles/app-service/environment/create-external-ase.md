---
title: Külső bekészítés létrehozása
description: Megtudhatja, hogyan hozhat létre App Service környezetet egy alkalmazással, vagy létrehozhat egy önálló (üres) bevezetést.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92927447"
---
# <a name="create-an-external-app-service-environment"></a>Külső App Service-környezet létrehozása

Az Azure App Service Environment az Azure App Service egy olyan példánya, amelyet egy Azure virtuális hálózat alhálózatában helyeztek üzembe.

> [!NOTE]
> Minden App Service Environment rendelkezik egy virtuális IP-címmel (VIP), amely felhasználható a App Service Environment való kapcsolatfelvételre.

Az App Service Environment (ASE) üzembe helyezésének két módja van:

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- A virtuális IP-címet egy belső IP-címen, amelyet gyakran ILB-központnak neveznek, mivel a belső végpont egy belső Load Balancer (ILB).

Ez a cikk bemutatja, hogyan hozhat létre külső kiegészítőt. A kiegészítő funkció áttekintését lásd: [Bevezetés a app Service Environment][Intro]. A ILB-előkészítés létrehozásával kapcsolatos információkért lásd: ILB-bekészítés [létrehozása és használata][MakeILBASE].

## <a name="before-you-create-your-ase"></a>A bekészítés előtt

A bekészítés létrehozása után a következők nem módosíthatók:

- Hely
- Előfizetés
- Erőforráscsoport
- Használt VNet
- Használt alhálózat
- Alhálózat mérete

> [!NOTE]
> Ha kiválaszt egy VNet, és megad egy alhálózatot, győződjön meg arról, hogy elég nagy a jövőbeli növekedési és skálázási igények kielégítéséhez. A `/24` 256-es címmel rendelkező méretet javasoljuk.
>

## <a name="three-ways-to-create-an-ase"></a>Három módszer a bekészítés létrehozására

A következő három módon hozható létre:

- **App Service terv létrehozásakor**. Ez a módszer egyetlen lépésben hozza létre a központot és a App Service tervet.
- **Önálló műveletként**. Ez a módszer egy önálló beadási módszert hoz létre, amely egy nem megfelelő beadási csomag. Ez a módszer egy fejlettebb folyamat a beadási technológia létrehozásához. Ezzel létrehoz egy ILB-t tartalmazó szolgáltatót.
- **Egy Azure Resource Manager sablonból**. Ez a módszer speciális felhasználók számára érhető el. További információ: a bekészítés [létrehozása sablonból][MakeASEfromTemplate].

A külső kiegészítő szolgáltatás egy nyilvános virtuális IP-címmel rendelkezik, ami azt jelenti, hogy a központba tartozó alkalmazásokra irányuló összes HTTP/HTTPS-forgalom internetről elérhető IP-címet tartalmaz. Egy ILB rendelkező beadási pont IP-címmel rendelkezik a szolgáltató által használt alhálózatból. Az egy ILB-beosztásban üzemeltetett alkalmazások nem jelennek meg közvetlenül az interneten.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>BeApp Servicei csomag létrehozása

Az App Service terv az alkalmazások tárolója. Amikor App Serviceban hoz létre alkalmazást, válasszon ki vagy hozzon létre egy App Service tervet. App Service környezetek App Service terveket tartanak fenn, és a App Service csomagok megtartják az alkalmazásokat.

A beApp Servicei csomag létrehozásakor hozzon létre egy kiegészítő csomagot:

1. A [Azure Portal](https://portal.azure.com/)válassza az **erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás** lehetőséget.

    ![Képernyőfelvétel: az Azure Marketplace-en kiválasztott Web és mobil kiválasztott Azure Portal és a jobb oldalon megnyitott új webalkalmazás létrehozásának képernyője.][1]

2. Válassza ki előfizetését. Az alkalmazás és a kiegészítő szolgáltatás ugyanabban az előfizetésben jön létre.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok segítségével a kapcsolódó Azure-erőforrásokat egységként kezelheti. Az erőforráscsoportok akkor is hasznosak, ha Role-Based Access Control szabályokat hoz létre az alkalmazásaihoz. További információért lásd [az Azure Resource Manager áttekintését][ARMOverview].

4. Válassza ki az operációs rendszerét (Windows, Linux vagy Docker). 

5. Válassza ki a App Service tervet, majd válassza az **új létrehozása** lehetőséget. A Linux Web Apps és a Windows Web Apps nem lehet ugyanabban a App Service csomagban, de ugyanabban a App Service Environmentban lehet. 

    ![Képernyőfelvétel: a webalkalmazás panelt, az App Service-csomag ablaktábláját és az új App Service terv panelt bemutató Azure Portal.][2]

6. A **hely** legördülő listában válassza ki azt a régiót, ahol létre kívánja hozni a központot. Ha kijelöl egy meglévő beadási lehetőséget, akkor a rendszer nem hoz létre új kiegészítőt. A App Service tervet a kiválasztott szakszolgáltatásban hozza létre a rendszer. 

7. Válassza ki az **árképzési szintet**, és válasszon az **elkülönített** díjszabási SKU közül. Ha egy **elkülönített** SKU-kártyát és egy olyan helyet választ, amely nem a betekintő, akkor az adott helyen létrejön egy új kiegészítő szolgáltatás. A létrehozási folyamat elindításához válassza a **kiválasztás** lehetőséget. Az **elkülönített** SKU csak a központtal együtt érhető el. Nem használhat más díjszabási SKU-t az **elkülönített** kiegészítő csomagon kívül is. 

    ![Árképzési szintek kiválasztása][3]

8. Adja meg a beadás nevét. Ez a név az alkalmazások címezhető nevében használatos. Ha a _appsvcenvdemo_ neve, a tartománynév a *. appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy *mytestapp* nevű alkalmazást, a címe a következő címen található: mytestapp.appsvcenvdemo.p.azurewebsites.net. Nem használhat szóközt a névben. Nagybetűs karakterek használata esetén a tartománynév a név teljes kisbetűs verziója.

    ![Új App Service csomag neve][4]

9. Adja meg az Azure-beli virtuális hálózatkezelés részleteit. Válassza az **új létrehozása** vagy a **meglévő kiválasztása** lehetőséget. A meglévő VNet kiválasztásának lehetősége csak akkor érhető el, ha a kiválasztott régió VNet rendelkezik. Ha az **új létrehozása** lehetőséget választja, adja meg a VNet nevét. Létrejön egy új Resource Manager-VNet ezzel a névvel. A kiválasztott régióban lévő címterület használatával működik `192.168.250.0/23` . Ha a **meglévő kiválasztása** lehetőséget választja, a következőket kell tennie:

    a. Ha egynél többre van szüksége, válassza ki a VNet.

    b. Adja meg az új alhálózat nevét.

    c. Válassza ki az alhálózat méretét. *Ne feledje, hogy elég nagy méretűre kell kiválasztania, hogy megfeleljen a beadásának jövőbeli növekedésének.* Azt javasoljuk `/24` , hogy a 256-es címekkel rendelkezik, és képes legyen a maximális méretű betekintő szolgáltatás kezelésére. Nem ajánlott `/28` például, mert csak 16 cím érhető el. Az infrastruktúra legalább hét címet használ, és az Azure Networking egy másik 5-öt használ. Egy `/28` alhálózatban marad a 4 app Service-es csomag-példányok, amelyek egy külső BEILBnek, és mindössze 3 app Service megtervezik az előfizetést.

    d. Válassza ki az alhálózat IP-tartományát.

10. Válassza a **Létrehozás** lehetőséget a beadás létrehozásához. Ez a folyamat létrehozza a App Service tervet és az alkalmazást is. A beApp Servicei csomag és az alkalmazás mind ugyanahhoz az előfizetéshez, sem ugyanahhoz az erőforráshoz tartozik. Ha a szolgáltatónak külön erőforráscsoportot kell használnia, vagy ha ILB-beadásra van szüksége, kövesse a következő lépéseket: a beadási folyamat létrehozása önmagával.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Az egyéni Docker-rendszerkép együttes használatával létrehozhat egy beadási és egy linuxos webalkalmazást

1. A [Azure Portal](https://portal.azure.com/) **hozzon létre egy erőforrást**  >  **web és mobil**  >  **Web App for containers.** 

    ![Képernyőkép a Azure Portalről, amely az Azure Marketplace-en kiválasztott Web és mobil, a jobb oldalon pedig a Web App for Containers panelt jeleníti meg.][7]

1. Válassza ki előfizetését. Az alkalmazás és a kiegészítő szolgáltatás ugyanabban az előfizetésben jön létre.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok segítségével a kapcsolódó Azure-erőforrásokat egységként kezelheti. Az erőforráscsoportok akkor is hasznosak, ha Role-Based Access Control szabályokat hoz létre az alkalmazásaihoz. További információért lásd [az Azure Resource Manager áttekintését][ARMOverview].

1. Válassza ki a App Service tervet, majd válassza az **új létrehozása** lehetőséget. A Linux Web Apps és a Windows Web Apps nem lehet ugyanabban a App Service csomagban, de ugyanabban a App Service Environmentban lehet. 

    ![Képernyőkép a Web App for Containers ablaktáblát, az App Service-csomag ablaktáblát és az új App Servicei terv panelt megjelenítő Azure Portal.][8]

1. A **hely** legördülő listában válassza ki azt a régiót, ahol létre kívánja hozni a központot. Ha kijelöl egy meglévő beadási lehetőséget, akkor a rendszer nem hoz létre új kiegészítőt. A App Service tervet a kiválasztott szakszolgáltatásban hozza létre a rendszer. 

1. Válassza ki az **árképzési szintet**, és válasszon az **elkülönített** díjszabási SKU közül. Ha egy **elkülönített** SKU-kártyát és egy olyan helyet választ, amely nem a betekintő, akkor az adott helyen létrejön egy új kiegészítő szolgáltatás. A létrehozási folyamat elindításához válassza a **kiválasztás** lehetőséget. Az **elkülönített** SKU csak a központtal együtt érhető el. Nem használhat más díjszabási SKU-t az **elkülönített** kiegészítő csomagon kívül is. 

    ![Árképzési szintek kiválasztása][3]

1. Adja meg a beadás nevét. Ez a név az alkalmazások címezhető nevében használatos. Ha a _appsvcenvdemo_ neve, a tartománynév a *. appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy *mytestapp* nevű alkalmazást, a címe a következő címen található: mytestapp.appsvcenvdemo.p.azurewebsites.net. Nem használhat szóközt a névben. Nagybetűs karakterek használata esetén a tartománynév a név teljes kisbetűs verziója.

    ![Új App Service csomag neve][4]

1. Adja meg az Azure-beli virtuális hálózatkezelés részleteit. Válassza az **új létrehozása** vagy a **meglévő kiválasztása** lehetőséget. A meglévő VNet kiválasztásának lehetősége csak akkor érhető el, ha a kiválasztott régió VNet rendelkezik. Ha az **új létrehozása** lehetőséget választja, adja meg a VNet nevét. Létrejön egy új Resource Manager-VNet ezzel a névvel. A kiválasztott régióban lévő címterület használatával működik `192.168.250.0/23` . Ha a **meglévő kiválasztása** lehetőséget választja, a következőket kell tennie:

    a. Ha egynél többre van szüksége, válassza ki a VNet.

    b. Adja meg az új alhálózat nevét.

    c. Válassza ki az alhálózat méretét. *Ne feledje, hogy elég nagy méretűre kell kiválasztania, hogy megfeleljen a beadásának jövőbeli növekedésének.* Azt javasoljuk `/24` , hogy a 128-es címekkel rendelkezik, és képes legyen a maximális méretű betekintő szolgáltatás kezelésére. Nem ajánlott `/28` például, mert csak 16 cím érhető el. Az infrastruktúra legalább hét címet használ, és az Azure Networking egy másik 5-öt használ. Egy `/28` alhálózatban marad a 4 app Service-es csomag-példányok, amelyek egy külső BEILBnek, és mindössze 3 app Service megtervezik az előfizetést.

    d. Válassza ki az alhálózat IP-tartományát.

1.  Válassza a "tároló konfigurálása" lehetőséget.
    * Adja meg az egyéni rendszerkép nevét (a Azure Container Registry, a Docker hub és a saját privát beállításjegyzék segítségével). Ha nem szeretné használni a saját egyéni tárolóját, egyszerűen használhatja a programkódot, és használhat egy beépített rendszerképet a Linuxon App Service a fenti utasítások használatával. 

    ![Tároló konfigurálása][9]

1. Válassza a **Létrehozás** lehetőséget a beadás létrehozásához. Ez a folyamat létrehozza a App Service tervet és az alkalmazást is. A beApp Servicei csomag és az alkalmazás mind ugyanahhoz az előfizetéshez, sem ugyanahhoz az erőforráshoz tartozik. Ha a szolgáltatónak külön erőforráscsoportot kell használnia, vagy ha ILB-beadásra van szüksége, kövesse a következő lépéseket: a beadási folyamat létrehozása önmagával.


## <a name="create-an-ase-by-itself"></a>Önmagát önmagát létrehozó szolgáltató létrehozása

Ha önálló kisegítő lehetőséget hoz létre, azzal semmi sincs benne. Egy üres bevezetési szolgáltatás továbbra is havi díjat számít fel az infrastruktúra számára. Kövesse az alábbi lépéseket egy ILB létrehozásához, vagy egy saját erőforráscsoport létrehozásához. A szolgáltató létrehozása után a normál folyamat használatával létrehozhat alkalmazásokat. Válassza ki az új beadási helyet.

1. Keresse meg **app Service Environment** az Azure Marketplace piactéren, vagy válassza az **erőforrás létrehozása**  >  **web Mobile**  >  **app Service Environment** lehetőséget. 

1. Adja meg a beadás nevét. A rendszer ezt a nevet használja a központhoz létrehozott alkalmazásokhoz. Ha a név *mynewdemoase*, az altartomány neve: *. mynewdemoase.p.azurewebsites.net*. Ha létrehoz egy *mytestapp* nevű alkalmazást, a címe a következő címen található: mytestapp.mynewdemoase.p.azurewebsites.net. Nem használhat szóközt a névben. Nagybetűs karakterek használata esetén a tartománynév a név teljes kisbetűs verziója. Ha ILB használ, a beléptetési név nem használatos az altartományban, hanem explicit módon meg van határozva a központilag történő létrehozás során.

    ![Beosztási elnevezés][5]

1. Válassza ki előfizetését. Ez az előfizetés egyben az is, amelyet a központhoz tartozó összes alkalmazás használ. Egy másik előfizetésben található VNet nem helyezheti üzembe a központot.

1. Válasszon ki vagy adjon meg egy új erőforráscsoportot. A VNet használt erőforráscsoportot meg kell egyeznie a saját előfizetésével. Ha kiválaszt egy meglévő VNet, a rendszer frissíti a beadási csoport kiválasztott erőforrását, hogy tükrözze a VNet. *Ha Resource Manager-sablont használ, létrehozhat egy olyan erőforráscsoportot, amely nem azonos a VNet erőforráscsoporthoz.* Ha egy sablonból szeretne létrehozni egy előkészítő-t, tekintse meg a [app Service környezet sablonból][MakeASEfromTemplate]való létrehozását ismertető témakört.

    ![Erőforráscsoport kiválasztása][6]

1. Válassza ki a VNet és a helyet. Létrehozhat egy új VNet, vagy kiválaszthat egy meglévő VNet: 

    * Új virtuális hálózat kiválasztása esetén új nevet és helyet adhat meg. 
    
    * Az új VNet a 192.168.250.0/23 címtartomány és az alapértelmezett nevű alhálózat szerepel. Az alhálózat 192.168.250.0/24-ként van definiálva. Csak Resource Manager-VNet választhat. A virtuális IP-cím **típusának** meghatározása meghatározza, hogy a központilag elérhető-e közvetlenül az internetről (külső), vagy ha ILB használ. További információ ezekről a lehetőségekről: [belső terheléselosztó létrehozása és használata app Service környezettel][MakeILBASE]. 

      * Ha a **VIP típushoz** a **külső** lehetőséget választja, kiválaszthatja, hogy a rendszer hány külső IP-címet hozzon létre az IP-alapú SSL-célokra. 
    
      * Ha a **VIP-típushoz** a **belső** lehetőséget választja, meg kell adnia azt a tartományt, amelyet a benyújtó használ. Központilag is üzembe helyezhet egy olyan VNet, amely nyilvános vagy privát címtartományt használ. Ha nyilvános címtartományt használó VNet szeretne használni, akkor előre létre kell hoznia a VNet. 
    
    * Ha kiválaszt egy meglévő VNet, akkor létrejön egy új alhálózat, amikor létrejön a beadási pont. *A portálon nem használhat előre létrehozott alhálózatot. Ha Resource Manager-sablont használ, létrehozhat egy meglévő alhálózattal rendelkező bevezetőt is.* Ha egy sablonból szeretne létrehozni egy előkészítő-t, olvassa el a [app Service Environment sablonból][MakeASEfromTemplate]való létrehozását ismertető témakört.

## <a name="app-service-environment-v1"></a>App Service-környezet v1

Továbbra is létrehozhatja a App Service Environment (ASEv1) első verziójának példányait. A folyamat elindításához keressen a piactéren **app Service Environment v1**. A bevezetőt ugyanúgy hozza létre, mint az önálló központot. Ha elkészült, a ASEv1 két előtérrel és két feldolgozóval rendelkezik. A ASEv1 az előtér-és a feldolgozókat kell kezelnie. Ezeket a rendszer nem adja hozzá automatikusan a App Service csomagok létrehozásakor. A kezelőfelületek HTTP/HTTPS-végpontként működnek, és a feldolgozóknak küldenek forgalmat. A feldolgozók az alkalmazásokat üzemeltető szerepkörök. Az előfizetések mennyiségét és a feldolgozókat a bekészítés után módosíthatja. 

További információ a ASEv1: [a app Service Environment v1 bemutatása][ASEv1Intro]. A ASEv1 méretezésével, kezelésével és figyelésével kapcsolatos további információkért lásd: [app Service Environment konfigurálása][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
