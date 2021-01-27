---
title: A SAP HANA hálózati architektúrája az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Hálózati architektúra a SAP HANA üzembe helyezéséhez az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ed932488551918bb0bfeb7dc9ffcb2f59b6d152
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878901"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (nagyméretű példányok) hálózati architektúrája

Az Azure hálózati szolgáltatások architektúrája az SAP-alkalmazások a HANA nagyméretű példányon történő sikeres üzembe helyezésének egyik kulcsfontosságú összetevője. Az Azure-beli (nagyméretű példányok) üzembe helyezése SAP HANA általában nagyobb SAP-környezettel rendelkezik, és számos különböző SAP-megoldással rendelkezik, amelyek különböző méretű adatbázisokkal, CPU-erőforrás-használattal és memória-kihasználtsággal rendelkeznek. Valószínű, hogy az összes IT-rendszer nem az Azure-ban található. Az SAP-környezet gyakran hibrid, valamint az adatbázis-kezelői pont és az SAP-alkalmazás nézete a NetWeaver, valamint az S/4HANA és a SAP HANA és más adatbázis-kezelők kombinációjának használatával. Az Azure különböző szolgáltatásokat kínál, amelyek lehetővé teszik a különböző adatbázis-kezelői, NetWeaver-és S/4HANA rendszerek futtatását az Azure-ban. Az Azure olyan hálózati technológiát is kínál, amely az Azure-hoz hasonló virtuális adatközpontot biztosít a helyszíni szoftverek üzembe helyezéséhez

Kivéve, ha a teljes informatikai rendszerek az Azure-ban futnak. Az Azure hálózatkezelési funkciói a helyszíni világ Azure-eszközökkel való összekapcsolására szolgálnak, így az Azure a tiéd virtuális adatközponthoz hasonlít. Az Azure hálózati funkcionalitása a következőket használja: 

- Az Azure-beli virtuális hálózatok a helyszíni hálózati eszközökhöz csatlakozó [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -áramkörhöz csatlakoznak.
- A helyszíni és az Azure-hoz csatlakozó ExpressRoute-áramkörnek legalább [1 GB/s vagy annál nagyobb](https://azure.microsoft.com/pricing/details/expressroute/)sávszélességgel kell rendelkeznie. Ez a minimális sávszélesség lehetővé teszi a megfelelő sávszélességet az adatok átviteléhez a helyszíni rendszerek és a virtuális gépeken futó rendszerek között. Emellett lehetővé teszi a megfelelő sávszélességet az Azure-rendszerekhez való kapcsolódáshoz a helyszíni felhasználóktól.
- Az Azure-ban az összes SAP-rendszer úgy van beállítva, hogy a virtuális hálózatokban kommunikáljon egymással.
- A helyszíni Active Directory és a helyi DNS-t az Azure-ba ExpressRoute-en keresztül terjesztik ki a helyszínen, vagy az Azure-ban futnak.

A HANA nagyméretű példányainak az Azure adatközpont hálózati hálóba való integrálásának konkrét esetére az Azure ExpressRoute technológiát is használja.


> [!NOTE] 
> Egy adott Azure-régióban csak egyetlen bérlőhöz lehet hozzárendelni egy HANA nagyméretű példány bélyegzőjét. Ezzel szemben egy nagyméretű HANA-példány Stamp bérlője csak egy Azure-előfizetéshez kapcsolható. Ez a követelmény összhangban van az Azure-ban található többi számlázandó objektummal.

Ha SAP HANA az Azure-ban (nagyméretű példányok) több különböző Azure-régióban van üzembe helyezve, a HANA nagyméretű példány bélyegzője külön bérlőt helyez üzembe. Ugyanazon Azure-előfizetés alatt is futtatható, ha ezek a példányok ugyanahhoz az SAP-környezethez tartoznak. 

> [!IMPORTANT] 
> Az Azure-ban (nagyméretű példányok) SAP HANA csak a Azure Resource Manager telepítési módszert támogatja.

 

## <a name="additional-virtual-network-information"></a>További virtuális hálózati információk

Egy virtuális hálózat ExpressRoute való összekapcsolásához létre kell hozni egy Azure ExpressRoute-átjárót. További információ: [a Expressroute Expressroute-átjárók](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

Az Azure ExpressRoute-átjárót az Azure-on kívüli infrastruktúra vagy egy nagyméretű Azure-példány ExpressRoute használják. Az Azure ExpressRoute-átjáró legfeljebb négy különböző ExpressRoute-áramkörhöz csatlakoztatható, feltéve, hogy ezek a kapcsolatok különböző Microsoft Enterprise Edge-útválasztók származnak. További információ: [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure](hana-overview-infrastructure-connectivity.md)-ban. 

> [!NOTE] 
> A ExpressRoute-átjárók által elérhető maximális átviteli sebesség 10 GB/s egy ExpressRoute-kapcsolatok használatával. A fájlok másolása egy virtuális hálózaton található virtuális gép és egy helyszíni rendszer (egyetlen másolási adatfolyam) között nem éri el a különböző átjárók teljes átviteli sebességét. A ExpressRoute-átjáró teljes sávszélességének kihasználása érdekében több streamet használjon. Más fájlokat is át kell másolnia egy adott fájl párhuzamos streamben.


## <a name="networking-architecture-for-hana-large-instance"></a>A HANA nagyméretű példány hálózatkezelési architektúrája
A HANA nagyméretű példány hálózatkezelési architektúrája négy különböző részből választható:

- Helyszíni Hálózatkezelés és ExpressRoute-kapcsolat az Azure-hoz. Ez a rész az ügyfél tartománya, és csatlakozik az Azure-hoz az ExpressRoute-on keresztül. Ezt a Expressroute áramkört Ön fizeti ki ügyfeleinek. A sávszélességnek elég nagynak kell lennie ahhoz, hogy kezelni tudja a helyszíni eszközök és az Ön által összekapcsolt Azure-régió közötti hálózati forgalmat. Tekintse meg a jobb alsó részt az alábbi ábrán.
- Az Azure-beli hálózati szolgáltatások, ahogy azt korábban már említettük, a virtuális hálózatokkal, amelyekhez ismét szükség van a ExpressRoute átjárók hozzáadására. Ez a rész egy olyan terület, ahol meg kell találnia az alkalmazásra, a biztonságra és a megfelelőségre vonatkozó követelmények megfelelő kialakítását. Azt jelzi, hogy a HANA Large példány használata egy másik lehetőség-e a virtuális hálózatok és az Azure Gateway SKU-azonosítók számának a kiválasztásához. Az ábrán a jobb felső sarokban látható.
- A HANA nagyméretű példány kapcsolata a ExpressRoute technológián keresztül az Azure-ba. Ezt a részt a Microsoft telepíti és kezeli. Mindössze annyit kell tennie, hogy az eszközeinek a HANA nagyméretű példányon való üzembe helyezése után az ExpressRoute áramkört a virtuális hálózatokhoz köti. További információ: [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure](hana-overview-infrastructure-connectivity.md)-ban. Az Azure-adatközpont hálózati hálója és a HANA nagyméretű példány-egységek közötti kapcsolathoz nem számítunk fel további díjat.
- Hálózatkezelés a HANA nagyméretű példányának bélyegén belül, amely leginkább transzparens.

![Az Azure-ban (nagyméretű példányok) és a helyszínen SAP HANA csatlakoztatott virtuális hálózat](./media/hana-overview-architecture/image1-architecture.png)

Az, hogy a helyszíni eszközeinek a ExpressRoute-on keresztül kell csatlakozniuk az Azure-hoz, nem változik, mert a HANA nagyméretű példányát használja. Az a követelmény, hogy egy vagy több virtuális gépet futtató virtuális hálózattal rendelkezzen, amely a HANA nagyméretű példány-egységekben üzemeltetett HANA-példányokhoz kapcsolódó alkalmazási réteget üzemelteti, szintén nem változik. 

Az Azure-beli SAP-üzemelő példányok közötti különbségek a következők:

- Az ügyfél-bérlő HANA nagyméretű példány-egységei egy másik ExpressRoute-áramkörön keresztül kapcsolódnak a virtuális hálózatokhoz. A betöltési feltételek elválasztásához a helyszíni és az Azure-beli virtuális hálózati ExpressRoute áramkörök, valamint az Azure-beli virtuális hálózatok és a HANA nagyméretű példányok közötti áramkörek nem ugyanazt az útválasztót használják.
- Az SAP-alkalmazási réteg és a HANA nagyméretű példány közötti munkaterhelés-profil eltérő jellegű, sok kis kéréssel és adatforgalommal, például adatátvitelsel (eredményhalmaz) SAP HANA az alkalmazás rétegében.
- Az SAP-alkalmazás architektúrája nagyobb mértékben érzékeny a hálózati késésre, mint a szokásos forgatókönyvek, ahol az adatok cseréje a helyszíni és az Azure között történik.
- Az Azure ExpressRoute-átjáró legalább két ExpressRoute-kapcsolattal rendelkezik. Egy olyan áramkör, amely a helyszíni rendszerből csatlakozik, és egy, a HANA nagyméretű példányairól csatlakozik. Ez a lépés csak a különböző Msee két további, a ExpressRoute-átjáróhoz való kapcsolódáshoz szükséges helyet hagy. Ez a korlátozás független a ExpressRoute gyors elérési útjának használattól. Az összes csatlakoztatott áramkör megosztja a ExpressRoute-átjáró bejövő adatmennyiségének maximális sávszélességét.

A HANA nagyméretű példányok bélyegének 3. változatában a virtuális gépek és a HANA nagyméretű példány-egységek közötti hálózati késés nagyobb lehet, mint a szokásos virtuálisgép-hálózat típusú hálózati adatelérési késés. Az Azure-régiótól függ, hogy a mért értékek meghaladják az SAP-Megjegyzés átlaga alatt besorolt 0,7 – MS menetidő-késést [#1100926 – gyakori kérdések: hálózati teljesítmény](https://launchpad.support.sap.com/#/notes/1100926/E). Az Azure-régiótól és az eszköztől függ, hogy az Azure-beli virtuális gép és a HANA nagyméretű példányok egysége közötti hálózati kétirányú késések mérhetőek-e, a mért késés akár 2 ezredmásodperc is lehet. Az ügyfelek ugyanakkor SAP HANA nagyméretű példányon sikeresen telepítenek SAP HANA-alapú üzemi SAP-alkalmazásokat. Győződjön meg róla, hogy alaposan tesztelje üzleti folyamatait az Azure HANA nagyméretű példányán. Egy új, ExpressRoute gyors elérési úttal rendelkező funkció képes csökkenteni a nagy méretű HANA-példányok és az Azure-beli alkalmazás-rétegbeli virtuális gépek közötti hálózati késést (lásd alább). 

A HANA nagyméretű példányok bélyegének 4. változatában a HANA nagy példányszámú bélyegző közelében üzembe helyezett Azure-beli virtuális gépek közötti hálózati késés az [SAP-megjegyzés #1100926 – GYIK: hálózati teljesítmény](https://launchpad.support.sap.com/#/notes/1100926/E) , ha az Azure ExpressRoute gyors elérési útja konfigurálva van (lásd alább). Ahhoz, hogy az Azure-beli virtuális gépeket a 4. változatban található HANA nagyméretű példányokhoz közel lehessen helyezni, ki kell használni az [Azure Proximity-elhelyezési csoportokat](../../co-location.md). A közelségi elhelyezési csoportok segítségével megkeresheti az SAP-alkalmazás rétegét ugyanabban az Azure-adatközpontban, mint a 4. változatban üzemeltetett HANA nagyméretű példány-egységeket az [Azure Proximity-elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.

Ahhoz, hogy a determinisztikus hálózati késést biztosítson a virtuális gépek és a HANA nagyméretű példányai között, elengedhetetlen a ExpressRoute Gateway SKU választása. A helyszíni és a virtuális gépek közötti adatforgalomtól eltérően a virtuális gépek és a HANA nagyméretű példányok közötti forgalmi mintázat kisebb, de nagy mennyiségű kérést és adatmennyiséget képes kialakítani. Az ilyen adattörések kezeléséhez kifejezetten ajánlott az UltraPerformance Gateway SKU használata. A HANA Large instances SKU II Type osztálya esetében a UltraPerformance átjáró SKU-jának ExpressRoute-átjáróként való használatát kötelező megadni.

> [!IMPORTANT] 
> Az SAP-alkalmazás és az adatbázis-rétegek közötti teljes hálózati forgalom miatt csak a virtuális hálózatokhoz tartozó HighPerformance-vagy UltraPerformance-átjárók támogatottak az Azure-ban (nagyméretű példányok) lévő SAP HANAhoz való csatlakozáshoz. A HANA nagyméretű példány Type II SKU-hoz csak a UltraPerformance Gateway SKU támogatott ExpressRoute-átjáróként. Kivételek érvényesek a ExpressRoute gyors elérési útjának használatakor (lásd alább)

### <a name="expressroute-fast-path"></a>ExpressRoute gyors elérési útja
A késés csökkentése érdekében a ExpressRoute gyors elérési útja a 2019 májusában lett bevezetve, és az SAP Application VM-et futtató Azure-beli virtuális hálózatok számára a HANA Large-példányok adott kapcsolatához. Az eddigi megoldás fő eltérése az, hogy a virtuális gépek és a HANA nagyméretű példányai közötti adatforgalom többé nem a ExpressRoute-átjárón keresztül történik. Ehelyett az Azure-beli virtuális hálózat alhálózatában hozzárendelt virtuális gépek közvetlenül kommunikálnak a dedikált vállalati peremhálózati útválasztóval. 

> [!IMPORTANT] 
> A ExpressRoute gyors elérési útja funkció megköveteli, hogy az SAP-alkalmazás virtuális gépeit futtató alhálózatok ugyanabban az Azure-beli virtuális hálózatban legyenek, amely a HANA nagyméretű példányaihoz csatlakozik. Az Azure Virtual Networks szolgáltatásban található virtuális gépek, amelyek az Azure-beli virtuális hálózattal vannak összekapcsolva, közvetlenül a HANA nagyméretű példány-egységekhez kapcsolódnak, nem élvezik a ExpressRoute gyors elérési útját. Ennek eredményeképpen jellemző a hub és a küllős virtuális hálózati tervek, ahol a ExpressRoute áramkörök egy hub virtuális hálózattal és az SAP-alkalmazás rétegét (küllőit) tartalmazó virtuális hálózatokkal csatlakoznak, a ExpressRoute gyors elérési útjának optimalizálása nem fog működni. A kívül-ben a ExpressRoute gyors elérési útja jelenleg nem támogatja a felhasználó által megadott útválasztási szabályokat (UDR). További információ: [ExpressRoute Virtual Network Gateway and FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


A ExpressRoute gyors elérési útjának konfigurálásával kapcsolatos további információkért olvassa el a [virtuális hálózat összekapcsolása a HANA nagyméretű példányokkal](./hana-connect-vnet-express-route.md)című dokumentumot.    

> [!NOTE]
> UltraPerformance ExpressRoute-átjáró szükséges ahhoz, hogy a ExpressRoute gyors elérési útja működjön


## <a name="single-sap-system"></a>Egyetlen SAP-System

A korábban bemutatott helyszíni infrastruktúra a ExpressRoute-on keresztül csatlakozik az Azure-hoz. A ExpressRoute áramkör egy Microsoft Enterprise Edge-útválasztóhoz (MSEE) csatlakozik. További információ: a [ExpressRoute technikai áttekintése](../../../expressroute/expressroute-introduction.md). Az útvonal létrehozása után az az Azure-gerinchez csatlakozik.

> [!NOTE] 
> Az SAP-tájak Azure-ban való futtatásához kapcsolódjon az SAP-környezet Azure-régiójához legközelebb eső Enterprise Edge-útválasztóhoz. A HANA nagyméretű példányai a dedikált nagyvállalati peremhálózati útválasztó eszközökön keresztül kapcsolódnak az Azure IaaS-beli virtuális gépek és a HANA nagyméretű példányai közötti hálózati késések minimalizálásához.

Az SAP-ExpressRoute futtató virtuális gépekhez tartozó átjáró a helyi hálózathoz csatlakozó egyetlen ExpressRoute-áramkörhöz csatlakozik. Ugyanez a virtuális hálózat egy különálló vállalati peremhálózati útválasztóhoz van csatlakoztatva, amely a nagyméretű példányokhoz való csatlakozáshoz van hozzárendelve. A ExpressRoute gyors elérési útját használva a HANA nagyméretű példányairól az SAP Application Layer virtuális gépekre irányuló adatfolyamok nem irányíthatók át a ExpressRoute-átjárón keresztül, és ez csökkenti a hálózati kétirányú késést.

Ez a rendszer egyszerű példa egyetlen SAP-rendszerre. Az SAP-alkalmazás rétegét az Azure üzemelteti. A SAP HANA adatbázis az Azure-on SAP HANA fut (nagyméretű példányok). Feltételezhető, hogy a 2 GB/s vagy 10 GB/s sebességű ExpressRoute-átjáró sávszélessége nem jelent szűk keresztmetszetet.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP-rendszer vagy nagy SAP-rendszer

Ha több SAP-rendszer vagy nagy SAP-rendszer van telepítve az Azure-SAP HANA való kapcsolódáshoz (nagyméretű példányok), a ExpressRoute-átjáró átviteli sebessége szűk keresztmetszetet eredményezhet. Vagy a különböző Azure-beli virtuális hálózatokban lévő üzemi és nem üzemi rendszerek elkülönítését is el szeretné különíteni. Ilyen esetben az alkalmazás rétegeit több virtuális hálózatra kell bontani. Létrehozhat egy olyan speciális virtuális hálózatot is, amely a HANA Large-példányhoz csatlakozik, például a következő esetekben:

- A biztonsági mentések közvetlenül a HANA-példányokban lévő Hana-példányokból az NFS-megosztásokat üzemeltető Azure-beli virtuális gépre.
- Nagyméretű biztonsági másolatok vagy más fájlok másolása a HANA nagyméretű példányairól az Azure-ban kezelt lemezterületre.

Használjon külön virtuális hálózatot a nagyméretű HANA-példányok és az Azure-beli adatok tömeges átvitelére szolgáló tárolók üzemeltetésére. Ezzel a megoldással elkerülhető, hogy a nagyméretű fájlok vagy adatátvitel a HANA Large-példányból az Azure-ba kerüljön a ExpressRoute-átjárón, amely az SAP-alkalmazási réteget futtató virtuális gépeket szolgálja ki. 

Skálázható hálózati architektúra esetén:

- Több virtuális hálózat kihasználása egyetlen, nagyobb SAP-alkalmazási réteghez.
- Telepítsen egy külön virtuális hálózatot minden telepített SAP-rendszerhez, összehasonlítva ezeket az SAP-rendszereket különálló alhálózatokban, ugyanazon a virtuális hálózaton.

  Skálázható hálózatkezelési architektúra az Azure-SAP HANA (nagyméretű példányok):

![SAP-alkalmazás rétegének üzembe helyezése több virtuális hálózat között](./media/hana-overview-architecture/image4-networking-architecture.png)

A szabályoktól és a korlátozástól függően a különböző SAP-rendszerű virtuális gépeket üzemeltető különböző virtuális hálózatok között érdemes a virtuális hálózatokat egyenrangúként használni. További információ a virtuális hálózatok összevonásáról: [Virtual Network-peering](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Útválasztás az Azure-ban

Alapértelmezés szerint a központi telepítés során három hálózati útválasztási szempontot kell figyelembe venni SAP HANA az Azure-ban (nagyméretű példányok):

* A SAP HANA az Azure-ban (nagyméretű példányok) csak az Azure-beli virtuális gépeken és a dedikált ExpressRoute-kapcsolaton keresztül érhető el, nem közvetlenül a helyszínen. A helyszíni helyről a HANA nagyméretű példányokra való közvetlen hozzáférés, amelyet a Microsoft az Ön számára biztosít, nem lehet azonnal. A tranzitív útválasztási korlátozásokat a SAP HANA nagyméretű példányhoz használt aktuális Azure-hálózati architektúra okozza. Egyes felügyeleti ügyfelek és bármely olyan alkalmazás, amely közvetlen hozzáférést igényel, mint például a helyszínen futó SAP Solution Manager, nem tud csatlakozni a SAP HANA-adatbázishoz. A kivételeket a "közvetlen útválasztás a HANA Large instances szolgáltatáshoz" című szakaszban találja.

* Ha a két különböző Azure-régióban üzembe helyezett HANA nagyméretű példány-egység vész-helyreállítást eredményez, a múltban ugyanazok az átmeneti útválasztási korlátozások érvényesek. Ez azt jelenti, hogy az egyik régióban (például USA nyugati régiójában) lévő HANA nagyméretű példányok IP-címei nem lettek átirányítva egy másik régióban üzembe helyezett HANA nagyméretű példány-egységbe (például az USA keleti régiója). Ez a korlátozás független volt az Azure-hálózatok közötti, illetve a különböző régiók közötti, illetve a nagyméretű ExpressRoute-áramkörök virtuális hálózatokhoz csatlakoztatására szolgáló kapcsolatok összekapcsolásával. Grafikus ábrázolás esetén tekintse meg a "HANA nagyméretű példány-egységek használata több régióban" című szakaszt. Ez a korlátozás, amely az üzembe helyezett architektúrával jött létre, megtiltotta a HANA rendszerreplikáció azonnali használatát vész-helyreállítási funkcióként. A legutóbbi módosítások esetében tekintse meg a "HANA nagyméretű példány-egységek használata több régióban" című szakaszt. 

* SAP HANA az Azure-beli (nagyméretű példányok) egységekhez hozzárendelt IP-cím tartozik a kiszolgáló IP-címkészlet címtartományból, amelyet a HANA nagyméretű példány központi telepítésének kérésekor küldött. További információ: [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure](hana-overview-infrastructure-connectivity.md)-ban. Ez az IP-cím elérhető az Azure-előfizetések és-áramkör használatával, amely az Azure-beli virtuális hálózatokat a HANA nagyméretű példányaihoz köti össze. A kiszolgáló IP-címkészlet-címtartomány kiosztott IP-címe közvetlenül a hardver egységhez van rendelve. *Nem* a NAT-on keresztül van hozzárendelve, mint a megoldás első üzembe helyezése esetén. 

### <a name="direct-routing-to-hana-large-instances"></a>Közvetlen útválasztás a HANA nagyméretű példányaihoz

Alapértelmezés szerint a tranzitív útválasztás nem működik ezekben a forgatókönyvekben:

* A HANA nagyméretű példányai és a helyszíni üzemelő példányok között.

* Két különböző régióban üzembe helyezett HANA nagyméretű példány-útválasztás között.

A tranzitív útválasztás három módon engedélyezhető a következő helyzetekben:

- Fordított proxy az adatok továbbításához a és a között. Például az F5 BIG-IP, NGINX és Traffic Manager üzembe helyezése az Azure-beli virtuális hálózaton, amely a HANA nagyméretű példányokhoz és a helyszíni környezethez csatlakozik virtuális tűzfal/forgalom-útválasztási megoldásként.
- A Linux rendszerű virtuális gépeken az iptables szabályok használatával engedélyezhető a helyszíni és a HANA nagyméretű példány-egységek, illetve a különböző régiókban található HANA nagyméretű példányok közötti útválasztás. Az iptables rendszert futtató virtuális GÉPET az Azure virtuális hálózatban kell telepíteni, amely a HANA nagyméretű példányokhoz és a helyszíni környezethez csatlakozik. Ennek megfelelően méretezni kell a virtuális gépet, hogy a virtuális gép hálózati átviteli sebessége elegendő legyen a várt hálózati forgalomhoz. A virtuális gép hálózati sávszélességével kapcsolatos részletekért lásd az [Azure-beli Linux rendszerű virtuális gépek méreteit](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető cikket.
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) lenne egy másik megoldás, amely lehetővé teszi a közvetlen forgalmat a helyszíni és a HANA nagyméretű példányok között. 

A rendszer az összes forgalmat egy Azure-beli virtuális hálózaton keresztül irányítja át, így a forgalmat a használt Soft-készülékek vagy az Azure hálózati biztonsági csoportok is korlátozzák, így bizonyos IP-címek vagy IP-címtartományok blokkolása vagy explicit módon engedélyezett a HANA nagyméretű példányaihoz való hozzáféréshez. 

> [!NOTE]  
> Vegye figyelembe, hogy a harmadik féltől származó hálózati berendezéseket vagy iptables-ket érintő egyéni megoldások megvalósítását és támogatását nem a Microsoft biztosítja. A támogatást a használt összetevő gyártójának vagy az integrátornak kell megadnia. 

#### <a name="express-route-global-reach"></a>Expressz útvonal Global Reach
A Microsoft egy [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md)nevű új funkciót vezetett be. A Global Reach a HANA nagyméretű példányain két esetben használható:

- Közvetlen hozzáférés engedélyezése a helyszínről a különböző régiókban üzembe helyezett HANA nagyméretű példány-egységekre
- A különböző régiókban üzembe helyezett HANA nagyméretű példány-egységek közötti közvetlen kommunikáció engedélyezése


##### <a name="direct-access-from-on-premises"></a>Közvetlen hozzáférés a helyszíni környezetből
Azon Azure-régiókban, ahol a Global Reach elérhető, kérheti a ExpressRoute-áramkör Global Reach funkciójának engedélyezését, amely összekapcsolja a helyszíni hálózatot az Azure-beli virtuális hálózattal, amely a HANA nagyméretű példány-egységekhez is csatlakozik. A ExpressRoute-áramkör helyszíni oldalának van néhány hatása. Az árakért keresse [meg Global REACH-bővítmény](https://azure.microsoft.com/pricing/details/expressroute/)árát. A HANA nagyméretű példány-egységeket az Azure-hoz összekapcsoló áramkörhöz kapcsolódóan nem számítunk fel további költségeket. 

> [!IMPORTANT]  
> Ha Global Reacht használ a HANA nagyméretű példány-egységek és a helyszíni eszközök közvetlen elérésének engedélyezéséhez, a hálózati adatok és a vezérlési folyamat **nem az Azure Virtual Networks** szolgáltatáson keresztül történik, hanem közvetlenül a Microsoft vállalati Exchange-útválasztók között. Ennek eredményeképpen a NSG-vagy ASG-szabályok, illetve az Azure-beli virtuális hálózatokban üzembe helyezett bármilyen típusú tűzfal, NVA vagy proxy nem kerül megérintésre. **Ha a ExpressRoute Global Reach segítségével engedélyezi a helyszíni és a HANA-példányok nagyméretű példányaira vonatkozó korlátozásokat, és a HANA nagyméretű példányokhoz való hozzáféréshez szükséges engedélyeket meg kell határozni a helyszíni oldalon található tűzfalakon.** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>A HANA nagyméretű példányainak különböző Azure-régiókban való csatlakoztatása
Ugyanúgy, ahogy a ExpressRoute Global Reach a helyszíni és a HANA nagyméretű példányokhoz való csatlakozáshoz is használható, két különböző régióban üzembe helyezett HANA nagyméretű példány-bérlők összekapcsolására használható. Az elkülönítés az a ExpressRoute-áramkör, amelyet a HANA nagyméretű példányai bérlői használnak az Azure-hoz való kapcsolódásra mindkét régióban. Két különböző régióban üzembe helyezett HANA nagyméretű példány-bérlő csatlakoztatása nem jár további költségekkel. 

> [!IMPORTANT]  
> A különböző HANA nagyméretű példány-bérlők közötti hálózati forgalom adatáramlási és vezérlési folyamata nem lesz átirányítva az Azure Networks szolgáltatáson keresztül. Ennek eredményeképpen nem használhatja az Azure funkcionalitását vagy a NVA a két HANA nagyméretű példány bérlői közötti kommunikációs korlátozások betartatására. 

A ExpressRoute-Global Reach engedélyezésével kapcsolatos további információkért olvassa el a [virtuális hálózat összekapcsolása a HANA nagyméretű példányokkal](./hana-connect-vnet-express-route.md)című dokumentumot.


## <a name="internet-connectivity-of-hana-large-instance"></a>Nagyméretű HANA-példány internetkapcsolata
A HANA nagyméretű példány *nem* rendelkezik közvetlen internetkapcsolattal. Előfordulhat például, hogy ez a korlátozás korlátozza az operációs rendszer rendszerképének közvetlen regisztrálását az operációs rendszer gyártójával. Előfordulhat, hogy a helyi SUSE Linux Enterprise Server előfizetés-kezelési eszköz kiszolgálójának vagy Red Hat Enterprise Linux előfizetés-kezelőjének kell működnie.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Adattitkosítás a virtuális gépek és a HANA nagyméretű példányai között
A HANA nagyméretű példány és a virtuális gépek között továbbított adatforgalom nincs titkosítva. Azonban kizárólag a HANA adatbázis-kezelő és a JDBC/ODBC-alapú alkalmazások közötti adatcsere esetén engedélyezheti a forgalom titkosítását. További információkért tekintse meg az [SAP által használt dokumentációt](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>HANA nagyméretű példány-egységek használata több régióban

A vész-helyreállítási készlet feladatainak kihasználásához több Azure-régióban is meg kell adnia a nagy példányszámú példányok mennyiségét. Még az Azure [globális vnet-társítás] használatával is, a tranzitív útválasztás alapértelmezés szerint nem működik két különböző régióban lévő HANA nagyméretű példány-bérlők között. Global Reach azonban megnyitja a kommunikációs útvonalat a két különböző régióban kiépített HANA nagyméretű példány-egységek között. A ExpressRoute Global Reach ezen használati forgatókönyve a következőket teszi lehetővé:

 - HANA rendszerreplikáció további proxyk vagy tűzfalak nélkül
 - Biztonsági másolatok másolása két különböző régióban lévő HANA nagyméretű példány-egységek között a rendszermásolatok vagy a rendszerfrissítések elvégzéséhez


![Azure-beli nagyméretű példányokhoz csatlakozó virtuális hálózat különböző Azure-régiókban](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábrán látható, hogy a két régió különböző virtuális hálózatai két különböző ExpressRoute-áramkörhöz csatlakoznak, amelyek az Azure-ban (nagy példányok) az Azure-régiókban (a szürke vonalakon) való SAP HANA való csatlakozáshoz használatosak. Ennek a két kapcsolatnak az oka az, hogy mindkét oldalon a Msee kimaradása elleni védelem történik. A két Azure-régióban található két virtuális hálózat közötti kommunikációs folyamatot a két különböző régióban található két virtuális hálózat [globális](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) összevonásával kell kezelni (kék pontozott vonal). A vastag piros vonal írja le a ExpressRoute Global Reach kapcsolatot, amely lehetővé teszi, hogy a két különböző régióban lévő HANA nagyméretű példány egységei kommunikálhassanak egymással. 

> [!IMPORTANT] 
> Ha több ExpressRoute-áramkört használt, az elérési út és a helyi beállítások BGP-beállításait kell használni a forgalom megfelelő útválasztásának biztosításához.

**Következő lépések**
- [SAP HANA (nagyméretű példányok) tárolási architektúrájának](hana-storage-architecture.md) átirányítása