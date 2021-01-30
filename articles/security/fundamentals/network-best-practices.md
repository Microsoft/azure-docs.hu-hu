---
title: Ajánlott eljárások a hálózati biztonsághoz – Microsoft Azure
description: Ez a cikk a hálózati biztonsággal kapcsolatos ajánlott eljárásokat ismerteti a beépített Azure-funkciók használatával.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 68f3b9f9cfa8ea74a8240fab6e47bf737788081f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094324"
---
# <a name="azure-best-practices-for-network-security"></a>Az Azure ajánlott hálózati biztonsági eljárásai
Ez a cikk az Azure ajánlott eljárásainak gyűjteményét ismerteti a hálózati biztonság növelése érdekében. Ezek az ajánlott eljárások az Azure hálózatkezelési tapasztalataiból és az ügyfelek, például saját tapasztalataiból származnak.

Ebben a cikkben minden ajánlott eljáráshoz a következőket ismertetjük:

* Az ajánlott eljárás
* Miért érdemes engedélyezni az ajánlott eljárásokat
* Mi lehet az eredmény, ha nem sikerül engedélyezni az ajánlott eljárást
* Az ajánlott eljárás lehetséges alternatívái
* Hogyan sajátíthatja el az ajánlott eljárásokat

Ezek az ajánlott eljárások konszenzusos véleményt, valamint az Azure platform képességeit és funkcióit ismertetik, mivel azok a cikk írásának időpontjában szerepelnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

## <a name="use-strong-network-controls"></a>Erős hálózati vezérlők használata
Az Azure Virtual [Machines (VM)](https://azure.microsoft.com/services/virtual-machines/) és a készülékek más hálózati eszközökhöz is csatlakoztathatók az [Azure Virtual Networks](../../virtual-network/index.yml)szolgáltatásba való helyezéssel. Ez azt is lehetővé teszi, hogy virtuális hálózati adaptereket csatlakoztasson egy virtuális hálózathoz, hogy engedélyezze a TCP/IP-alapú kommunikációt a hálózatra képes eszközök között. Az Azure-beli virtuális hálózathoz csatlakoztatott virtuális gépek csatlakozhatnak ugyanazon a virtuális hálózaton, különböző virtuális hálózatokon, az interneten vagy a saját helyszíni hálózatokban lévő eszközökhöz.

A hálózat és a hálózat biztonságának tervezése során javasoljuk, hogy központosítsa a következőket:

- Az alapvető hálózati funkciók, például a ExpressRoute, a virtuális hálózatok és az alhálózatok üzembe helyezése, valamint az IP-címek kezelése.
- A hálózati biztonsági elemek, például a hálózati virtuális berendezések, például a ExpressRoute, a virtuális hálózat és az alhálózat kiépítése, valamint az IP-címek irányítása.

Ha közös felügyeleti eszközöket használ a hálózat és a hálózat biztonságának figyelésére, akkor mindkettőben egyértelmű láthatóságot kap. Egy egyszerű, egységesített biztonsági stratégia csökkenti a hibákat, mert növeli az emberi ismereteket és az automatizálás megbízhatóságát.

## <a name="logically-segment-subnets"></a>Logikai szegmens alhálózatok
Az Azure-beli virtuális hálózatok hasonlók a helyi hálózaton lévő LAN-hálózatokhoz. Az Azure-beli virtuális hálózat mögötti elképzelés az, hogy egyetlen magánhálózati IP-címtartomány alapján hozzon létre egy hálózatot, amelyen elhelyezheti az összes Azure-beli virtuális gépet. Az elérhető magánhálózati IP-címtartomány az A osztály (10.0.0.0/8), B osztály (172.16.0.0/12) és C osztály (192.168.0.0/16) tartománya.

Az alhálózatok logikai szegmentálásának ajánlott eljárásai a következők:

**Ajánlott eljárás**: ne rendeljen széles tartományú engedélyezési szabályokat (például a 0.0.0.0 engedélyezése a 255.255.255.255-en keresztül).  
**Részletek**: az ilyen típusú szabályok beállításának megelőzése vagy tiltása a hibaelhárítási eljárásokban. Ezek az engedélyezési szabályok hamis biztonsági értelemben vezetnek, és a vörös csapatok gyakran találják meg és használják fel azokat.

**Ajánlott eljárás**: a nagyobb címtartomány felosztása alhálózatokra.   
**Részletek**: az alhálózatok létrehozásához használja a [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-alapú alhálózatok alapelveit.

**Ajánlott eljárás**: hálózati hozzáférés-vezérlés létrehozása az alhálózatok között. Az alhálózatok közötti útválasztás automatikusan történik, és nem kell manuálisan konfigurálnia az útválasztási táblákat. Alapértelmezés szerint az Azure-beli virtuális hálózatokon létrehozott alhálózatok között nincs hálózati hozzáférés-vezérlés.   
**Részletek**: [hálózati biztonsági csoport](../../virtual-network/virtual-network-vnet-plan-design-arm.md) használata az Azure-alhálózatokra irányuló kéretlen forgalom elleni védelemhez. A hálózati biztonsági csoportok egyszerű, állapot-nyilvántartó csomagok vizsgálati eszközei, amelyek az 5 rekordos megközelítést használják (forrás IP-cím, forrásport, cél IP-címe, célport és 4. réteg protokoll) a hálózati forgalom engedélyezési/megtagadási szabályainak létrehozásához. Engedélyezheti vagy megtagadhatja a forgalmat egyetlen IP-címről, több IP-címre, illetve a teljes alhálózatokról érkező és onnan érkező forgalomra.

Ha hálózati biztonsági csoportokat használ az alhálózatok közötti hálózati hozzáférés-vezérléshez, a saját alhálózatában lévő azonos biztonsági zónához vagy szerepkörhöz tartozó erőforrásokat is elhelyezheti.

**Ajánlott eljárás**: kerülje a kisméretű virtuális hálózatok és alhálózatok elkerülését az egyszerűség és a rugalmasság biztosítása érdekében.   
**Részletek**: a legtöbb szervezet több erőforrást ad hozzá, mint az eredetileg tervezett, és a címek ismételt kiosztása a munkaigényes. A kis alhálózatok használata korlátozott biztonsági értéket tesz elérhetővé, és egy hálózati biztonsági csoportot rendel hozzá az egyes alhálózatokhoz. Az alhálózatok széles körű meghatározása a növekedés rugalmasságának biztosításához.

**Ajánlott eljárás**: a hálózati biztonsági csoportokra vonatkozó szabályok kezelésének egyszerűsítése az [alkalmazás biztonsági csoportjainak](https://azure.microsoft.com/blog/applicationsecuritygroups/)definiálásával.  
**Részletek**: Definiáljon egy alkalmazás biztonsági csoportot azon IP-címek listájához, amelyeket úgy gondol, hogy a későbbiekben változhat, vagy számos hálózati biztonsági csoporton belül használható. Ügyeljen arra, hogy az alkalmazás biztonsági csoportjai egyértelműen megnevezzenek, hogy mások is megértsék a tartalmukat és célját.

## <a name="adopt-a-zero-trust-approach"></a>Nullára való megbízhatósági megközelítés elfogadása
A peremhálózati hálózatok abban a feltételezésben működnek, hogy a hálózaton belüli összes rendszer megbízható. A mai alkalmazottak azonban különböző eszközökön és alkalmazásokon keresztül férhetnek hozzá a szervezet erőforrásaihoz, így a peremhálózat biztonsági ellenőrzése nem releváns. A csak az erőforrásokhoz hozzáférő hozzáférés-vezérlési szabályzatok nem elegendőek. A biztonság és a termelékenység egyensúlyának elsajátításához a biztonsági rendszergazdáknak is meg kell ismerniük az erőforrások elérésének *módját* .

A hálózatoknak a hagyományos védelemből kell fejlődnek, mivel a hálózatok sebezhetőek lehetnek a behatolások miatt: a támadók a megbízható határon belül egyetlen végpontot veszélyeztethetik, majd gyorsan kiterjeszthetik a teljes hálózatban lévő lábát. A [megbízhatósági kapcsolatok nulla](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) a körzeten belüli hálózati hely alapján megszüntetik a megbízhatóság fogalmát. Ehelyett a bizalmi kapcsolatok architektúrái az eszköz-és felhasználói megbízhatósági jogcímeket használják a szervezeti információkhoz és erőforrásokhoz való hozzáféréshez. Az új kezdeményezésekhez olyan zéró megbízhatósági megközelítést kell alkalmazni, amely a hozzáférés időpontjában érvényesíti a megbízhatósági kapcsolatot.

Az ajánlott eljárások a következők:

**Ajánlott eljárás**: az erőforrásokhoz való feltételes hozzáférés biztosítása az eszközök, az identitás, a megbízhatóság, a hálózati hely és egyebek alapján.  
**Részletek**: az [Azure ad feltételes hozzáférése](../../active-directory/conditional-access/overview.md) lehetővé teszi a megfelelő hozzáférés-vezérlés alkalmazását a szükséges feltételek alapján automatizált hozzáférés-vezérlési döntések bevezetésével. További információ: az [Azure Management hozzáférésének kezelése feltételes hozzáféréssel](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Ajánlott eljárás**: a portok hozzáférésének engedélyezése csak a munkafolyamat jóváhagyása után.  
**Részletek**: az Azure-beli virtuális gépekhez való, igény szerinti [Azure Security Center](../../security-center/security-center-just-in-time.md) a bejövő forgalom zárolásának leállításához, valamint a támadásoknak való kitettség csökkentéséhez, valamint a virtuális gépekhez való csatlakozáshoz szükséges egyszerű hozzáférést biztosít.

**Ajánlott eljárás**: ideiglenes engedélyek biztosítása a Kiemelt feladatok végrehajtásához, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor adható meg, ha a felhasználóknak szüksége van rá.  
**Részletek**: igény szerinti hozzáférés használata Azure ad Privileged Identity Management vagy harmadik féltől származó megoldásban a Kiemelt feladatok végrehajtásához szükséges engedélyek megadásához.

A zéró megbízhatóság a hálózati biztonság következő továbbfejlesztése. A maihoz állapota miatt a szervezeteknek a "jogsértés feltételezése" gondolkodásmódot kell tenniük, de ez a megközelítés nem korlátozható. A megbízható hálózatok zéró megbízhatósággal védik a vállalati és az erőforrásokat, és biztosítják, hogy a szervezetek egy modern munkahelyet hozzanak létre olyan technológiák használatával, amelyek lehetővé teszik az alkalmazottak számára, hogy bármilyen módon, bárhol és bármikor termelékenyek

## <a name="control-routing-behavior"></a>Vezérlési útválasztási viselkedés
Ha egy Azure-beli virtuális hálózaton helyez üzembe egy virtuális gépet, a virtuális gép csatlakozhat bármely más virtuális géphez ugyanazon a virtuális hálózaton, még akkor is, ha a többi virtuális gép különböző alhálózatokon található. Ez azért lehetséges, mert az alapértelmezés szerint engedélyezett rendszerútvonalak gyűjteménye lehetővé teszi az ilyen típusú kommunikációt. Ezek az alapértelmezett útvonalak lehetővé teszik, hogy ugyanazon a virtuális hálózaton lévő virtuális gépek kapcsolatot kezdeményezzenek egymással és az internettel (csak az internetre irányuló kimenő kommunikáció esetén).

Bár az alapértelmezett rendszerútvonalak számos központi telepítési forgatókönyv esetén hasznosak, a központi telepítések útválasztási konfigurációjának testreszabására is szükség van. A következő ugrási címeket beállíthatja meghatározott célhelyek eléréséhez.

Javasoljuk, hogy konfigurálja a [felhasználó által megadott útvonalakat](../../virtual-network/virtual-networks-udr-overview.md) , amikor egy biztonsági berendezést telepít egy virtuális hálózathoz. Erről egy későbbi, a [kritikus Azure-szolgáltatási erőforrások védelme érdekében című szakaszban olvashat, amely csak a virtuális hálózatokra vonatkozik](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> A felhasználó által megadott útvonalak nem szükségesek, és az alapértelmezett rendszerútvonalak általában működnek.
>
>

## <a name="use-virtual-network-appliances"></a>Virtuális hálózati berendezések használata
A hálózati biztonsági csoportok és a felhasználó által definiált útválasztás bizonyos mértékű hálózati biztonságot biztosíthat az [OSI modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és szállítási rétegében. Bizonyos helyzetekben azonban a verem magas szintjein is engedélyezni kell a biztonságot. Ilyen helyzetekben ajánlott az Azure-partnerek által biztosított virtuális hálózati biztonsági berendezések üzembe helyezése.

Az Azure hálózati biztonsági berendezések jobb biztonságot biztosíthatnak, mint amit a hálózati szintű vezérlők biztosítanak. A virtuális hálózati biztonsági berendezések hálózati biztonsági képességei a következők:


* Tűzfalas
* Behatolás-észlelés/behatolás-megelőzés
* Biztonságirés-kezelés
* Alkalmazás-vezérlőelem
* Hálózat alapú anomáliák észlelése
* Webszűrés
* Vírusvédelem
* Botnet-védelem

Az elérhető Azure Virtual Network biztonsági berendezések kereséséhez nyissa meg az [Azure Marketplace](https://azure.microsoft.com/marketplace/) -t, és keressen rá a "biztonság" és a "hálózati biztonság" kifejezésre.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Peremhálózati hálózatok üzembe helyezése biztonsági zónákhoz
A [peremhálózat](/azure/architecture/vdc/networking-virtual-datacenter) (más néven DMZ) egy fizikai vagy logikai hálózati szegmens, amely további biztonsági réteget biztosít az eszközök és az internet között. A peremhálózat szélén lévő speciális hálózati hozzáférés-vezérlési eszközök csak a virtuális hálózatra irányuló kívánt forgalmat teszik lehetővé.

A peremhálózati hálózatok hasznosak, mert a hálózati hozzáférés-vezérlési felügyeletet, a figyelést, a naplózást és a jelentéskészítést az Azure-beli virtuális hálózat peremén lévő eszközökön helyezheti át. A peremhálózaton általában lehetővé teszi az elosztott szolgáltatásmegtagadási (DDoS) megelőzés, a behatolás-észlelés/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek), a tűzfalszabályok és a házirendek, a webes szűrés, a hálózati antimalware és egyéb szolgáltatások. A hálózati biztonsági eszközök az Internet és az Azure-beli virtuális hálózat között ülnek, és mindkét hálózaton rendelkeznek illesztőfelülettel.

Bár ez a peremhálózat alapszintű kialakítása, számos különböző kialakítás létezik, például a háttér-visszaállítás, a Tri-homed és a többhelyű.

A korábban említett zéró megbízhatósági koncepció alapján azt javasoljuk, hogy az Azure-erőforrások hálózati biztonságának és hozzáférés-vezérlésének szintjének növeléséhez használjon peremhálózati hálózatot az összes magas biztonságú központi telepítéshez. Az Azure-ban vagy egy harmadik féltől származó megoldással további biztonsági réteget biztosíthat az eszközök és az internet között:

- Azure natív vezérlők. [Azure Firewall](../../firewall/overview.md) és a [Application Gateway webalkalmazási tűzfala](../../application-gateway/features.md#web-application-firewall) alapszintű biztonságot kínál a teljes állapotú tűzfallal, a beépített magas rendelkezésre állással, a korlátlan felhő-méretezhetőséggel, a teljes tartománynév-SZŰRÉSsel, a OWASP-szabályok támogatásával, valamint az egyszerű beállítással és konfigurációval.
- Harmadik féltől származó ajánlatok. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en megkeresheti a következő generációs tűzfalat (NGFW) és más, az ismerős biztonsági eszközöket és a hálózati biztonság jelentős szintjét biztosító ajánlatokat. Előfordulhat, hogy a konfiguráció összetettebb, de egy harmadik féltől származó ajánlat lehetővé teszi a meglévő képességek és szakértelmével használatát.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>A dedikált WAN-kapcsolatokkal elkerülhető az internetre való kitettség
Számos szervezet választotta a hibrid IT-útvonalat. A hibrid informatikai szolgáltatásokban a vállalat adateszközeinek némelyike az Azure-ban található, mások pedig a helyszínen maradnak. Számos esetben a szolgáltatás egyes összetevői futnak az Azure-ban, míg más összetevők a helyszínen maradnak.

Egy hibrid IT-forgatókönyvben általában valamilyen típusú létesítmények közötti kapcsolat van. A létesítmények közötti kapcsolat lehetővé teszi a vállalat számára, hogy a helyszíni hálózatait Azure-beli virtuális hálózatokhoz kapcsolja. Két több telephelyre kiterjedő csatlakozási megoldás érhető el:

* [Helyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Ez egy megbízható, megbízható és kialakított technológia, de a kapcsolat az interneten keresztül zajlik. A sávszélességet a rendszer legfeljebb 1,25 GB/s-ra korlátozza. Bizonyos helyzetekben a helyek közötti VPN használata kívánatos.
* **Azure-ExpressRoute**. Javasoljuk, hogy a [ExpressRoute](../../expressroute/expressroute-introduction.md) használja a létesítmények közötti kapcsolathoz. Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. A ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft Cloud Services-szolgáltatásokkal, mint például az Azure, a Microsoft 365 és a Dynamics 365. A ExpressRoute egy dedikált WAN-kapcsolat a helyszíni hely vagy egy Microsoft Exchange-szolgáltató között. Mivel ez egy távközlési kapcsolat, az adatai nem jutnak el az interneten keresztül, így nem jelentenek az internetes kommunikáció lehetséges kockázatait.

A ExpressRoute-kapcsolatok helye hatással lehet a tűzfal kapacitására, a méretezhetőségre, a megbízhatóságra és a hálózati forgalom láthatóságára. Meg kell határoznia, hogy hol kell leállítani a ExpressRoute a meglévő (helyszíni) hálózatokban. A következőket teheti:

- Ha a tűzfalon kívülről (a peremhálózati paradigma) leáll, ha az adatközpontok elkülönítéséhez meglévő gyakorlatot kell folytatnia, vagy ha kizárólag extranetes erőforrásokat helyez üzembe az Azure-ban.
- Megszakítás a tűzfalon belül (a hálózati bővítmény paradigma). Ez az alapértelmezett javaslat. Minden más esetben azt javasoljuk, hogy az Azure-t n/s adatközpontként kezelje.

## <a name="optimize-uptime-and-performance"></a>Üzemidő és teljesítmény optimalizálása
Ha egy szolgáltatás nem érhető el, az adatok nem érhetők el. Ha a teljesítmény olyan gyenge, hogy az adat nem használható, akkor az adat nem érhető el. Biztonsági szempontból a lehető leghatékonyabban kell meggyőződnie arról, hogy a szolgáltatások optimális üzemidőt és teljesítményt biztosítanak.

A rendelkezésre állás és a teljesítmény növelésére szolgáló népszerű és hatékony módszer a terheléselosztás. A terheléselosztás olyan módszer, amellyel a hálózati forgalom a szolgáltatás részét képező kiszolgálók között terjeszthető. Ha például a szolgáltatás részeként rendelkezik előtér-webkiszolgálókkal, a terheléselosztás használatával terjesztheti a forgalmat a több előtér-webkiszolgálón keresztül.

A forgalom ezen eloszlása növeli a rendelkezésre állást, mivel ha az egyik webkiszolgáló elérhetetlenné válik, a terheléselosztó leállítja a forgalmat az adott kiszolgálóra, és átirányítja a még online kiszolgálókra. A terheléselosztás a teljesítmény növelését is lehetővé teszi, mivel a kérelmek kiszolgálására szolgáló processzor, hálózat és memória terhelése az összes terheléselosztási kiszolgáló között oszlik meg.

Ajánlott terheléselosztást alkalmazni a szolgáltatásaihoz, és szükség esetén a szolgáltatásokhoz is. A következő forgatókönyvek mind az Azure virtuális hálózat szintjén, mind a globális szinten, valamint a terheléselosztási lehetőségekkel együtt használhatók.

**Forgatókönyv**: van egy olyan alkalmazás, amely:

- Az azonos felhasználói vagy ügyfél-munkamenettől érkező kéréseket igényel ugyanazon háttérbeli virtuális gép eléréséhez. Ilyenek például a bevásárlókocsi-alkalmazások és a webmail-kiszolgálók.
- Csak biztonságos kapcsolatot fogad el, ezért a kiszolgálóhoz való titkosítatlan kommunikáció nem elfogadható megoldás.
- Több HTTP-kérést igényel ugyanarra a hosszan futó TCP-kapcsolatra, hogy a különböző háttér-kiszolgálókra irányítsák vagy terheléselosztást lehessen készíteni.

**Terheléselosztási lehetőség**: használja az [Azure Application Gateway](../../application-gateway/overview.md), egy http webes forgalom Load balancert. Application Gateway támogatja a végpontok közötti TLS-titkosítást és a [TLS-lezárást](../../application-gateway/overview.md) az átjárón. A webkiszolgálók ezt követően nem terhelik a titkosítást és a visszafejtési terhelést, és titkosítatlan forgalmat végeznek a háttér-kiszolgálókra.

**Forgatókönyv**: be kell töltenie az internetről érkező bejövő kapcsolatokat az Azure-beli virtuális hálózatban található kiszolgálók között. A forgatókönyvek a következők:

- Olyan állapot nélküli alkalmazások rendelkeznek, amelyek elfogadják az internetről érkező kéréseket.
- Nem szükséges a Sticky Sessions vagy a TLS kiszervezése. A Sticky-munkamenetek az alkalmazások terheléselosztásához használt metódusok, amelyek a kiszolgáló-affinitás elérésére szolgálnak.

**Terheléselosztási lehetőség**: a Azure Portal használatával [hozzon létre egy külső terheléselosztó](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , amely a bejövő kérelmeket több virtuális gép között osztja el, és magasabb szintű rendelkezésre állást biztosít.

**Forgatókönyv**: terheléselosztási kapcsolatokat kell betölteni az interneten kívüli virtuális gépekről. A legtöbb esetben az Azure-beli virtuális hálózaton lévő eszközök, például a SQL Server példányok vagy a belső webkiszolgálók kezdeményezik a terheléselosztáshoz elfogadott kapcsolatokat.   
**Terheléselosztási lehetőség**: a Azure Portal használatával [hozzon létre egy belső terheléselosztó](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , amely a bejövő kérelmeket több virtuális gép között osztja el, és magasabb szintű rendelkezésre állást biztosít.

**Forgatókönyv**: globális terheléselosztásra van szükség, mert:

- Olyan felhőalapú megoldással rendelkezik, amelyet széles körben terjesztenek több régióban, és a lehető legmagasabb szintű üzemidőt (rendelkezésre állást) igénylik.
- A lehető legmagasabb szintű üzemidőt kell biztosítani annak biztosításához, hogy a szolgáltatás elérhető legyen, még akkor is, ha egy teljes adatközpont elérhetetlenné válik.

**Terheléselosztási lehetőség**: az Azure Traffic Manager használata. Traffic Manager lehetővé teszi, hogy a felhasználó helye alapján terheléselosztást lehessen létesíteni a szolgáltatásaival.

Ha például a felhasználó egy kérést küld a szolgáltatásnak az EU-ból, akkor a rendszer egy EU-adatközpontban található szolgáltatásokhoz irányítja a kapcsolódást. Traffic Manager globális terheléselosztás ezen része segít a teljesítmény javításában, mivel a legközelebbi adatközponthoz való csatlakozás gyorsabb, mint a távol lévő adatközpontokhoz való csatlakozás.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>RDP/SSH-hozzáférés letiltása a virtuális gépekhez
Az Azure-beli virtuális gépeket [RDP protokoll](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) és a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) protokoll használatával lehet elérni. Ezek a protokollok lehetővé teszik a virtuális gépek távoli felügyeletét, és mindennaposak az adatközpontokban használt számítástechnikában.

Lehetséges biztonsági probléma a protokollok interneten keresztüli használatával, hogy a támadók a [találgatásos kényszerítési](https://en.wikipedia.org/wiki/Brute-force_attack) technikákat használják az Azure-beli virtuális gépek eléréséhez. Miután a támadó megszerezte a hozzáférést, a virtuális gépet kiindulópontként használva a virtuális hálózat más gépeit is feltörheti, és akár az Azure-on kívüli, hálózatra kötött eszközöket is megtámadhatja.

Javasoljuk, hogy tiltsa le az internetről az Azure-beli virtuális gépek közvetlen RDP-és SSH-hozzáférését. Ha az internetről történő közvetlen RDP-és SSH-hozzáférés le van tiltva, akkor más beállítások is elérhetők, amelyekkel a távoli felügyelethez hozzáférhet ezekhez a virtuális gépekhez.

**Forgatókönyv**: egyetlen felhasználó számára lehetővé teszi, hogy az interneten keresztül kapcsolódjon egy Azure-beli virtuális hálózathoz.   
**Lehetőség**: a [pont – hely VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) egy másik kifejezés a távelérési VPN-ügyfél/kiszolgáló kapcsolathoz. A pont – hely kapcsolat létrejötte után a felhasználó RDP vagy SSH használatával kapcsolódhat az Azure-beli virtuális hálózatban található, pont – hely típusú VPN-kapcsolaton keresztül csatlakozó virtuális gépekhez. Ez azt feltételezi, hogy a felhasználó jogosultsággal rendelkezik a virtuális gépek eléréséhez.

A pont – hely VPN biztonságosabb, mint a közvetlen RDP-vagy SSH-kapcsolat, mivel a felhasználónak kétszer kell hitelesítenie a virtuális géphez való csatlakozás előtt. Először a felhasználónak hitelesítenie kell magát (és engedélyezni kell) a pont – hely VPN-kapcsolat létrehozásához. Másodszor, a felhasználónak hitelesítenie kell magát (és engedélyezni kell) az RDP-vagy SSH-munkamenet létrehozásához.

**Forgatókönyv**: engedélyezze a helyszíni hálózaton lévő felhasználók számára az Azure Virtual Network-beli virtuális gépekhez való kapcsolódást.   
**Lehetőség**: a [helyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) egy teljes hálózatot csatlakoztat egy másik hálózathoz az interneten keresztül. A helyszíni hálózat egy Azure-beli virtuális hálózathoz való összekapcsolásához a helyek közötti VPN-t használhatja. A helyszíni hálózaton lévő felhasználók RDP vagy SSH protokoll használatával csatlakoznak a helyek közötti VPN-kapcsolathoz. Nem kell engedélyeznie az interneten keresztüli közvetlen RDP-vagy SSH-hozzáférést.

**Forgatókönyv**: dedikált WAN-kapcsolat használata a helyek közötti VPN-hez hasonló funkciók biztosításához.   
**Lehetőség**: a [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)használata. A pont-hely típusú VPN-hez hasonló funkciókat biztosít. A legfontosabb különbségek a következők:

- A dedikált WAN-kapcsolat nem haladja meg az internetet.
- A dedikált WAN-kapcsolatok általában stabilabbak, és jobb teljesítményt biztosítanak.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>A kritikus Azure-szolgáltatási erőforrások biztonságossá tétele csak a virtuális hálózatok számára
Az Azure Private link használatával elérheti az Azure Pásti-szolgáltatásokat (például az Azure Storage és a SQL Database) a virtuális hálózat privát végpontján keresztül. A privát végpontok lehetővé teszik a kritikus Azure-szolgáltatási erőforrások védelmét csak a virtuális hálózatok számára. A virtuális hálózatról az Azure-szolgáltatásra irányuló forgalom mindig a Microsoft Azure gerinc hálózatán marad. A virtuális hálózatnak a nyilvános internethez való kimutatása már nem szükséges az Azure Pásti-szolgáltatások használatához. 

Az Azure Private link a következő előnyöket biztosítja:
- Az Azure- **szolgáltatás erőforrásainak jobb biztonsága**: az Azure privát hivatkozásával az Azure-szolgáltatás erőforrásai a privát végpont használatával védhetők a virtuális hálózat számára. A szolgáltatás erőforrásainak a virtuális hálózatban található privát végponthoz való biztonságossá tétele nagyobb biztonságot nyújt, mivel teljes mértékben eltávolítja a nyilvános internet-hozzáférést az erőforrásokhoz, és csak a virtuális hálózat privát végpontján keresztül engedélyezi a forgalmat.
- **Az Azure-szolgáltatási erőforrások privát elérése az Azure platformon**: a virtuális hálózat összekapcsolása az Azure-beli szolgáltatásokkal privát végpontok használatával. Nincs szükség nyilvános IP-cím megadására. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton.
- **Hozzáférés helyszíni és egymással összekapcsolt hálózatokból**: az Azure-ban futó, a helyszíni és a VPN-alagutakat futtató, valamint a privát végpontokat használó ExpressRoute-alapú virtuális hálózatok eléréséhez. A szolgáltatás eléréséhez nincs szükség a ExpressRoute Microsoft-partnerek konfigurálására vagy az Internet bejárására. A privát hivatkozás biztonságos módszert biztosít a számítási feladatok Azure-ba történő átirányításához.
- **Védelem az adatszivárgás ellen**: a rendszer a teljes szolgáltatás helyett egy privát végpontot rendel egy Pásti erőforrás egy példányához. A felhasználók csak az adott erőforráshoz tudnak csatlakozni. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben.
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióban lehet, és a B régióban található szolgáltatásokhoz is csatlakozhat.
- **Egyszerűen beállítható és kezelhető**: a virtuális hálózatokban már nincs szüksége fenntartott, nyilvános IP-címekre az Azure-erőforrások IP-tűzfalon keresztüli biztonságossá tételéhez. A magánhálózati végpontok beállításához nem szükséges NAT-vagy átjáró-eszköz. A privát végpontok egyszerű munkafolyamaton keresztül konfigurálhatók. A szolgáltatás oldalán könnyedén kezelheti az Azure-szolgáltatási erőforráshoz tartozó kapcsolatkérelmeket is. Az Azure Private link olyan fogyasztóknak és szolgáltatásoknak is működik, amelyek a különböző Azure Active Directory bérlők számára is tartanak. 
    
Ha többet szeretne megtudni a privát végpontokról és azokról az Azure-szolgáltatásokról és-régiókról, amelyekhez privát végpontok érhetők el, tekintse meg az [Azure privát hivatkozását](https://docs.microsoft.com/azure/private-link/private-link-overview).


## <a name="next-steps"></a>Következő lépések
Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.
