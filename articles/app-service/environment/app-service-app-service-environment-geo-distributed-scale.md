---
title: Földrajzilag elosztott skála
description: Megtudhatja, hogyan méretezheti horizontálisan az alkalmazásokat Traffic Manager és App Service környezetekkel való geo-terjesztéssel.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833604"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzi alapú méretezés App Service-környezetekkel
## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A nagyon nagy léptéket igénylő alkalmazási forgatókönyvek meghaladják az alkalmazás egyetlen központi telepítéséhez elérhető számítási erőforrások kapacitását.  A szavazati alkalmazások, a sportesemények és a televíziós szórakoztatás eseményei a rendkívül nagy léptéket igénylő forgatókönyvekre mutatnak. A nagy léptékű követelmények teljesítése az alkalmazások horizontális felskálázásával hajtható végre. A szélsőséges terhelési követelmények kezelése érdekében számos alkalmazás-telepítés végezhető egyetlen régióban és régión belül.

App Service környezetek ideális platform horizontális felskálázáshoz. Az ismert kérelmek arányát támogató App Service Environment konfiguráció kiválasztása után a fejlesztők további App Service környezeteket helyezhetnek üzembe "cookie-vágó" módon, hogy elérjék a kívánt maximális terhelési kapacitást.

Tegyük fel például, hogy egy App Service Environment konfiguráción futó alkalmazást teszteltek a másodpercenkénti 20000 kérelmek kezelésére (RPS).  Ha a kívánt maximális terhelés 100 000 RPS, akkor öt (5) App Service környezet hozható létre és konfigurálható annak biztosításához, hogy az alkalmazás kezelni tudja a maximális tervezett terhelést.

Mivel az ügyfelek általában egyéni (vagy Vanity) tartománnyal férnek hozzá az alkalmazásokhoz, a fejlesztőknek az összes App Service Environment-példányon el kell osztaniuk az alkalmazásra vonatkozó kérelmeket.  Ennek a célnak a megvalósítása nagyszerű módja az egyéni tartomány feloldása [Azure Traffic Manager-profil][AzureTrafficManagerProfile]használatával.  A Traffic Manager profil úgy konfigurálható, hogy az összes egyéni App Service környezetben mutasson.  A Traffic Manager automatikusan kezeli az ügyfelek terjesztését az összes App Service-környezetben, a Traffic Manager profil terheléselosztási beállításai alapján.  Ez a módszer attól függetlenül működik, hogy az összes App Service környezet egyetlen Azure-régióban van-e telepítve, vagy világszerte több Azure-régión belül van-e telepítve.

Emellett mivel az ügyfelek a hiúság tartományon keresztül érik el az alkalmazásokat, az ügyfelek nem ismerik az alkalmazást futtató App Service környezetek számát.  Ennek eredményeképpen a fejlesztők gyorsan és egyszerűen hozzáadhatnak és eltávolíthatnak App Service környezeteket a megfigyelt forgalmi terhelés alapján.

Az alábbi elméleti ábrán egy adott régión belül három App Service környezetben horizontálisan skálázható alkalmazás látható.

![Fogalmi architektúra][ConceptualArchitecture] 

A témakör további részében ismertetjük a több App Service környezet használatával elosztott topológia beállításának lépéseit.

## <a name="planning-the-topology"></a>A topológia megtervezése
Mielőtt kiépít egy elosztott alkalmazás-lábnyomot, a rendszer segít néhány adat megkezdése előtt.

* **Egyéni tartomány az alkalmazáshoz:**  Mi az az Egyéni tartománynév, amelyet az ügyfelek használni fognak az alkalmazás eléréséhez?  A minta alkalmazás esetében az Egyéni tartománynév a következő: `www.scalableasedemo.com` .
* **Traffic Manager tartomány:** Adja meg a tartománynevet [Azure Traffic Manager-profil][AzureTrafficManagerProfile]létrehozásakor.  Ezt a nevet a rendszer a *trafficmanager.net* utótaggal kombinálva regisztrálja Traffic Manager által felügyelt tartományi bejegyzést.  A minta alkalmazás esetében a választott név a *skálázható – a bemutató*.  Ennek eredményeképpen a Traffic Manager által felügyelt teljes tartománynév *Scalable-ASE-demo.trafficmanager.net*.
* **Az alkalmazás helyigényének méretezésére szolgáló stratégia:**  Az alkalmazási lábnyom több App Service környezetbe kerül elosztásra egyetlen régióban?  Több régió?  Mindkét megközelítés kombinációja és megfeleltetése?  Az ügyfelek forgalmának helyétől, valamint az alkalmazás által támogatott háttérrendszer-infrastruktúrák teljes skálájának kihasználása.  Például egy 100%-os állapot nélküli alkalmazás esetében az alkalmazások nagymértékben méretezhetők az egyes Azure-régiók számos App Service környezetének kombinációjával, és a számos Azure-régióban üzembe helyezett App Service környezetek szorzatával.  A 15 és a globális Azure-régiók közül választhatnak, így az ügyfelek valóban az egész világra kiterjedő, Hyper-Scale alkalmazási lábnyomot hozhatnak létre.  A cikkhez használt minta alkalmazáshoz három App Service környezet lett létrehozva egyetlen Azure-régióban (az USA déli középső régiója).
* **A app Service környezetek elnevezési konvenciója:**  Minden App Service Environment egyedi nevet igényel.  Egy vagy két App Service környezeten kívül hasznos lehet elnevezési konvenciója az egyes App Service Environment azonosításához.  A minta alkalmazás esetében egyszerű elnevezési konvenciót használtak.  A három App Service környezet neve *fe1ase*, *fe2ase* és *fe3ase*.
* **Az alkalmazások elnevezési konvenciója:**  Mivel az alkalmazás több példánya is telepítve lesz, a központilag telepített alkalmazás minden példányához nevet kell megadni.  App Service környezetek egyik kevéssé ismert funkciója, hogy ugyanazt az alkalmazást több App Service környezetben is használhatja.  Mivel minden App Service Environment egyedi tartományi utótaggal rendelkezik, a fejlesztők úgy dönthetnek, hogy ugyanazt az alkalmazást használják az egyes környezetekben.  Előfordulhat például, hogy egy fejlesztőnek a következőképpen kell megneveznie az alkalmazásokat:  *MyApp.Foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.Foo3.p.azurewebsites.net* stb.  A minta alkalmazás esetében azonban minden alkalmazás példányának egyedi neve is van.  Az *webfrontend1*, a *webfrontend2* és a *webfrontend3* használt alkalmazás-példányok nevei.

## <a name="setting-up-the-traffic-manager-profile"></a>A Traffic Manager profil beállítása
Ha egy alkalmazás több példánya is telepítve van több App Service környezetbe, az egyes alkalmazás-példányok regisztrálva lehetnek a Traffic Managerban.  A minta alkalmazás esetében Traffic Manager profilra van szükség ahhoz, hogy a *Scalable-ASE-demo.trafficmanager.net* az ügyfeleket a következő telepített alkalmazás-példányok bármelyikére tudja irányítani:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Az első App Service Environment üzembe helyezett minta alkalmazás példánya.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  A második App Service Environment üzembe helyezett minta alkalmazás példánya.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  A harmadik App Service Environment üzembe helyezett minta alkalmazás példánya.

Több Azure App Service-végpont regisztrálásának legegyszerűbb módja, amely **ugyanabban** az Azure-régióban fut, a PowerShell- [Azure Resource Manager Traffic Manager támogatással][ARMTrafficManager].  

Első lépésként létre kell hoznia egy Azure Traffic Manager-profilt.  Az alábbi kód azt mutatja be, hogyan jött létre a profil a minta alkalmazáshoz:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Figyelje meg, hogyan lett beállítva a *RelativeDnsName* paraméter skálázható-beadási *-bemutatóra*.  Ez a paraméter azt eredményezi, hogy a tartománynév *Scalable-ASE-demo.trafficmanager.net* hozható létre, és társítva van egy Traffic Manager profilhoz.

A *TrafficRoutingMethod* paraméter határozza meg a terheléselosztási házirendet, Traffic Manager a segítségével határozza meg, hogy a rendszer hogyan terjessze az ügyfelek terhelését az összes rendelkezésre álló végpont között.  Ebben a példában a *súlyozott* metódus lett kiválasztva.  Ennek a lehetőségnek a megválasztása miatt a rendszer az összes regisztrált alkalmazás-végponton az egyes végpontokhoz társított relatív súlyok alapján fogja terjeszteni az ügyfelek kérelmeit. 

A létrehozott profillal a rendszer minden egyes alkalmazás-példányt natív Azure-végpontként ad hozzá a profilhoz.  A következő kód az egyes előtér-webalkalmazásokra mutató hivatkozásokat olvas be. Ezután hozzáadja az összes alkalmazást Traffic Manager végpontként a *targetresourceid azonosítója* paraméterrel.

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Figyelje meg, hogy az egyes alkalmazás-példányok esetében van *-e AzureTrafficManagerEndpointConfig-* hívás.  Az egyes PowerShell-parancsok *targetresourceid azonosítója* paramétere a három telepített alkalmazás egyik példányára hivatkozik.  A Traffic Manager profil a profilban regisztrált mindhárom végpont terhelését fogja osztani.

Mindhárom végpont ugyanazt az értéket (10) használja a *súlyozási* paraméterhez.  Ez a helyzet azt eredményezi, hogy Traffic Manager a három alkalmazás-példány között viszonylag egyenletesen terjeszti az ügyfelek kérelmeit. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás egyéni tartományának mutatása a Traffic Manager tartományban
Az utolsó lépés ahhoz szükséges, hogy az alkalmazás egyéni tartományát a Traffic Manager tartományban mutasson.  A minta alkalmazás esetében mutasson a `www.scalableasedemo.com` következőre: `scalable-ase-demo.trafficmanager.net` .  Hajtsa végre ezt a lépést az egyéni tartományt felügyelő tartományregisztrálónál.  

A regisztrátor tartományi felügyeleti eszközeinek használatával létre kell hoznia egy CNAME rekordot, amely az Traffic Manager tartományban lévő egyéni tartományt mutat.  Az alábbi képen látható egy példa arra, hogy a CNAME konfiguráció hogyan néz ki:

![CNAME egyéni tartományhoz][CNAMEforCustomDomain] 

Bár ez a témakör nem foglalkozik, ne feledje, hogy minden egyes alkalmazás-példánynak rendelkeznie kell az egyéni tartománnyal is.  Ellenkező esetben, ha egy kérelem egy alkalmazás-példányba kerül, és az alkalmazás nem regisztrálta az egyéni tartományt az alkalmazással, a kérelem sikertelen lesz.

Ebben a példában az egyéni tartomány a `www.scalableasedemo.com` , és minden alkalmazás-példányhoz hozzá van rendelve egy egyéni tartomány.

![Egyéni tartomány][CustomDomain] 

Az egyéni tartományok Azure App Service alkalmazásokkal való regisztrálásával kapcsolatos további információkért lásd: [Egyéni tartományok regisztrálása][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Az elosztott topológia kipróbálása
A Traffic Manager és a DNS konfigurációjának végeredménye az, hogy a kérelmek a `www.scalableasedemo.com` következő sorozatot követik majd át:

1. Egy böngésző vagy eszköz DNS-keresést végez a következőhöz: `www.scalableasedemo.com`
2. A tartományregisztrálónál lévő CNAME bejegyzés a DNS-címkeresés átirányítását okozza az Azure Traffic Manager.
3. A rendszer DNS-keresést végez az Azure Traffic Manager DNS-kiszolgálók egyikének *Scalable-ASE-demo.trafficmanager.net* .
4. A *TrafficRoutingMethod* paraméterben korábban meghatározott terheléselosztási házirend alapján Traffic Manager kiválasztja az egyik konfigurált végpontot. Ezután visszaadja a végpont teljes tartománynevét a böngészőnek vagy az eszköznek.
5. Mivel a végpont teljes tartományneve egy App Service Environment futó alkalmazás-példány URL-címe, a böngésző vagy az eszköz kérni fogja a Microsoft Azure DNS-kiszolgálót, hogy oldja fel a teljes tartománynevet az IP-címre. 
6. A böngésző vagy az eszköz elküldi a HTTP/S kérelmet az IP-címnek.  
7. A kérelem a App Service-környezetek egyikén futó alkalmazás-példányok egyikén fog megérkezni.

Az alábbi kép a minta alkalmazás egyéni tartományának DNS-keresését jeleníti meg. Sikerült feloldani egy olyan alkalmazás-példányra, amely a három minta App Service környezet egyikén fut (ebben az esetben a három App Service környezet közül a második):

![DNS-keresés][DNSLookup] 

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A PowerShell-Azure Resource Manager dokumentációja [Traffic Manager-támogatás][ARMTrafficManager].  

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
