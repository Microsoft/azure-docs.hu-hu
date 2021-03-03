---
title: 'Azure ExpressRoute: útválasztási követelmények'
description: Ez az oldal ExpressRoute-kapcsolatcsoportok útválasztási konfigurálásának és kezelésének részletes követelményeit ismerteti.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: 0dc2b48d02eb8a69afc947891c263ef1510257a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721837"
---
# <a name="expressroute-routing-requirements"></a>Az ExpressRoute útválasztási követelményei
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, be kell állítania és kezelnie kell az útválasztást. Egyes kapcsolatszolgáltatók az útválasztás beállítását és kezelését felügyelt szolgáltatásként kínálják. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínálja-e ezt a szolgáltatást. Ha nem, akkor meg kell felelnie az alábbi követelményeknek:

A kapcsolat elősegítésének érdekében beállítandó útválasztási munkamenetek leírását a [kapcsolatcsoportokat és útválasztási tartományokat](expressroute-circuit-peerings.md) ismertető cikkben találja.

> [!NOTE]
> A Microsoft nem támogat semmilyen útválasztó-redundancia protokollt (például HSRP, VRRP) a magas rendelkezésre állású konfigurációkhoz. A magas rendelkezésre állás érdekében csak a társviszonyonként meglévő BGP-munkamenetek redundáns párjára támaszkodunk.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Társviszony-létesítéshez használt IP-címek
Fenn kell tartania néhány IP-címblokkot az Ön hálózata és a Microsoft vállalati peremhálózati (MSEE) útválasztói közötti útválasztás konfigurálásához. Ez a szakasz a követelmények listáját tartalmazza, és ismerteti ezen IP-címek beszerzésére és használatára vonatkozó szabályokat.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Magánhálózati Azure-társviszony-létesítéshez használt IP-címek
A társviszony-létesítések konfigurálásához privát IP-címeket vagy nyilvános IP-címeket használhat. Az útvonalak konfigurálásához használt címtartományok nem lehetnek átfedésben a virtuális hálózatok az Azure-ban való létrehozásához használt címtartományokkal. 

* IPv4
    * Egy /29 vagy két /30 alhálózatot le kell foglalnia az útválasztási felületek számára.
    * Az útválasztáshoz használt alhálózatok privát IP-címeket vagy nyilvános IP-címeket használhatnak.
    * Az alhálózatok nem ütközhetnek az ügyfél által a Microsoft Cloud használatához lefoglalt tartománnyal.
    * Ha egy /29 alhálózatot használ, az két /30 alhálózatra van felosztva. 
      * Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz használja a rendszer.
      * Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft használja egy BGP-munkamenet beállításához.
      * Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.
* IPv6
    * Az útválasztási felületek esetében le kell foglalni a/125 alhálózatot vagy két/126 alhálózatot.
    * Az útválasztáshoz használt alhálózatok privát IP-címeket vagy nyilvános IP-címeket használhatnak.
    * Az alhálózatok nem ütközhetnek az ügyfél által a Microsoft Cloud használatához lefoglalt tartománnyal.
    * Ha egy /125 alhálózatot használ, az két /126 alhálózatra van felosztva. 
      * Az első/126 alhálózatot az elsődleges kapcsolathoz, a második/30 alhálózatot pedig a másodlagos kapcsolathoz használja a rendszer.
      * Mindkét /126 alhálózathoz a /126 alhálózat IP-címét kell használnia az útválasztón. A /126 alhálózat második IP-címét a Microsoft használja egy BGP-munkamenet beállításához.
      * Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.

#### <a name="example-for-private-peering"></a>Példa a privát társviszony-létesítésre
Ha az a.b.c.d/29 alhálózatot választja a társviszony-létesítés beállítására, az két /30 alhálózatra van felosztva. Az alábbi példában figyelje meg, hogyan használják az a. b. c. d/29 alhálózatot:

* Az a.b.c.d/29 alhálózat fel van osztva az a.b.c.d/30 és az a.b.c.d+4/30 alhálózatra, és továbbítva lesz a Microsoftnak a kiépítési API-kon keresztül.
  * Ön az a.b.c.d+1 címet használja az elsődleges PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+2 címet használja az elsődleges MSEE VRF IP-címeként.
  * Ön az a.b.c.d+5 címet használja a másodlagos PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+6 címet használja a másodlagos MSEE VRF IP-címeként.

Vegyük azt az esetet, amelyben a 192.168.100.128/29 címet választja a privát társviszony-létesítés beállításához. Az 192.168.100.128/29 a 192.168.100.128–192.168.100.135 tartományból tartalmaz címeket, amelyek közül:

* az 192.168.100.128/30 az 1. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.129 címet, a Microsoft pedig a 192.168.100.130 címet használja.
* az 192.168.100.132/30 a 2. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.133 címet, a Microsoft pedig a 192.168.100.134 címet használja.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Microsoftos társviszony-létesítéshez használt IP-címek
A BGP-munkamenetek beállításához az Ön birtokában lévő nyilvános IP-címeket kell használnia. A Microsoftnak képesnek kell lennie ellenőrizni az IP-címek tulajdonjogát egy regionális útválasztási internetes jegyzékben vagy egy internetes útválasztási jegyzékben.

* A portálon a Microsoft társviszony-létesítéshez meghirdetett nyilvános előtagok IP-címei az IP-címekről érkező bejövő adatforgalom engedélyezéséhez ACL-eket hoznak létre a Microsoft Core-útválasztókhoz. 
* ExpressRoute-kapcsolatcsoportonként minden társviszonyhoz egy egyedi /29 (IPv4) vagy /125 (IPv6) alhálózatot vagy két /30 (IPv4) vagy /126 (IPv6) alhálózatot kell használnia a BGP társviszony-létesítés beállításához (ha többel rendelkezik).
* Ha egy /29 alhálózatot használ, az két /30 alhálózatra van felosztva.
* Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz használja a rendszer.
* Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft használja egy BGP-munkamenet beállításához.
* Ha egy /125 alhálózatot használ, az két /126 alhálózatra van felosztva.
* Az első /126 alhálózatot az elsődleges kapcsolathoz, a második /126 alhálózatot pedig a másodlagos kapcsolathoz használja a rendszer.
* Mindkét /126 alhálózathoz a /126 alhálózat IP-címét kell használnia az útválasztón. A /126 alhálózat második IP-címét a Microsoft használja egy BGP-munkamenet beállításához.
* Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Nyilvános Azure-társviszony-létesítéshez használt IP-címek

> [!NOTE]
> Az Azure nyilvános társ-összevonása nem érhető el az új áramkörökhöz.
> 

A BGP-munkamenetek beállításához az Ön birtokában lévő nyilvános IP-címeket kell használnia. A Microsoftnak képesnek kell lennie ellenőrizni az IP-címek tulajdonjogát egy regionális útválasztási internetes jegyzékben vagy egy internetes útválasztási jegyzékben. 

* ExpressRoute-kapcsolatcsoportonként minden társviszonyhoz egy egyedi /29 alhálózatot vagy két /30 alhálózatot kell használnia a BGP társviszony-létesítés beállításához (ha többel rendelkezik). 
* Ha egy /29 alhálózatot használ, az két /30 alhálózatra van felosztva. 
  * Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz használja a rendszer.
  * Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft használja egy BGP-munkamenet beállításához.
  * Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.

## <a name="public-ip-address-requirement"></a>Nyilvános IP-cím-követelmények

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés
Eldöntheti, hogy nyilvános vagy magánhálózati IPv4-címeket szeretne-e használni a magánhálózati társviszony-létesítéshez. Mi biztosítjuk a forgalom végpontok közötti elkülönítését, így elkerülhető, hogy a címek átfedésben legyenek más ügyfelekkel magánhálózati társviszony-létesítés esetén. Ezek a címek nincsenek meghirdetve az interneten. 

### <a name="microsoft-peering"></a>Microsoftos társhálózat-létesítés
A Microsoft-partneri útvonal lehetővé teszi a Microsoft Cloud Serviceshez való kapcsolódást. A szolgáltatások listája olyan Microsoft 365 szolgáltatásokat tartalmaz, mint például az Exchange Online, a SharePoint Online, a Skype vállalati verzió és a Microsoft teams. A Microsoft támogatja a kétirányú kapcsolatokat a Microsoft-társviszony-létesítésen keresztül. A Microsoft-felhőszolgáltatások felé irányuló forgalomnak érvényes nyilvános IPv4-címeket kell használnia, mielőtt belép a Microsoft hálózatába.

Győződjön meg róla, hogy az IP-címek és AS-számok regisztrálva vannak az alábbi nyilvántartások egyikében:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Amennyiben a rendszer nem rendeli Önhöz az előtagokat és az AS-számot az előző beállításjegyzékekben, meg kell nyitnia egy támogatási esetet az előtagok és az ASN manuális érvényesítéséhez. Az ügyfélszolgálat olyan dokumentációt (például Engedélyezési nyilatkozatot) kér, amely igazolja, hogy jogosult az erőforrások használatára.

A saját AS-számok Microsoft társviszony-létesítés esetében engedélyezettek, de ebben az esetben is szükség van manuális érvényesítésre. Ezenkívül eltávolítjuk az AS PATH értékében lévő privát AS-számokat a fogadott előtagok esetében. Ennek eredményeképpen nem fűzhet privát AS-számokat az AS PATH értékéhez [a Microsoft társviszony-létesítés útválasztásának befolyásolásához](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Ne Hirdessen ugyanazt a nyilvános IP-útvonalat a nyilvános internetre és a ExpressRoute-ra. Az aszimmetrikus útválasztást okozó helytelen konfiguráció kockázatának csökkentése érdekében erősen ajánlott, hogy a Microsofton keresztül hirdetett [NAT IP-címek](expressroute-nat.md) olyan tartományból ExpressRoute legyenek, amely egyáltalán nem hirdeti meg az internetet. Ha ez nem érhető el, elengedhetetlen annak biztosítása, hogy a ExpressRoute több mint egy adott tartományt Hirdessen, mint az internetkapcsolaton. A NAT nyilvános útvonalán kívül a helyszíni hálózatban lévő kiszolgálók által használt nyilvános IP-címeket is meghirdetheti a Microsofton belüli Microsoft 365-végpontokkal kommunikáló ExpressRoute. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Nyilvános peering (elavult – új áramkörök esetén nem érhető el)
Az Azure nyilvános társviszony-létesítési útvonal használatával az Azure-ban üzemeltetett összes szolgáltatáshoz csatlakozhat a nyilvános IP-címeiken keresztül. Ezen szolgáltatások közé tartoznak az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) című dokumentumban felsorolt szolgáltatások, valamint az ISV-k által a Microsoft Azure-ban üzemeltetett szolgáltatások. A Microsoft Azure-szolgáltatásokhoz a nyilvános társviszony-létesítéssel létrehozott kapcsolatokat mindig az Ön hálózata kezdeményezi a Microsoft hálózata felé. A Microsoft hálózatához tervezett forgalomhoz nyilvános IP-címeket kell használnia.

> [!IMPORTANT]
> Az összes Azure-beli Pásti-szolgáltatás a Microsoft-partneri hozzáférésen keresztül érhető el.
>   

A nyilvános társítások használata engedélyezett.

## <a name="dynamic-route-exchange"></a>Dinamikus útvonalcsere
Az útválasztás cseréje az eBGP protokollon keresztül történik. Az EBGP-munkamenetek az MSEE-k és az Ön útválasztója között jönnek létre. A BGP-munkamenetek hitelesítése nem szükséges. Szükség esetén konfigurálható egy MD5-kivonat. A BGP-munkamenetek konfigurálásával kapcsolatban lásd az [útválasztás konfigurálását](how-to-routefilter-portal.md) és a [kapcsolatcsoport-kiépítési munkafolyamatokat és a kapcsolatcsoportok állapotait](expressroute-workflows.md) ismertető témaköröket.

## <a name="autonomous-system-numbers"></a>Autonóm rendszerek számai
A Microsoft az AS 12076 számot használja az Azure nyilvános, az Azure privát és a Microsoft társviszony-létesítéshez. Az 65515–65520 AS-számok belső használatra vannak fenntartva. A 16 és a 32 bites AS-számok is támogatottak.

Az adatátvitel szimmetriájára nem vonatkoznak követelmények. Az előre és visszafelé haladó útvonalak különböző útválasztópárokon haladhatnak keresztül. Az azonos útvonalakat az Ön tulajdonában lévő több áramköri pár oldaláról kell meghirdetni. Az útvonalmetrikáknak nem kell megegyezniük.

## <a name="route-aggregation-and-prefix-limits"></a>Útvonal-összevonások és előtagkorlátozások
Akár 4000 IPv4-előtagot és 100 IPv6-előtagokat támogatunk az Azure-beli privát kapcsolaton keresztül. Ez akár 10 000 IPv4-előtaggal is növelhető, ha az ExpressRoute Premium bővítmény engedélyezve van. BGP-munkamenetenként legfeljebb 200 előtagot fogadunk el az Azure nyilvános és a Microsoft társviszony-létesítés esetében. 

A rendszer eldobja a BGP-munkameneteket, ha az előtagok száma meghaladja a korlátot. Csak a privát társviszony-létesítési kapcsolaton fogadjuk el az alapértelmezett útvonalakat. A szolgáltatónak ki kell szűrnie az alapértelmezett útvonalakat és a privát IP-címeket (RFC 1918) az Azure nyilvános és a Microsoft társviszony-létesítési útvonalakból. 

## <a name="transit-routing-and-cross-region-routing"></a>Tranzit útválasztás és régiók közötti útválasztás
Az ExpressRoute nem konfigurálható tranzit útválasztóként. A tranzit útválasztási szolgáltatások tekintetében a kapcsolatszolgáltatójára kell támaszkodnia.

## <a name="advertising-default-routes"></a>Alapértelmezett útvonalak meghirdetése
Az alapértelmezett útvonalak használata csak az Azure privát társviszony-létesítési munkamenetek esetében engedélyezett. Ilyen esetben a társított virtuális hálózatból származó minden forgalmat az Ön hálózatára irányítunk át. Az alapértelmezett útvonalak privát társviszony-létesítésbe történő meghirdetése az Azure-ból származó internetes útvonalak blokkolását eredményezi. Az internetről származó és az arra irányuló forgalom átirányításához az Azure-ban üzemeltetett szolgáltatások esetén a vállalati peremhálózatra kell támaszkodnia. 

 A más Azure-szolgáltatások és infrastruktúra-szolgáltatások csatlakozásának engedélyezéséhez biztosítania kell az alábbi elemek egyikének meglétét.

* Az Azure nyilvános társításának engedélyezése lehetővé teszi a forgalom nyilvános végpontokra való irányítását.
* Felhasználó által meghatározott útválasztást használ az internetkapcsolatot igénylő összes alhálózat internetkapcsolatának engedélyezéséhez.

> [!NOTE]
> Az alapértelmezett útvonalak meghirdetése megszakítja a Windows- és az egyéb virtuálisgép-licencek aktiválását. Megkerülő megoldás alkalmazásához kövesse az [itt](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling) található utasításokat.
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>BGP-közösségek támogatása
Ez a szakasz áttekinti, hogyan használhatók a BGP-közösségek az ExpressRoute-tal. A Microsoft a megfelelő közösségértékekkel címkézett útvonalakkal hirdeti meg az útvonalakat a nyilvános és a Microsoft társviszony-létesítésekben. Ennek az eljárásnak az indoklását és a közösségértékek részleteit alább olvashatja. A Microsoft azonban nem fogadja el a Microsoft számára meghirdetett útvonalakhoz rendelt közösségértékeket.

Ha az ExpressRoute-on keresztül csatlakozik a Microsofthoz egy geopolitikai régión belül lévő bármelyik társviszony-létesítési helyszínen, az összes Microsoft-felhőszolgáltatáshoz hozzáférést kap a geopolitikai határon belüli összes régióban. 

Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, az Észak-Európában és Nyugat-Európában üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáféréssel fog rendelkezni. 

A geopolitikai régiók, a hozzájuk rendelt Azure-régiók és a megfelelő ExpressRoute társviszony-létesítési helyszínek részletes listáját az [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md) oldalon találja.

Geopolitikai régiónként több ExpressRoute-kapcsolatcsoportot is vásárolhat. Több kapcsolattal jelentős előnyöket szerezhet a magas rendelkezésre állás és a földrajzi alapú redundancia területén. Azokban az esetekben, ahol több ExpressRoute-áramkör található, a Microsofttól származó, a Microsoft és a nyilvános kapcsolatok elérési útjain meghirdetett előtagokat fogja kapni. Ez azt jelenti, hogy a hálózatából több útvonal fog irányulni a Microsoft felé. Emiatt előfordulhat, hogy a hálózaton belüli útvonalválasztási döntések nem lesznek optimálisak. Ez az optimálisnál rosszabb csatlakozási teljesítményt okozhat a különböző szolgáltatások esetében. A közösségértékek alapján megfelelő útválasztási döntéseket hozhat, amelyekkel [optimális útválasztást kínálhat a felhasználóknak](expressroute-optimize-routing.md).

| **Microsoft Azure-régió** | **Regionális BGP-Közösség** | **Storage BGP-Közösség** | **SQL BGP-Közösség** | **Cosmos DB BGP-Közösség** | **Backup BGP-Közösség** |
| --- | --- | --- | --- | --- | --- |
| **Észak-Amerika** | |
| USA keleti régiója | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| USA 2. keleti régiója | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| USA nyugati régiója | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| USA 2. nyugati régiója | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| USA nyugati középső régiója | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| USA északi középső régiója | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| USA déli középső régiója | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| Az USA középső régiója | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Közép-Kanada | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Kelet-Kanada | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **Dél-Amerika** | |
| Dél-Brazília | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Európa** | |
| Észak-Európa | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| Nyugat-Európa | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| Az Egyesült Királyság déli régiója | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| Az Egyesült Királyság nyugati régiója | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| Közép-Franciaország | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Dél-Franciaország | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Észak-Svájc | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Nyugat-Svájc | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Észak-Németország | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Középnyugat-Németország | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Kelet-Norvégia | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Norvégia nyugati régiója | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Ázsia és a Csendes-óceáni térség** | |
| Kelet-Ázsia | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Délkelet-Ázsia | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japán** | |
| Kelet-Japán | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Nyugat-Japán | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Ausztrália** | |
| Kelet-Ausztrália | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Délkelet-Ausztrália | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Ausztrália kormányzati** | |
| Ausztrália középső régiója | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Ausztrália 2. középső régiója | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **India** | |
| Dél-India | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Nyugat-India | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Közép-India | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Dél-Korea** | |
| Dél-Korea déli régiója | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Dél-Korea középső régiója | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **Dél-afrikai Köztársaság**| |
| Dél-Afrika északi régiója | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Dél-Afrika nyugati régiója | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **EAE**| |
| Észak-Egyesült Arab | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| UAE középső régiója | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


A Microsoft által hirdetett összes útvonal a megfelelő közösségértéket tartalmazó címkével lesz ellátva. 

> [!IMPORTANT]
> A globális előtagok egy megfelelő közösségértéket tartalmazó címkével rendelkeznek.
> 
> 

### <a name="service-to-bgp-community-value"></a>Szolgáltatás a BGP közösségi értékre
A fentiek mellett a Microsoft a kapcsolódó szolgáltatások alapján is címkével látja el az előtagokat. Ez csak a Microsoft társviszony-létesítésre vonatkozik. Az alábbi táblázat a szolgáltatások BGP-közösségértékre való leképezéseit tartalmazza. A "Get-AzBgpServiceCommunity" parancsmagot futtathatja a legfrissebb értékek teljes listájához.

| **Szolgáltatás** | **BGP-közösségérték** |
| --- | --- |
| Exchange Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype vállalati online verzió\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Azure globális szolgáltatások\* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Egyéb Office 365 online szolgáltatások * * | 12076:5100 |

\* Az Azure globális szolgáltatásai jelenleg csak az Azure-DevOps tartalmazzák.

\*\* Engedélyezés szükséges a Microsofttól, lásd [: útválasztási szűrők konfigurálása a Microsoft-partnerek számára](how-to-routefilter-portal.md)

\*\*\* Ez a Közösség a Microsoft Teams Services szükséges útvonalait is közzéteszi.

\*\*\*\* A CRM Online támogatja a Dynamics v 8.2-es vagy régebbi verzióit. A magasabb verziók esetében válassza ki a regionális Közösséget a Dynamics-környezetekben.

> [!NOTE]
> A Microsoft nem fogadja el a Microsoft számára meghirdetett útvonalakon beállított BGP-közösségértékeket.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>BGP-közösségek támogatása országos felhőkörnyezetekben

| **Országos felhőkörnyezetek – Azure-régió**| **BGP-közösségérték** |
| --- | --- |
| **USA kormánya** |  |
| USA-beli államigazgatás – Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| USA-beli államigazgatás – Virginia | 12076:51105 |
| USA-beli államigazgatás – Texas | 12076:51108 |
| US DoD – Középső régió | 12076:51209 |
| US DoD – Kelet | 12076:51205 |


| **Szolgáltatás országos felhőkörnyezetekben** | **BGP-közösségérték** |
| --- | --- |
| **USA kormánya** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype Vállalati online verzió |12076:5130 |
| Azure Active Directory |12076:5160 |
| Egyéb Office 365-szolgáltatások |12076:5200 |

## <a name="next-steps"></a>Következő lépések
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
  * [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
