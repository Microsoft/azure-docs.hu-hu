---
title: Földrajzi eloszlású skálán
description: Megtudhatja, hogyan méretelhet horizontálisan alkalmazásokat földrajzi elosztással, Traffic Manager és App Service környezetekkel.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834299"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzi alapú méretezés App Service-környezetekkel
## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A nagyon nagy méretezést igénylő alkalmazásforgatókönyvek meghaladhatják az alkalmazás egyetlen üzemelő példánya számára elérhető számítási erőforrás-kapacitást.  A szavazóalkalmazások, sportesemények és a televisált szórakoztató események mind példaként szolgálnak olyan forgatókönyvekre, amelyek rendkívül nagy méretezést igényelnek. A nagy léptékű követelmények az alkalmazások horizontális skálázása által teljesülnek. A szélsőséges terhelési követelmények kezeléséhez számos alkalmazástelepítést lehet végrehajtani egyetlen régión belül és régiók között.

App Service környezetek ideális platformot kínálnak a horizontális felskáláshoz. Miután kiválasztott egy App Service Environment-konfigurációt, amely támogatja az ismert kérelemarányt, a fejlesztők további App Service-környezeteket helyezhetnek üzembe "cookie-ás" módon a kívánt maximális terhelési kapacitás eléréséhez.

Tegyük fel például, hogy egy App Service Environment futtatott alkalmazást teszteltek másodpercenként 20 000 kérelem (RPS) kezeléséhez.  Ha a maximális terhelési kapacitás 100 000 RPS, öt (5) App Service környezetet lehet létrehozni és konfigurálni, hogy az alkalmazás képes legyen kezelni a maximális tervezett terhelést.

Mivel az ügyfelek általában egyéni (vagy egyéni) tartomány használatával férnek hozzá az alkalmazásokhoz, a fejlesztőknek szükségük van egy módszerre, amely elosztja az alkalmazáskéréseket az összes App Service Environment példány között.  Ennek a célnak a eléréséhez egy egyéni tartományt kell feloldani egy Azure Traffic Manager [profillal.][AzureTrafficManagerProfile]  A Traffic Manager profil konfigurálható úgy, hogy az összes egyéni vagy App Service mutasson.  Traffic Manager a rendszer automatikusan elosztja az ügyfeleket az összes App Service-környezetben a terheléselosztási beállítások alapján a Traffic Manager alapján.  Ez a megközelítés attól függetlenül működik, hogy az összes App Service környezet egyetlen Azure-régióban vagy világszerte több Azure-régióban van üzembe stb.

Továbbá, mivel az ügyfelek a hiússág tartományon keresztül férnek hozzá az alkalmazásokhoz, az ügyfelek nem App Service az alkalmazást futtató környezetek számát.  Ennek eredményeképpen a fejlesztők gyorsan és egyszerűen adhatnak hozzá és távolíthatnak el App Service környezeteket a megfigyelt forgalmi terhelés alapján.

Az alábbi fogalmi diagram egy alkalmazást ábrázol horizontálisan felskálzva három App Service környezetben egyetlen régión belül.

![Fogalmi architektúra][ConceptualArchitecture] 

A témakör további részében végigvezetjük a mintaalkalmazás elosztott topológiája több különböző környezetben való beállításának App Service lépéseit.

## <a name="planning-the-topology"></a>A topológia megtervezése
Az elosztott alkalmazásigény kiépítése előtt segíthet, ha előre rendelkezésre áll néhány információ.

* **Az alkalmazás egyéni tartománya:**  Mi az az egyéni tartománynév, amely alapján az ügyfelek hozzáférhetnek az alkalmazáshoz?  A mintaalkalmazás esetében az egyéni tartománynév `www.scalableasedemo.com` a következő: .
* **Traffic Manager tartomány:** Válasszon egy tartománynevet a Azure Traffic Manager [létrehozásakor.][AzureTrafficManagerProfile]  Ezt a nevet a  rendszer a trafficmanager.net utótaggal kombinálva regisztrál egy tartománybejegyzést, amelyet a Traffic Manager.  A mintaalkalmazás esetében a kiválasztott név *a következő: scalable-ase-demo.*  Ennek eredményeképpen a felhasználó által felügyelt teljes tartománynév Traffic Manager lesz *scalable-ase-demo.trafficmanager.net.*
* **Az alkalmazáslábnyom skálázhatóságának stratégiája:**  Az alkalmazásigény egyetlen régióban lesz elosztva több App Service környezet között?  Több régió?  A két megközelítés vegyesen illeszkedik?  A döntést az ügyfélforgalom származási helyével kapcsolatos elvárásokra alapozhatja, és hogy az alkalmazás támogató háttérinfra infrastruktúrájának többi része milyen mértékben méretezhető.  Egy 100%-os állapot nélküli alkalmazás esetén például az alkalmazások nagymértékben skálázhatóak az egyes Azure-régiókban található számos App Service-környezet kombinációjával, szorozva a számos Azure-régióban üzembe helyezett App Service-környezetekkel.  Most, hogy több mint 15 globális Azure-régió közül választhat, az ügyfelek teljes körű, nagy méretre kiterjedő alkalmazásigényt építenek ki.  A cikkben használt mintaalkalmazáshoz három App Service környezet jött létre egyetlen Azure-régióban (USA déli középső régiója).
* **Elnevezési konvenció a App Service környezetekhez:**  Minden App Service Environment egyedi névre van szükség.  Egy-két App Service környezeten túl hasznos lehet egy elnevezési konvenció, amely segít azonosítani az App Service Environment.  A mintaalkalmazáshoz egy egyszerű elnevezési konvenciót használtunk.  A három új környezet neve App Service a *fe1ase,* *a fe2ase* és *a fe3ase.*
* **Elnevezési konvenció az alkalmazásokhoz:**  Mivel a rendszer az alkalmazás több példányát is üzembe fogja helyezni, az üzembe helyezett alkalmazás minden példányához szükség lesz egy névre.  A környezetek egyik ismert, de kényelmes funkciója App Service hogy ugyanazt az alkalmazásnevet több különböző környezetben App Service is.  Mivel minden App Service Environment egyedi tartomány-utótaggal rendelkezik, a fejlesztők dönthetnek úgy, hogy ugyanazt az alkalmazásnevet használják újra minden környezetben.  Egy fejlesztő például a következőképpen nevezheti el az alkalmazásokat:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, stb.  A mintaalkalmazás esetében azonban minden alkalmazáspéldány egyedi névvel rendelkezik.  Az alkalmazáspéldányok nevei: *webfrontend1,* *webfrontend2* és *webfrontend3.*

## <a name="setting-up-the-traffic-manager-profile"></a>A profil Traffic Manager beállítása
Miután egy alkalmazás több példánya is üzembe App Service környezetben, az egyes alkalmazáspéldányok regisztrálhatóak a Traffic Manager.  A mintaalkalmazáshoz szükség van egy Traffic Manager  profilra a scalable-ase-demo.trafficmanager.net, amely az alábbi telepített alkalmazáspéldányok bármelyikéhez irányíthatja az ügyfeleket:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Az első virtuális gépre telepített mintaalkalmazás App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  A mintaalkalmazás egy példánya, amely a második App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  A mintaalkalmazás egy példánya, amely a harmadik App Service Environment.

Több, ugyanabban az Azure-régióban Azure App Service végpont regisztrálásának  legegyszerűbb módja a PowerShell és [Azure Resource Manager Traffic Manager támogatása.][ARMTrafficManager]  

Az első lépés egy új Azure Traffic Manager létrehozása.  Az alábbi kód bemutatja, hogyan jött létre a profil a mintaalkalmazáshoz:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Figyelje meg, hogy *a RelativeDnsName* paraméter *scalable-ase-demo lett.*  Ez a paraméter a *tartománynév* scalable-ase-demo.trafficmanager.net létre, és társítva lesz egy Traffic Manager profilhoz.

A *TrafficRoutingMethod* paraméter határozza meg azt a terheléselosztási szabályzatot, Traffic Manager az ügyfélterhelés az összes elérhető végpont között való eloszlása érdekében.  Ebben a példában *a Súlyozott* metódust választottuk.  A választás miatt az ügyfélkérések az összes regisztrált alkalmazásvégpont között el lesznek osva az egyes végpontokkal társított relatív súlyok alapján. 

A profil létrehozása után minden alkalmazáspéldány natív Azure-végpontként lesz hozzáadva a profilhoz.  Az alábbi kód lekér egy hivatkozást az egyes előoldali webalkalmazások számára. Ezután hozzáadja az egyes alkalmazásokat Traffic Manager végpontként a *TargetResourceId paraméterrel.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Figyelje meg, hogy minden egyes alkalmazáspéldányhoz egy hívás van az *Add-AzureTrafficManagerEndpointConfig* számára.  Az egyes PowerShell-parancsok *TargetResourceId* paramétere a három üzembe helyezett alkalmazáspéldány egyikének referenciája.  A Traffic Manager profil a profilban regisztrált mindhárom végpont között el fogja terjeszteni a terhelést.

Mindhárom végpont ugyanazt az értéket (10) használja a *Weight paraméterhez.*  Ez a helyzet azt Traffic Manager, hogy az ügyfélkérések viszonylag egyenletesen vannak elterjesztve mindhárom alkalmazáspéldány között. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás Custom Domain mutat a Traffic Manager tartományra
Az utolsó lépés az alkalmazás egyéni tartományának a tartományra Traffic Manager mutat.  A mintaalkalmazás esetében mutasson a `www.scalableasedemo.com` következőre: `scalable-ase-demo.trafficmanager.net` .  Ezt a lépést az egyéni tartományt felügyelő tartományregisztrálónál kell végrehajtania.  

A regisztráló tartománykezelő eszközeinek használatával létre kell Traffic Manager CNAME rekordokat.  Az alábbi képen egy példa látható arra, hogyan néz ki ez a CNAME-konfiguráció:

![CNAME a Custom Domain][CNAMEforCustomDomain] 

Bár ebben a témakörben nem ismertetjük, ne feledje, hogy minden egyes alkalmazáspéldányhoz az egyéni tartományt is regisztrálni kell.  Ellenkező esetben, ha egy kérés egy alkalmazáspéldányhoz kéri, és az alkalmazás nem regisztrálta az egyéni tartományt az alkalmazásban, a kérés sikertelen lesz.

Ebben a példában az egyéni tartomány , és `www.scalableasedemo.com` minden alkalmazáspéldányhoz tartozik egy egyéni tartomány.

![Egyéni tartomány][CustomDomain] 

Az egyéni tartományok alkalmazásokkal való regisztrálásának Azure App Service lásd: [Egyéni tartományok regisztrálása.][RegisterCustomDomain]

## <a name="trying-out-the-distributed-topology"></a>Az elosztott topológia kipróbálása
A lekérdezési és DNS-Traffic Manager végeredménye az, hogy a kérései a `www.scalableasedemo.com` következő sorrendben fognak áthaladni:

1. Egy böngésző vagy eszköz DNS-kereséseket fog `www.scalableasedemo.com`
2. A tartományregisztráló CNAME bejegyzése a DNS-keresés átirányítását okozza a Azure Traffic Manager.
3. Dns-keresés készül az  scalable-ase-demo.trafficmanager.net DNS-kiszolgálók egyikének Azure Traffic Manager kereséséhez.
4. A *TrafficRoutingMethod* paraméterben korábban megadott terheléselosztási szabályzat alapján a Traffic Manager kiválasztja az egyik konfigurált végpontot. Ezután visszaadja a végpont FQDN-jét a böngészőnek vagy az eszköznek.
5. Mivel a végpont FQDN-je egy App Service Environment-on futó alkalmazáspéldány URL-címe, a böngésző vagy az eszköz megkér egy Microsoft Azure DNS-kiszolgálót, hogy oldja fel a teljes tartománynevét egy IP-címre. 
6. A böngésző vagy az eszköz elküldi a HTTP/S kérést az IP-címre.  
7. A kérés az egyik új környezetben futó egyik alkalmazáspéldányra App Service érkezik.

Az alábbi konzolkép a mintaalkalmazás egyéni tartományának DNS-keresését mutatja. Sikeresen felold egy alkalmazáspéldányt, amely a három App Service-környezet egyikében fut (ebben az esetben a három App Service közül a másodikon):

![DNS-keresés][DNSLookup] 

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A PowerShell-Azure Resource Manager Traffic Manager [dokumentációja.][ARMTrafficManager]  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
