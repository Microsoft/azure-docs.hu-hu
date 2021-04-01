---
title: Bevezetés
description: Ismerje meg, hogy Azure App Service környezetek hogyan segítik az alkalmazások méretezését, védelmét és optimalizálását egy teljesen elkülönített és dedikált környezetben.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92631138"
---
# <a name="introduction-to-the-app-service-environments"></a>Az App Service Environment bemutatása #
## <a name="overview"></a>Áttekintés ##

Az Azure App Service Environment egy Azure App Service-funkció, amely teljesen elkülönített és dedikált környezetet biztosít az App Service-alkalmazások biztonságos, nagy léptékű futtatásához. Ez a szolgáltatás a következők üzemeltetésére képes:

* Windows-webalkalmazások
* Linux-webalkalmazások 
* Docker-tárolók
* Mobilalkalmazások
* Functions

Az App Service Environment (ASE) a következő igényekkel rendelkező összes alkalmazási számítási feladat elvégzésére használható:

* Nagyon nagy skálázhatóság.
* Elkülönítés és biztonságos hálózati hozzáférés.
* Magas memóriakihasználtság.

Egy vagy több Azure-régión belül több ASE létrehozásának lehetősége az ügyfelek számára. Ez a rugalmasság a ASE ideális megoldást kínál az állapot nélküli, nagy mennyiségű kérelmek másodpercenkénti (RPS) számítási feladatainak támogatásához.

A ASE csak egy ügyfélről, az egyik virtuális hálózatok. Az ügyfelek teljes mértékben szabályozhatják az alkalmazás bejövő és kimenő hálózati adatforgalmát. Az alkalmazások nagy sebességű, biztonságos VPN-kapcsolatokat létesíthetnek a helyszíni vállalati erőforrásokkal.

* Az ASE saját tarifacsomaggal rendelkezik. Ismerje meg, hogyan segíti elő az [elkülönített ajánlat](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) a nagy léptékű skálázását és a biztonságot.
* Az [App Service-környezetek (v2)](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) egy olyan környezetet biztosítanak, amely védi a hálózat egy alhálózatán található alkalmazásokat, és egy saját, privát Azure App Service üzemelő példányt nyújt.
* Több ASE is felhasználható a horizontális skálázásra. További információkért lásd a [földrajzilag elosztott alkalmazás beállítását](app-service-app-service-environment-geo-distributed-scale.md) ismertető részt.
* Az ASE környezetek használatával a biztonsági architektúra is konfigurálható, ahogyan azt az AzureCon Deep Dive is bemutatja. Az AzureCon Deep Dive-ban látható biztonsági architektúra konfigurálásáról a [rétegelt biztonsági architektúra App Service Environmenttel történő megvalósításáról szóló cikkben](app-service-app-service-environment-layered-security.md) találhat további információkat.
* Az ASE környezetekben futó alkalmazások hozzáférésében sorompós kapcsolatok alakíthatók ki alsóbb rétegbeli eszközök, például webalkalmazás-tűzfalak (WAF-ok) segítségével. További információ: [Webalkalmazás-tűzfal (WAF)][AppGW].
* App Service környezetek a Availability Zones (AZ) rendszerbe helyezhetők üzembe a zóna-rögzítés használatával.  További részletekért tekintse meg a [Availability Zones app Service Environment támogatását][ASEAZ] ismertető témakört.

## <a name="dedicated-environment"></a>Dedikált környezet ##

Az ASE kizárólag egyetlen előfizetéshez van dedikálva, és akár 100 App Service-csomagpéldány üzemeltetésére is képes. A tartomány akár 100 példányt is magában foglalhat egyetlen App Service-csomag keretében, vagy 100 egypéldányos App Service-csomagot, illetve ezek között bármennyit.

Egy ASE előtérrendszerekből és feldolgozókból áll. Az előtérrendszerek a HTTP/HTTPS-végződtetésért, valamint az alkalmazáskérések egy ASE-n belüli automatikus terheléselosztásáért felelősek. Az előtérrendszerek az ASE App Service-csomagjainak felskálázásakor automatikusan hozzáadódnak.

A feldolgozók az ügyfélalkalmazások üzemeltetéséért felelős szerepkörök. A feldolgozók három állandó méretben érhetők el:

* Egy vCPU/3,5 GB RAM
* Két vCPU/7 GB RAM
* Négy vCPU/14 GB RAM

Az ügyfeleknek nem kell foglalkozniuk az előtérrendszerek és a feldolgozók kezelésével. Amikor az ügyfelek horizontálisan felskálázzák az App Service-csomagjaikat, az infrastruktúra automatikusan hozzáadódik. Mivel az App Service-csomagok egy ASE-n belül vannak létrehozva vagy skálázva, ezért a szükséges infrastruktúra a helyzetnek megfelelően adható hozzá vagy távolítható el.

Az ASE átalányalapú havidíja fedezi az infrastruktúra költségét, és nem változik az ASE méretével. A további díjakat az App Service-csomagok vCPU-inak száma határozza meg. Egy ASE környezeten belül az összes üzemeltetett alkalmazás az elkülönített díjszabású termékváltozatba tartozik. Az ASE környezetek árképzéséről az [App Service díjszabása][Pricing] oldalon, az ASE környezetek elérhető díjszabási lehetőségei alatt találhat információkat.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása ##

A bevezetési funkció a Azure App Service közvetlen üzembe helyezése az ügyfél Azure Resource Manager virtuális hálózatában. Az Azure virtuális hálózatairól további információt az [Azure virtuális hálózatok GYIK](../../virtual-network/virtual-networks-faq.md) dokumentumában talál. Az ASE mindig egy virtuális hálózaton belül, pontosabban a virtuális hálózat alhálózatán működik. A virtuális hálózatok biztonsági funkciói segítségével szabályozhatja az alkalmazásai bejövő és kimenő hálózati kommunikációját.

Az ASE lehet internetre irányuló, nyilvános IP-címmel, vagy befelé irányuló, Azure belső terheléselosztási (ILB) címmel.

A [hálózati biztonsági csoportok][NSGs] korlátozzák az ASE alhálózatára beérkező hálózati kommunikációt. A hálózati biztonsági csoportok használatával futtathatja az alkalmazásait alsóbb rétegbeli eszközök és szolgáltatások, valamint WAF-ok és hálózati SaaS-szolgáltatók mögött.

Az alkalmazásoknak gyakran kell hozzáférniük vállalati erőforrásokhoz, például belső adatbázisokhoz vagy webes szolgáltatásokhoz. Ha olyan virtuális hálózatban telepíti az ASE környezetet, amely VPN-kapcsolatban van a helyszíni hálózattal, akkor az ASE környezeten belüli alkalmazások hozzáférhetnek a helyszíni erőforrásokhoz. Ez mind a [helyek közötti](../../vpn-gateway/vpn-gateway-multi-site.md), mind az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) VPN-kapcsolatok esetében igaz.

Az ASE-k virtuális és helyszíni hálózatokkal történő használatáról az [App Service Environment hálózati szempontjai][ASENetwork] részben találhat további információkat.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##

Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat. 

Az ASEv1 esetén minden erőforrást manuálisan kell felügyelnie. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. Mielőtt horizontálisan felskálázza az App Service-csomagot, először a feldolgozók készletét kell felskáláznia oda, ahol üzemeltetni szeretné azt.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ez tartalmazza azokat az előtérrendszerekhez és a feldolgozókhoz tartozó vCPU-kat, amelyek nem üzemeltetnek számítási feladatokat. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
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
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html