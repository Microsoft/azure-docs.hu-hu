---
title: Hálózati biztonság – Microsoft Azure ajánlott eljárásai
description: A cikk ismerteti az ajánlott eljárások hálózati biztonsági a beépített Azure-képességek egy készletét.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: f36658d57fb514ff279c39e9cb1644cf56341ec4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761830"
---
# <a name="azure-best-practices-for-network-security"></a>Hálózati biztonság Azure ajánlott eljárásai
Ez a cikk ismerteti a hálózati biztonságot Azure – ajánlott eljárások gyűjteménye. Ajánlott eljárások az Azure-hálózatok tapasztalatainkon származnak, és az ügyfelek a funkciókat, például saját magának.

Minden egyes ajánlott eljárás Ez a cikk ismerteti:

* Mi az az ajánlott eljárás szerint
* Miért ajánlott eljárás, hogy engedélyezni szeretné
* Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan tudhat meg az ajánlott eljárás engedélyezése

Ajánlott eljárások alapulnak a consensus véleményét, és az Azure platform képességeit és szolgáltatáskészleteket, ez a cikk írásának időpontjában léteznek. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

## <a name="use-strong-network-controls"></a>Erős hálózati vezérlők használata
A kapcsolódás [Azure-beli virtuális gépek (VM)](https://azure.microsoft.com/services/virtual-machines/) és más hálózati eszközök szerint áthelyezi őket-készülékek [Azure virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/). Virtuális hálózati adapterek kapcsolódhatnak, hogy engedélyezik a hálózati eszközök közötti TCP/IP-alapú kommunikációt egy virtuális hálózathoz. Azure-beli virtuális hálózathoz csatlakozó virtuális gépek eszközök ugyanazon a virtuális hálózaton, különböző virtuális hálózatokban, az interneten vagy a saját helyszíni hálózatokhoz csatlakozhat.

A hálózat és a hálózati biztonság tervezése, javasoljuk, hogy központosítása:

- Alapvető hálózati funkciók, mint például az ExpressRoute, virtuális hálózat és alhálózat kiépítési és IP-címkezelés kezelése.
- A hálózati biztonsági elemek, például a hálózati virtuális berendezés funkciók, mint például az ExpressRoute, virtuális hálózat és alhálózat kiépítési és IP-címkezelés irányítása.

Ha tulajdonságkészlettel rendelkezik felügyeleti eszközök használatával figyelheti a hálózat és a hálózati biztonság, tisztán kap a két. Egyszerű és egységes biztonsági stratégia csökkenti a hibák, mert növeli a emberi megértését és az automation megbízhatóságát.

## <a name="logically-segment-subnets"></a>Logikailag szegmens alhálózatok
Azure virtuális hálózatok hasonlítanak a helyi hálózatok a helyi hálózaton. A cél Azure-beli virtuális hálózathoz mögött, hálózatot, egy egyetlen privát IP-címteret, amelyen helyezze el az Azure-beli virtuális gépek alapján hoz létre. A magánhálózati IP-címterek érhető el az osztály A (10.0.0.0/8), B osztály (172.16.0.0/12), és C osztályú (192.168.0.0/16) tartományait.

Ajánlott eljárások az alhálózatok logikailag szegmentálja a következők:

**Ajánlott eljárás**: Ne rendeljen szabályok széles körű tartományok engedélyezése (például engedélyezési 0.0.0.0 255.255.255.255 keresztül).  
**Részletes**: Győződjön meg arról, a hibaelhárítási eljárásokkal tiltása, vagy ilyen típusú szabályok beállításának letiltása. Ezek a valóságnak biztonsági szabályok vezethet, és gyakran található és piros csapatok kiaknázni.

**Ajánlott eljárás**: A nagyobb címtérrel oszthatja alhálózatokra.   
**Részletes**: Használat [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-alapú alhálózatok alapelveket az alhálózatok létrehozására.

**Ajánlott eljárás**: Hozzon létre az alhálózatok közötti hálózati hozzáférés-vezérlést. Az alhálózatok közötti útválasztás automatikusan megtörténik, és nem kell manuálisan konfigurálnia a táblákat. Alapértelmezés szerint nincsenek nincs hálózati hozzáférés-vezérlés az Azure-beli virtuális hálózathoz létrehozott alhálózatok között.   
**Részletes**: Használja a [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) kéretlen forgalmat ellen védelmet biztosító Azure alhálózatra. Hálózati biztonsági csoportok olyan egyszerű, állapot-nyilvántartó csomag ellenőrző eszköz, amely az 5 rekordos módszer (forrás IP-címe, forrásport, cél IP-címe, célport és 4. rétegbeli protokoll) használatával hozzon létre hálózati forgalmi szabályokat engedélyezni vagy letiltani. Engedélyezi, vagy megtagadják a forgalmat, és a egy IP-címet, és több IP-címekről érkező vagy a és a teljes alhálózat.

Hálózati hozzáférés-vezérlés az alhálózatok közötti hálózati biztonsági csoportokat használ, amikor helyezheti az azonos biztonsági zónában vagy szerepkör külön alhálózatokon tartozó erőforrásokhoz.

**Ajánlott eljárás**: Kerülje a kis méretű virtuális hálózatok és alhálózatok az egyszerűség és rugalmasság biztosítása érdekében.   
**Részletes**: A legtöbb szervezet rendeljen több erőforrást, mint a kezdetben tervezett, és címek újbóli lefoglalása a munkaerő-igényes. Korlátozott biztonságot érték kisebb alhálózat használatával ad hozzá, és a hálózati biztonsági csoport hozzárendelése minden egyes alhálózat többletterheléssel. Definiálhat alhálózatokat széles körben, gondoskodjon arról, hogy van-e a növekedés rugalmasságot.

**Ajánlott eljárás**: Hálózati biztonsági csoport szabály kezelésének leegyszerűsítése definiálásával [az alkalmazásbiztonsági csoportok](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Részletes**: Adjon meg egy alkalmazás biztonsági csoportot, listák IP-címtartományokat, ha úgy gondolja, hogy a későbbiekben változhatnak, vagy használható számos hálózati biztonsági csoportok. Ügyeljen arra, hogy névre alkalmazásbiztonsági csoportok egyértelműen így mások képes megérteni azok tartalmát és a cél.

## <a name="adopt-a-zero-trust-approach"></a>Egy nulla megbízható megközelítés elfogadása
Szegélyhálózat-alapú hálózatok feltételezzük, hogy megbízható lehet-e a hálózaton belüli összes rendszer működik. A mai munkavállalók bárhonnan elérheti a szervezeti erőforrásokat, de a különböző eszközökhöz és alkalmazásokhoz, ami lehetővé teszi szegélyhálózat-alapú biztonsági vezérlő nem számít. Hozzáférés-vezérlési házirendeket, amelyek kizárólag a fókusz érheti el az erőforrás nem állnak elegendő. Biztonság és hatékonyság közötti egyensúly fő, biztonsági rendszergazdák is kell figyelembe vennie *hogyan* egy erőforrás elérésekor.

A hálózatot kell használnia a hagyományos védelem fejlődnek, mivel előfordulhat, hogy a hálózatokat a támadásokkal szemben sebezhető: egy támadó veszélyeztetheti a megbízható határán belül egyetlen végponton, és majd gyorsan bontsa ki a foothold az egész hálózatban. [Megbízhatósági nulla](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) hálózatok számára a megbízható hálózati helyen belül a szegélyhálózat-alapú fogalmát. Nulla megbízhatósági architektúrák használja inkább a felhasználói és megbízható jogcímeket kapu hozzáférés a vállalati adatokat és erőforrásokat. Új kezdeményezések fogadja el a nulla megbízhatósági megközelítést, amely bizalmi kapcsolat ellenőrzése a hozzáférés idején.

Ajánlott eljárások a következők:

**Ajánlott eljárás**: A feltételes hozzáférést az eszköz, identitás, frissítési garanciát biztosító, hálózati hely és egyéb erőforrásokhoz.  
**Részletes**: [Az Azure AD feltételes hozzáférés](../active-directory/conditional-access/overview.md) lehetővé teszi, hogy alkalmazza a megfelelő hozzáférés-vezérlés implementálásával automatikus döntést hozhasson a hozzáférésről a szükséges feltételek alapján. További információkért lásd: [Azure felügyeleti való hozzáférés kezelése feltételes hozzáféréssel](../role-based-access-control/conditional-access-azure-management.md).

**Ajánlott eljárás**: Port hozzáférés engedélyezése csak a munkafolyamat-jóváhagyás után.  
**Részletes**: Használhat [just-in-time VM access az Azure Security Centerben](../security-center/security-center-just-in-time.md) zárolása az Azure virtuális gépekre, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalom le.

**Ajánlott eljárás**: Engedélyek ideiglenes emelt szintű feladatok végrehajtásához, amely megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók férjenek hozzá, miután lejárt az engedélyeket. Hozzáférés csak akkor, ha a felhasználók szüksége van rá.  
**Részletes**: Segítségével igény szerinti elérése az Azure AD Privileged Identity Management vagy a külső megoldás engedélyeket emelt szintű feladatok végrehajtásához.

Nulla megbízhatósági nem a következő hálózati biztonsági alakulását. Az állam érő meghajtók szervezetek számára, hogy a "sérülések feltételezésének" így igénybe, de nem korlátozza az ezzel a módszerrel. Nulla megbízható hálózatok biztosítva, hogy szervezetek, amelyek a munkavégzéshez bármikor, bárhonnan, bármilyen módon segítse munkatársait technológiák használatával hozhat létre egy modern munkahely vállalati adatok és erőforrások védelmét.

## <a name="control-routing-behavior"></a>Útválasztási viselkedés vezérlése
Ha egy virtuális gép elhelyezése Azure-beli virtuális hálózathoz, a virtuális gép csatlakozhat bármely más virtuális Gépet az azonos virtuális hálózaton, akkor is, ha a virtuális gépek különböző alhálózatokon. Ez akkor lehetséges, mert gyűjteménye a rendszerútvonalak alapértelmezés szerint engedélyezve van, lehetővé teszi, hogy az ilyen típusú kommunikációhoz. Ezek az alapértelmezett útvonalak lehetővé teszik a virtuális gépek egymással, és az internettel (a kimenő kommunikáció csak az internethez) kapcsolatok kezdeményezésének azonos virtuális hálózaton.

Bár az alapértelmezett rendszerútvonalakat számos központi telepítési forgatókönyvek esetén hasznosak lehetnek, vannak azt szeretné, szabhatja testre az üzemelő példányok útválasztási konfigurációja. Beállíthatja, hogy a következő ugrási cím meghatározott célok eléréséhez.

Azt javasoljuk, hogy konfigurálja [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md) telepítésekor meg kell adnia egy virtuális hálózat biztonsági berendezés. Erről egy későbbi szakaszban olvashat nevű döntésről [biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózataihoz](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Felhasználó által megadott útvonalak nem szükségesek, és az alapértelmezett rendszerútvonalakat általában működik.
>
>

## <a name="use-virtual-network-appliances"></a>Virtuális hálózati berendezések használata
Hálózati biztonsági csoportok és a felhasználó által meghatározott útválasztás megadhat egy bizonyos szintű hálózati biztonság: a hálózati és átviteli rétegeket, a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model). De bizonyos helyzetekben, azt szeretné, vagy a verem magas szintű biztonsági engedélyeznie kell. Ilyen esetekben javasoljuk, hogy a virtuális hálózati biztonsági berendezéseket az Azure-partnerek által biztosított üzembe.

Azure-beli hálózati biztonsági berendezéseket hálózati szintű vezérlőket biztosít nagyobb biztonságot biztosíthat. Hálózati biztonsági funkcióinak virtuális hálózati biztonsági berendezéseket a következők:


* Optimalizálóként működik
* Észlelési/behatolás-megelőzési behatolás
* Biztonsági rések kezelése
* Alkalmazás-vezérlő
* Hálózati alapú anomáliadetektálás
* Webes szűrési
* A víruskereső
* Botnet védelme

Elérhető az Azure virtual network security berendezések megkereséséhez nyissa meg a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/) , és keressen a "security" és "hálózati biztonság."

## <a name="deploy-perimeter-networks-for-security-zones"></a>Központi telepítése a szegélyhálózatokon biztonsági zónák
A [szegélyhálózaton](https://docs.microsoft.com/azure/best-practices-network-security) (más néven DMZ) egy fizikai vagy logikai hálózati szegmens, amely az eszközök és az internet közötti biztonsági réteget biztosít. Speciális hálózati hozzáférést vezérlő eszközök szegélyhálózaton szélén engedélyezése csak a kívánt forgalmat a virtuális hálózatban.

Szegélyhálózat hasznosak, mert a hálózati hozzáférés-vezérlés kezelését, figyelés, naplózás és jelentéskészítés az eszközökön az Azure virtuális hálózat peremén összpontosíthasson. Szegélyhálózaton, ahol általában engedélyezi az elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás megelőzése, behatolás észlelési/behatolás megelőzési rendszerek (IDS/IPS), tűzfalszabályokat és házirendek, webszűrést, hálózati kártevőirtó és további. A hálózati biztonsági eszközök az interneten és az Azure virtuális hálózat között található, és mindkét hálózat kezelőfelületet.

Bár ez az alapvető tervezési szempontjait a szegélyhálózaton, nincsenek számos különböző formában használhatók, például a végpontok közötti, tri többcímes és Többhelyű.

A korábban említett nulla megbízható fogalmat alapján, javasoljuk, hogy, fontolja meg az összes biztonságú környezetek szegélyhálózaton hálózati biztonság és hozzáférés-vezérlés az Azure-erőforrások növelése. Azure-ban vagy egy külső megoldás segítségével egy további biztonsági réteget nyújt az eszközök és az internet között:

- Az Azure natív szabályozza. [Azure-tűzfalon](../firewall/overview.md) és a [az Application Gateway webalkalmazási tűzfal](../application-gateway/overview.md#web-application-firewall) kínálnak szolgáltatás, beépített magas rendelkezésre állás, korlátlan felhőalapú méretezhetőségi, egy teljes körűen állapot-nyilvántartó tűzfal alapvető biztonsági FQDN szűrése , alapvető OWASP-szabálykészletek, és egyszerű beállítás és konfiguráció támogatása.
- Külső ajánlatokat. Keresés a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) új generációs tűzfal (NGFW) és egyéb külső megoldások, amelyek jól ismert biztonsági eszközökkel és jelentős mértékben továbbfejlesztett hálózati biztonsági szintet nyújtanak. Előfordulhat, hogy a konfigurációs összetettebb, de a külső ajánlat előfordulhat, hogy lehetővé teszik, hogy meglévő képességeinek és ismereteket.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Dedikált WAN-kapcsolatok az interneten keresztül a fenyegetéseknek elkerülése
Számos szervezet választotta, a hibrid informatikai útvonalat. A hibrid informatikai a vállalat információs eszközeit vannak az Azure-ban, és mások a helyszínen marad. Sok esetben néhány összetevőt, a szolgáltatás Azure-ban fut más összetevők továbbra is a helyszíni.

A hibrid informatikai forgatókönyv van általában valamilyen típusú létesítmények közötti kapcsolatok. Létesítmények közötti kapcsolat lehetővé teszi, hogy a vállalat a helyszíni hálózatokhoz csatlakozni az Azure virtuális hálózatok. Létesítmények közötti kapcsolat két megoldások érhetők el:

* [Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Egy megbízható, megbízható és meglévő technológiai, de a kapcsolat az interneten keresztül történik. Legfeljebb körülbelül 1,25 GB/s sávszélesség van korlátozott. Site-to-site VPN kívánatos lehetőség csak bizonyos esetekben.
* **Azure ExpressRoute**. Javasoljuk, hogy használjon [ExpressRoute](../expressroute/expressroute-introduction.md) a létesítmények közötti kapcsolathoz. Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az expressroute-tal és a Microsoft felhőszolgáltatásai, például Azure, Office 365 és Dynamics 365 kapcsolatokat létesíthet. Az ExpressRoute dedikált WAN a helyszíni helyre vagy az üzemeltetési szolgáltatók a Microsoft Exchange közötti kapcsolat. Mivel ez egy telekommunikációs kapcsolatot, az adatok nem az utazási az interneten keresztül, így a ki nem téve az internetes kommunikáció jelentette lehetséges kockázatokról.

Az ExpressRoute-kapcsolat helyét hatással lehet a tűzfal kapacitás, a méretezhetőség, a megbízhatóság és a hálózati forgalom látható-e. Határozza meg, hol ExpressRoute leállítja a meglévő (helyszíni) hálózatok kell. A következőket teheti:

- Megszűnik a tűzfalon (a szegélyhálózat-alapú hálózati paradigmát) kívül, ha szüksége van a betekintést a forgalomra, ha egy meglévő gyakorlat adatközpontok elkülönítése a továbbiakban azt kell használnia, vagy ha az Azure-ban kívánja menteni az extranetes erőforrásokhoz kizárólag.
- Állítsa le a tűzfalon (a hálózati bővítmény paradigmát) belül. Ez az ajánlott alapértelmezett érték. Minden más esetben javasoljuk, hogy Azure kezelésére, az n-edik adatközpont.

## <a name="optimize-uptime-and-performance"></a>Rendelkezésre állását és a teljesítmény optimalizálása
Ha egy szolgáltatás nem működik, információt nem lehet hozzáférni. Ha a teljesítmény gyenge, hogy az adatok nem használható, érdemes lehet az adatok nem lesznek elérhetők. Biztonsági szempontból a kell tennie, bármi is győződjön meg róla, hogy a szolgáltatások optimális rendelkezésre állását és teljesítményét.

Népszerű és hatékony módszert a rendelkezésre állás és teljesítmény fokozása terheléselosztási. Terheléselosztás a szolgáltatás részét képező kiszolgálók közötti hálózati forgalom elosztása módszer. Például ha a szolgáltatás részeként előtér-webkiszolgálók, használhatja terheléselosztást a több előtér-webkiszolgáló között oszthatja el a forgalmat.

A forgalom elosztását növeli a rendelkezésre állási, mivel a webkiszolgálók egyike elérhetetlenné válik, ha a terheléselosztó nem irányít több forgalmat az adott kiszolgálóval, és átirányítja a kiszolgálók, amelyek továbbra is működik. Terheléselosztás lehetővé teszi a teljesítmény, mivel minden elosztott terhelésű kiszolgáló között oszlanak meg a processzor, memória és hálózati terhelést kérelmek számára.

Azt javasoljuk, hogy felhasználások terheléselosztás amikor csak lehet, és a szolgáltatások megfelelő. Az alábbiakban forgatókönyveket is az az Azure virtuális hálózat szintjén, és a globális szinten, valamint az egyes terheléselosztási lehetőségek.

**A forgatókönyv**: Egy alkalmazást, amely:

- Az azonos ügyfél-vagy felhasználói munkamenet kéréseket ugyanaz a háttérbeli virtuális gép eléréséhez szükséges. Erre vonatkozó példákat vannak vásárlás bevásárlókocsi-alkalmazások és a webes levelezőkiszolgálók.
- Csak egy biztonságos kapcsolatot fogad el, így a kiszolgáló felé irányuló titkosítatlan kommunikáció nem elfogadható megoldás.
- Szükséges, az azonos hosszú ideig futó TCP-kapcsolat átirányíthatók vagy betölteni a HTTP-kéréseit különböző háttérkiszolgálókra terheléselosztását.

**Terheléselosztási beállítás**: Használat [Azure Application Gateway](../application-gateway/application-gateway-introduction.md), egy HTTP webes forgalom load balancert. Az Application Gateway támogatja a teljes körű SSL-titkosítást és [SSL-lezárást](../application-gateway/application-gateway-introduction.md) az átjáróra. Webkiszolgálók is majd megszabaduljanak a titkosítási és visszafejtési üzemeltetési költségektől és a háttér-kiszolgálókon nem titkosított adatforgalom.

**A forgatókönyv**: Be kell tölteni az egyenleg a bejövő kapcsolatok az Azure virtuális hálózatban található kiszolgálók között az internetről. Forgatókönyvek, amikor Ön:

- Állapot nélküli alkalmazások, amelyek fogadják az internetről érkező bejövő kéréseket kell.
- Nincs szükség a fix kiszolgálású munkameneteket vagy SSL-alapú kiszervezés. Fix kiszolgálású munkameneteket kell Application Load Balancing, a kiszolgáló-affinitási eléréséhez használt módszer.

**Terheléselosztási beállítás**: Az Azure Portallal való [hozzon létre egy külső terheléselosztó](../load-balancer/quickstart-create-basic-load-balancer-portal.md) , amely a bejövő kérések osztja el a magasabb szintű rendelkezésre állást biztosít több virtuális gép között.

**A forgatókönyv**: Be kell tölteni az egyenleg kapcsolatok virtuális gépekről, amelyek nem az interneten. A legtöbb esetben egy Azure virtuális hálózaton, például az SQL Server-példányokat, vagy belső webkiszolgálók eszközök által kezdeményezett terheléselosztásra fogad kapcsolatokat.   
**Terheléselosztási beállítás**: Az Azure Portallal való [hozható létre belső terheléselosztó](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) , amely a bejövő kérések osztja el a magasabb szintű rendelkezésre állást biztosít több virtuális gép között.

**A forgatókönyv**: Kell globális terheléselosztás mivel meg:

- Rendelkezik egy felhőalapú megoldás, amely széles körben legyen elosztva több régióban és a legmagasabb szintű üzemidő (elérhető) lehetőség van szükség.
- Győződjön meg arról, hogy a szolgáltatás elérhető akkor is, ha a teljes adatközpont elérhetetlenné válik lehetővé kell a legmagasabb szintű rendelkezésre állását.

**Terheléselosztási beállítás**: Az Azure Traffic Manager használja. A TRAFFIC Manager lehetővé teszi a szolgáltatásokhoz, a felhasználó helye alapján egyenleg kapcsolatok betöltése.

Például ha a felhasználó kérést küld a szolgáltatás az Európai Unióból, a kapcsolat van irányítva az EU-adatközpontban található szolgáltatások. Ez a rész a Traffic Manager globális betölteni a terheléselosztási segítséget nyújt a teljesítmény javítása, mert a legközelebbi adatközponthoz csatlakozó gyorsabban, adatközpontok, amelyek messze csatlakozik.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>A virtuális gépekhez RDP/SSH-hozzáférés letiltása
Lehet elérni az Azure virtual machines használatával [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) és a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) protokollt. Ezeket a protokollokat engedélyezése a virtuális gépek távoli helyeken lévő felügyeleti és a datacenter számítástechnikai standard.

A potenciális biztonsági probléma merült fel ezeket a protokollokat használ az interneten keresztül, hogy a támadók arra használhatják [találgatásos](https://en.wikipedia.org/wiki/Brute-force_attack) eljárások Azure-beli virtuális gépek eléréséhez. A támadók hozzáférést szereznek, miután kiindulási pontot használni a virtuális Géphez a virtuális hálózat gépeivel csökkenése, vagy még a hálózati eszközök, Azure-on kívülről támadásokkal szemben.

Azt javasoljuk, hogy tiltsa le a közvetlen RDP és SSH-hozzáférés az Azure-beli virtuális gépek, az internetről. Az internetről érkező RDP és SSH közvetlen hozzáférés le van tiltva, után más lehetőségek eléréséhez ezek a virtuális gépek távoli felügyeletére használható.

**A forgatókönyv**: Az interneten keresztül csatlakozni az Azure virtuális hálózat egyetlen felhasználó engedélyezése.   
**A beállítás**: [Pont – hely VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) távelérési VPN-ügyfél-kiszolgáló kapcsolatot egy másik kifejezés. A pont – hely kapcsolat létrejötte után a felhasználó használhatja az RDP vagy SSH bármely, az Azure virtuális hálózat pont – hely VPN-en keresztül csatlakozó felhasználó található virtuális gépek csatlakozni. A parancs feltételezi, hogy a felhasználó jogosult virtuális gépek el azokat.

Pont – hely VPN nem biztonságosabb, mint a közvetlen RDP vagy SSH-kapcsolatok, mert a felhasználó rendelkezik a virtuális Géphez való csatlakozás előtt kétszer hitelesíteni. Első lépésként a felhasználónak kell hitelesíteni (és engedélyezhető) a pont – hely VPN-kapcsolat létrehozásához. A második, a felhasználónak kell hitelesíteni (és engedélyezhető) a RDP vagy SSH-munkamenet létrehozásához.

**A forgatókönyv**: Engedélyezze a felhasználók a helyszíni hálózat az Azure virtuális hálózat a Csatlakozás virtuális gépekhez.   
**A beállítás**: A [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) teljes hálózathoz csatlakozik egy másik hálózattal az interneten keresztül. A site-to-site VPN használatával a helyszíni hálózat csatlakoztatása az Azure-beli virtuális hálózathoz. Az a helyszíni hálózaton a helyek közötti VPN-kapcsolaton keresztül az RDP vagy SSH protokoll használatával kapcsolódnak. Nem kell, hogy a közvetlen RDP vagy SSH-hozzáférését az interneten keresztül.

**A forgatókönyv**: Egy dedikált WAN-kapcsolat használatával a site-to-site VPN-hez hasonló funkciókat biztosít.   
**A beállítás**: Használat [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). A site-to-site VPN-hez hasonló funkciókat biztosít. A legfontosabb különbségek a következők:

- A dedikált WAN-kapcsolat nem haladnak át az interneten.
- Dedikált WAN-kapcsolatok általában több stabilak, és jobb teljesítményt.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatokhoz
Virtuális hálózati Szolgáltatásvégpontok használatával kiterjesztheti a virtuális hálózat magáncímterét és az identitás a virtuális hálózat az Azure-szolgáltatásokra egy közvetlen kapcsolaton keresztül. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. Az Azure-szolgáltatás a virtuális hálózatból érkező forgalom mindig a Microsoft Azure gerinchálózatán marad.

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásai számára**: A szolgáltatásvégpontokkal az Azure-szolgáltatások erőforrásai leköthetőek a virtuális hálózathoz. Virtual Network szolgáltatás-erőforrások biztosítása biztosít továbbfejlesztett biztonsági teljes mértékben eltávolítja a nyilvános internetkapcsolaton keresztüli hozzáférés az erőforrásokhoz, és lehetővé teszi a csak a virtuális hálózatból érkező forgalmat.
- **Optimális útválasztás az Azure szolgáltatás forgalmát a virtuális hálózatról**: A virtuális hálózat összes olyan esetleges útvonalat, amely az internetforgalmat a helyszíni és/vagy a virtuális készülékek nevén a kényszerített bújtatást végez, az Azure-szolgáltatások forgalmára, mint az internetforgalmat a azonos útvonalon is kényszeríthető. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára.

  Végpontok szolgáltatás forgalmát közvetlenül a virtuális hálózatról a szolgáltatás mindig az Azure gerinchálózatán igénybe vehet. Az Azure gerinchálózatán tartja az adatforgalmat lehetővé teszi, hogy továbbra is naplózhatja és a virtuális hálózatok kimenő internetforgalmát figyelt számítógépekhez, kényszerített bújtatást végez, anélkül, hogy befolyásolná a szolgáltatás forgalmára. Tudjon meg többet [felhasználó által definiált útvonalak és a kényszerített bújtatás](../virtual-network/virtual-networks-udr-overview.md).

- **Egyszerű beállítás kevesebb felügyeleti igénnyel**: Lefoglalt, nyilvános IP-címek már nincs szüksége a virtuális hálózatok biztonságossá tétele Azure-erőforrások egy IP-tűzfalon keresztül. A szolgáltatásvégpontok beállításához nincs szükség NAT- és útválasztó eszközökre. A szolgáltatásvégpontok egy egyszerű kattintással konfigurálhatóak az alhálózatokon. Nincs szükség további erőforrásokra a végpontok kezelése.

A Szolgáltatásvégpontok és az Azure-szolgáltatások és a Szolgáltatásvégpontok elérhetők régió kapcsolatos további információkért lásd: [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>További lépések
Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.
