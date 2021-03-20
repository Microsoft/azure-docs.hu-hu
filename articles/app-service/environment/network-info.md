---
title: Hálózati megfontolások
description: Ismerkedjen meg a bevezető hálózati forgalommal, valamint a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak beállításával a bevezetővel.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997341"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service-környezet hálózati megfontolásai #

## <a name="overview"></a>Áttekintés ##

 Az Azure [app Service Environment][Intro] a Azure app Service üzembe helyezése az Azure Virtual Network (VNet) egyik alhálózatában. Egy App Service-környezethez két központi telepítési típus létezik:

- **Külső** kisegítő lehetőség: elérhetővé teszi a beadási szolgáltatás által üzemeltetett alkalmazásokat egy internetről elérhető IP-címen. További információkért lásd: külső betekintő [létrehozása][MakeExternalASE].
- **ILB**-Bevezetés: a VNet-on belüli IP-címen teszi elérhetővé a Bea beszolgáltatott alkalmazásokat. A belső végpont egy belső terheléselosztó (ILB), ezért a rendszer ILB-központnak nevezi. További információkért lásd: [ILB-bekészítés létrehozása és használata][MakeILBASE].

Minden ASE, külső és ILB rendelkezik egy nyilvános virtuális IP-vel, amely a bejövő felügyeleti forgalomhoz és a feladó címéhez van használva, amikor hívásokat kezdeményez az internetre. Az internetre csatlakozó Beérkezők által kezdeményezett hívások elhagyják a VNet a központhoz rendelt VIP-en keresztül. Ennek a VIP-nek a nyilvános IP-címe az a forrás IP-cím, amely az interneten keresztül elérhető, a központból érkező összes hívást megadja. Ha a szolgáltató alkalmazásai a VNet vagy VPN-en keresztül kezdeményeznek erőforrásokat, a forrás IP-cím a szolgáltató által használt alhálózat egyik IP-címe. Mivel a kiegészítő szolgáltatás a VNet belül van, további konfiguráció nélkül is hozzáférhet a VNet belüli erőforrásokhoz. Ha a VNet csatlakoztatva van a helyszíni hálózathoz, akkor a központhoz tartozó alkalmazások további konfigurálás nélkül is hozzáférhetnek az erőforrásokhoz.

![Külső bemutató][1] 

Ha külső elősegítő csomaggal rendelkezik, a nyilvános virtuális IP-cím is az a végpont, amelyet a benyújtó alkalmazásai a következőhöz oldják meg:

* HTTP/S 
* FTP/S
* Webes telepítés
* Távoli hibakeresés

![ILB][2]

Ha rendelkezik ILB-bevezetéssel, akkor a ILB címe a HTTP/S, az FTP/S, a web Deployment és a távoli hibakeresés végpontja.

## <a name="ase-subnet-size"></a>Bekapcsolási alhálózat mérete ##

A bevezetéshez használt alhálózat mérete nem módosítható a bevezetési pont telepítése után.  A kiegészítő csomag az egyes infrastruktúra-szerepkörökhöz, valamint az egyes elkülönített App Service-példányokhoz tartozó címeket használ.  Emellett az Azure Networking minden létrehozott alhálózat esetében öt címet használ.  Egy App Service csomaggal nem rendelkező betekintő szolgáltató 12 címet fog használni az alkalmazások létrehozása előtt.  Ha ez egy ILB, akkor 13 címet fog használni, mielőtt létrehoz egy alkalmazást a kiegészítőben. A bevezetési folyamat felskálázása során az infrastruktúra-szerepköröket az App Service-csomag példányainak 15 és 20 többszöröse adja hozzá.

   > [!NOTE]
   > Semmi más nem lehet az alhálózatban, de a központilag. Ügyeljen arra, hogy olyan címtartományt válasszon, amely lehetővé teszi a jövőbeli növekedést. Ez a beállítás később nem módosítható. A `/24` 256-es címmel rendelkező méretet javasoljuk.

Vertikális fel-vagy leskálázáskor a rendszer hozzáadja a megfelelő méretű új szerepköröket, majd a számítási feladatokat áttelepíti a jelenlegi méretről a célként megadott méretre. Az eredeti virtuális gépek csak a munkaterhelések áttelepítését követően törlődtek. Ha 100 ASP-példánnyal rendelkező beléptetési ponttal rendelkezett, akkor a virtuális gépek számának megduplázására van szükség.  Ezért javasoljuk, hogy a "/24" használatát az esetlegesen szükséges módosítások elfogadásához használja.  

## <a name="ase-dependencies"></a>Bemutató függőségek ##

### <a name="ase-inbound-dependencies"></a>Beérkező bejövő függőségek ###

Ahhoz, hogy a bejelentési funkció működjön, a beadáshoz a következő portok megnyitása szükséges:

| Használat | Forrás | Művelet |
|-----|------|----|
| Kezelés | Felügyeleti címek App Service | Bekapcsolási alhálózat: 454, 455 |
|  Belső belső kommunikáció | Bekapcsolási alhálózat: minden port | Bekapcsolási alhálózat: minden port
|  Azure Load Balancer bejövő engedélyezése | Azure Load Balancer | Bemutató alhálózat: 16001

2 további port is megnyitható a portok vizsgálatához, a 7654-es és a 1221-es porton. Egy IP-címmel válaszolnak, és semmi más nem. Szükség esetén blokkolva lehetnek. 

A bejövő felügyeleti forgalom a rendszerfigyelés mellett a bevezetési szolgáltatás utasításait és vezérlését is biztosítja. A forgalomhoz tartozó forráscím a benyújtó [felügyeleti címek][ASEManagement] dokumentumában szerepel. A hálózati biztonsági konfigurációnak engedélyeznie kell a hozzáférést az 454-es és a 455-es porton lévő beadási felügyeleti címekről. Ha letiltja a hozzáférést ezektől a címektől, a bevezető nem Kifogástalan állapotba kerül, majd felfüggesztve lesz. A 454-es és a 455-es portokon található TCP-forgalomnak ugyanabból a virtuális IP-címről kell visszalépnie, vagy pedig aszimmetrikus útválasztási problémával kell rendelkeznie. 

A bejelentési alhálózaton belül számos port van használatban a belső összetevő-kommunikációhoz, és ezek megváltoztathatók. Ehhez a beadási alhálózat összes portjának elérhetőnek kell lennie a beadási alhálózatból. 

Az Azure Load Balancer és a bejelentési alhálózat közötti kommunikációhoz a minimálisan megnyitható portok a következők: 454, 455 és 16001. Az 16001-es portot használja a terheléselosztó és a kiegészítő szolgáltatás közötti életben lévő forgalom megőrzése érdekében. Ha ILB-t használ, akkor a forgalmat a 454, 455, 16001 portokra lebontva is zárolhatja.  Ha külső betekintő eszközt használ, akkor figyelembe kell vennie a normál alkalmazás-hozzáférési portot.  

Az alkalmazás portjai a következő további portokra vonatkoznak:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresése  |  4020, 4022, 4024 |
|  Web Deploy szolgáltatás | 8172 |

Ha letiltja az alkalmazás portjait, a bejelentési funkció továbbra is működőképes, de az alkalmazás esetleg nem.  Ha az alkalmazáshoz hozzárendelt IP-címeket külső beosztással használja, akkor engedélyeznie kell a forgalmat az alkalmazásaihoz rendelt IP-címekről a be> IP-címek lapon megjelenő portokon látható portokra.

### <a name="ase-outbound-dependencies"></a>Kifelé irányuló kimenő függőségek ###

A kimenő hozzáféréshez a kiszervezet több külső rendszertől függ. A rendszerfüggőségek nagy része DNS-nevekkel van definiálva, és nem képezhető le az IP-címek rögzített készletére. Ennek megfelelően a bejelentési alhálózatról kimenő hozzáférés szükséges a beadási alhálózat minden külső IP-címéhez, különböző portokon keresztül. 

A beadás a következő portokon keresztül kommunikál az internettel elérhető címekkel:

| Használat | Portok |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-frissítések, Linux-függőségek, Azure-szolgáltatások | 80/443 |
| Azure SQL | 1433 | 
| Figyelés | 12000 |

A kimenő függőségek az [app Service Environment kimenő forgalom zárolását](./firewall-integration.md)ismertető dokumentumban találhatók. Ha a beszállító nem fér hozzá a függőségeihez, a működése leáll. Ha ez elég hosszú, a beadás felfüggesztve. 

### <a name="customer-dns"></a>Ügyfél DNS-je ###

Ha a VNet ügyfél által definiált DNS-kiszolgálóval van konfigurálva, a bérlői munkaterhelések használják azt. A benyújtó Azure DNS felügyeleti célokra használja. Ha a VNet egy ügyfél által kiválasztott DNS-kiszolgálóval van konfigurálva, a DNS-kiszolgálónak elérhetőnek kell lennie a központot tartalmazó alhálózatból.

A webalkalmazás DNS-feloldásának teszteléséhez használhatja a konzol parancs *nameresolver*. Lépjen az SCM-webhely hibakeresés ablakára az alkalmazáshoz, vagy nyissa meg az alkalmazást a portálon, és válassza a konzol lehetőséget. A rendszerhéj-parancssorból kiválaszthatja a parancs *nameresolver* a megkeresni kívánt DNS-névvel együtt. A visszakapott eredmény ugyanaz, mint amit az alkalmazás a keresés során fog kapni. Ha az nslookupt használja, a rendszer a Azure DNS használja helyette.

Ha megváltoztatja a VNet DNS-beállítását, akkor újra kell indítania a szolgáltatást. A beadási szolgáltatás újraindításának elkerülése érdekében erősen ajánlott a DNS-beállítások konfigurálása a VNet, mielőtt létrehozza a kiegészítő szolgáltatást.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portálfüggőségek ##

A központilag működő működési függőségek mellett a portál felületének néhány további eleme is van. A Azure Portal egyes képességei az _SCM-helyhez_ való közvetlen hozzáféréstől függenek. Azure App Service minden alkalmazásához két URL van. Az első URL-cím az alkalmazás elérésére szolgál. A második URL-cím az SCM-hely elérésére szolgál, amely más néven a _kudu-konzol_. Az SCM-helyet használó szolgáltatások a következők:

-   WebJobs
-   Functions
-   Naplózási adatfolyam
-   Kudu
-   Bővítmények
-   Process Explorer
-   Console

Ha ILB-beadást használ, az SCM-hely nem érhető el a VNet kívülről. Bizonyos funkciók nem fognak működni az alkalmazás-portálon, mert hozzáférést igényelnek egy alkalmazás SCM-helyéhez. A portál használata helyett közvetlenül is csatlakozhat az SCM-webhelyhez. 

Ha a ILB a tartománynév *contoso.appserviceenvironment.net* , és az alkalmazás neve *testapp*, az alkalmazás a következő helyen érhető el: *testapp.contoso.appserviceenvironment.net*. A szolgáltatással együtt elérhető SCM-hely a következő címen érhető el: *testapp.SCM.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Bemutató IP-címek ##

A kiegészítő szolgáltatásnak van néhány IP-címe, amelyről tisztában kell lennie. Ezek a következők:

- **Nyilvános bejövő IP-cím**: az alkalmazás adatforgalmához használatos egy külső beadásban, a felügyeleti forgalom pedig egy külső bemenően és egy ILB-ben is.
- **Kimenő nyilvános IP-cím**: "from" IP-címként használatos kimenő kapcsolatok esetén a VNet, amely nem a VPN-kapcsolaton keresztül van átirányítva.
- **ILB IP-címe**: a ILB IP-címe csak a ILB-ben létezik.
- **Alkalmazáshoz rendelt IP-alapú SSL-címek**: csak külső beadással lehetséges, és ha az IP-alapú SSL konfigurálva van.

Ezek az IP-címek láthatók a Azure Portal a betekintő felhasználói felületen. Ha rendelkezik ILB-elősegítő lehetőséggel, megjelenik a ILB IP-címe.

   > [!NOTE]
   > Ezek az IP-címek addig nem változnak, amíg a bevezetés folyamatban van.  Ha a beküldés felfüggesztette és visszaállítja a szolgáltatót, a szolgáltató által használt címek megváltoznak. A bevezetők felfüggesztésének szokásos oka az, ha letiltja a bejövő felügyeleti hozzáférést, vagy letiltja a hozzáférését egy bevezető függőséghez. 

![IP-címek][3]

### <a name="app-assigned-ip-addresses"></a>Alkalmazáshoz rendelt IP-címek ###

A külső kiegészítő szolgáltatással IP-címeket rendelhet az egyes alkalmazásokhoz. Ezt nem teheti meg egy ILB-elősegítő lehetőséggel. Az alkalmazás saját IP-címmel történő konfigurálásával kapcsolatos további információkért lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](../configure-ssl-bindings.md).

Ha egy alkalmazás saját IP-alapú SSL-címmel rendelkezik, a beadási osztály két portot rendel az adott IP-címhez. Egy port a HTTP-forgalomhoz, a másik pedig a HTTPS. Ezek a portok az IP-címek szakaszban lévő betekintő felhasználói felületen vannak felsorolva. A forgalomnak képesnek kell lennie a portok elérésére a VIP-címről, vagy az alkalmazások nem érhetők el. Ezt a követelményt fontos megjegyezni a hálózati biztonsági csoportok (NSG) konfigurálásakor.

## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok) ##

A [hálózati biztonsági csoportok][NSGs] lehetővé teszik a hálózati hozzáférés vezérlését egy VNet belül. Ha a portált használja, a legalacsonyabb prioritású implicit megtagadási szabályt kell megtagadnia minden adat megtagadásához. A buildek az engedélyezési szabályok.

A beadási szolgáltatásban nincs hozzáférése a saját önkiszolgáló üzemeltetéséhez használt virtuális gépekhez. Egy Microsoft által felügyelt előfizetésben vannak. Ha szeretné korlátozni az alkalmazásokhoz való hozzáférést a NSG, állítsa be a beadási alhálózaton. Ennek során körültekintően kell figyelnie a központilag fennálló függőségeket. Ha letiltja a függőségeket, a bekapcsoló nem működik.

A NSG konfigurálható a Azure Portal vagy a PowerShell használatával. Az itt látható információk a Azure Portal mutatják be. A NSG a **hálózatban** legfelső szintű erőforrásként hozhatja létre és kezelheti a portálon.

A NSG szükséges, a beosztási funkcióhoz tartozó bejegyzések lehetővé teszik a forgalom használatát:

**Bejövő**
* TCP az IP-szolgáltatás címkéjének AppServiceManagement a 454 455-as porton
* TCP a terheléselosztó által a 16001-es porton
* a bekapcsolási alhálózatból az összes porton lévő bekapcsoló alhálózatba

**Kimenő**
* UDP – az 53-es port összes IP-címe
* UDP – az 123-es port összes IP-címe
* TCP a 80-es portok összes IP-címeire, 443
* TCP és az IP-szolgáltatás címkéje `Sql` a 1433-as porton
* TCP és az összes IP-cím a 12000-as porton
* az összes porton lévő bekapcsolási alhálózatra

Ezek a portok nem tartalmazzák azokat a portokat, amelyeket az alkalmazásai a sikeres használathoz igényelnek. Előfordulhat például, hogy az alkalmazásnak meg kell hívnia egy MySQL-kiszolgálót a 3306-es porton. A 123-es porton futó Network Time Protocol (NTP) az operációs rendszer által használt Time szinkronizációs protokoll. Az NTP-végpontok nem kifejezetten a App Servicesra vonatkoznak, az operációs rendszertől függően változhatnak, és nem szerepelnek jól meghatározott címek listáján. Az időszinkronizálási problémák megelőzése érdekében engedélyeznie kell az UDP-forgalmat az 123-es porton található összes címhez. Az 12000-es porthoz tartozó kimenő TCP a rendszer támogatásához és elemzéséhez szükséges. A végpontok dinamikusak, és nincsenek pontosan meghatározott címekben.

A normál alkalmazás-hozzáférési portok a következők:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresése  |  4020, 4022, 4024 |
|  Web Deploy szolgáltatás | 8172 |

A bejövő és kimenő követelmények figyelembe vételével a NSG az ebben a példában bemutatott NSG hasonlóan kell kinéznie. 

![Bejövő biztonsági szabály][4]

Az alapértelmezett szabályok lehetővé teszik, hogy a VNet lévő IP-címek a beadási alhálózattal beszéljenek. Egy másik alapértelmezett szabály lehetővé teszi, hogy a terheléselosztó, más néven nyilvános virtuális IP-cím kommunikáljon a közcélú hálózattal. Az alapértelmezett szabályok megtekintéséhez válassza a **Hozzáadás** ikon melletti **alapértelmezett szabályok** elemet. Ha az alapértelmezett szabályok előtt elutasítja az összes többi szabályt, meggátolja a virtuális IP-címek és a közszolgáltatások közötti forgalmat. A VNet belülről érkező forgalom elkerüléséhez adja hozzá a saját szabályt a bejövő adatok engedélyezéséhez. A AzureLoadBalancer egyenlő forrást kell használnia, amelynek a rendeltetése **a és a** portszáma **\*** . Mivel a NSG-szabály a beadási alhálózatra van alkalmazva, nem kell konkrétnak lennie a célhelyen.

Ha IP-címet rendelt hozzá az alkalmazáshoz, győződjön meg róla, hogy megnyitotta a portok megtartását. A portok megtekintéséhez válassza ki **app Service Environment**  >  **IP-címeket**.  

A következő kimenő szabályokban látható összes elemre az utolsó elem kivételével szükség van. Lehetővé teszik a jelen cikk korábbi részében említett, a kiszolgált kapcsolatokhoz való hálózati hozzáférést. Ha letiltja valamelyiket, a kiegészítő szolgáltatás leáll. A lista utolsó eleme lehetővé teszi, hogy a beadás a VNet más erőforrásaival kommunikáljon.

![Kimenő biztonsági szabályok][5]

A NSG meghatározása után rendelje hozzá azokat az alhálózathoz, amelyhez a bekapcsolt. Ha nem emlékszik a beadási VNet vagy az alhálózatra, a következőt tekintheti meg a központot ismertető portál oldalán. A NSG az alhálózathoz való hozzárendeléséhez nyissa meg az alhálózat felhasználói felületét, és válassza ki a NSG.

## <a name="routes"></a>Útvonalak ##

A kényszerített bújtatás akkor történik, amikor útvonalakat állít be a VNet, így a kimenő forgalom nem közvetlenül az internethez csatlakozik, de valahol máshol, például egy ExpressRoute-átjáróval vagy egy virtuális berendezéssel.  Ha a bevezetőt úgy kell konfigurálnia, hogy az [app Service Environment a kényszerített bújtatással][forcedtunnel]konfigurálja, olvassa el a dokumentumot.  Ebből a dokumentumból megtudhatja, hogy a ExpressRoute és a kényszerített bújtatással hogyan használhatók a rendelkezésre álló lehetőségek.

Amikor létrehoz egy bevezetőt a portálon, az útválasztási táblázatokat is létrehozjuk a központból létrehozott alhálózaton.  Ezek az útvonalak egyszerűen csak azt mondják, hogy közvetlenül az internetre küldi a kimenő forgalmat.  
Ha ugyanazt az útvonalat manuálisan szeretné létrehozni, kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Portalt. Válassza a **hálózati**  >  **útválasztási táblák** lehetőséget.

2. Hozzon létre egy új útválasztási táblázatot a VNet megegyező régióban.

3. Az útválasztási táblázat felhasználói felületén válassza az **útvonalak**  >  **Hozzáadás** lehetőséget.

4. Állítsa a **következő ugrás típusát** az **Internet** értékre, a **címnek** pedig **0.0.0.0/0** értékre. Kattintson a **Mentés** gombra.

    Ekkor a következőhöz hasonló jelenik meg:

    ![Funkcionális útvonalak][6]

5. Az új útválasztási táblázat létrehozása után nyissa meg a bevezetőt tartalmazó alhálózatot. Válassza ki az útválasztási táblázatot a portálon lévő listából. A módosítás mentése után látnia kell az alhálózattal megjegyzett NSG és útvonalakat.

    ![NSG és útvonalak][7]

## <a name="service-endpoints"></a>Szolgáltatásvégpontok ##

A szolgáltatásvégpontokkal Azure-beli virtuális hálózatok és alhálózatok készletére korlátozhatja a több-bérlős szolgáltatásokhoz való hozzáférést. A szolgáltatásvégpontokról bővebben a [virtuális hálózatok szolgáltatásvégpontjaival][serviceendpoints] kapcsolatos dokumentációban olvashat. 

Amikor engedélyezi a szolgáltatásvégpontokat egy erőforráson, az összes többi útvonalhoz képest nagyobb prioritású útvonalak jönnek létre. Ha szolgáltatási végpontokat használ bármely Azure-szolgáltatáshoz, a kényszerített bújtatási szolgáltatóval, a szolgáltatásokra irányuló forgalmat nem kényszeríti a rendszer. 

Amikor a szolgáltatásvégpontok engedélyezettek egy Azure SQL-példánnyal rendelkező alhálózaton, akkor az erről az alhálózatról elért összes Azure SQL-példányhoz engedélyezve kell lennie a szolgáltatásvégpontoknak. Ha több Azure SQL-példányt szeretne elérni ugyanarról az alhálózatról, nem engedélyezheti a szolgáltatásvégpontokat csak az egyik Azure SQL-példányon, egy másikon pedig nem. Nincs más Azure-szolgáltatás, mint az Azure SQL, a szolgáltatási végpontok tekintetében. Amikor az Azure Storage szolgáltatáshoz engedélyezi a szolgáltatásvégpontokat, azzal zárolja az erőforráshoz való hozzáférést az alhálózatról, de továbbra is elérhet más Azure Storage-fiókokat, még akkor is, ha azokon nincsenek engedélyezve a szolgáltatásvégpontok.  

![Szolgáltatásvégpontok][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

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
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md