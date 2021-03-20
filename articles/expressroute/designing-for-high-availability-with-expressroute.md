---
title: 'Azure ExpressRoute: magas rendelkezésre állás tervezése'
description: Ez az oldal az Azure ExpressRoute használata során magas rendelkezésre álláshoz nyújt építészeti javaslatokat.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 3602c3944e8731263fbb55f024c276783950329f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202361"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Magas rendelkezésre állás tervezése a ExpressRoute

A ExpressRoute magas rendelkezésre állású, hogy a szolgáltatói szintű magánhálózati kapcsolatot biztosítson a Microsoft erőforrásaihoz. Más szóval, a Microsoft hálózaton belüli ExpressRoute útvonalon nem létezik egyetlen meghibásodási pont. A rendelkezésre állás maximalizálása érdekében a ExpressRoute-áramkör ügyfelének és szolgáltatói szegmensének is magas rendelkezésre állást kell megtervezni. Ebben a cikkben először nézzük meg a hálózati architektúrával kapcsolatos szempontokat a robusztus hálózati kapcsolatok ExpressRoute használatával történő kiépítéséhez, majd nézzük meg a finomhangolási funkciókat, amelyek segítenek a ExpressRoute-áramkör magas rendelkezésre állásának javításában.

>[!NOTE]
>Az ebben a cikkben ismertetett fogalmak akkor is érvényesek, ha egy ExpressRoute-áramkör virtuális WAN vagy azon kívül jön létre.
>

## <a name="architecture-considerations"></a>Architektúrával kapcsolatos megfontolások

Az alábbi ábra azt szemlélteti, hogyan lehet csatlakozni az ExpressRoute áramkör használatával a ExpressRoute-áramkör rendelkezésre állásának maximalizálása érdekében.

 [![1]][1]

A magas rendelkezésre állás érdekében elengedhetetlen a ExpressRoute áramkör redundanciának fenntartása a teljes körű hálózatban. Más szóval meg kell őriznie a redundanciát a helyszíni hálózaton belül, és ne veszélyeztesse a redundanciát a szolgáltatói hálózaton belül. A redundancia fenntartása minimálisan azt jelenti, hogy elkerülhetők a hálózati hibák egyetlen pontja. A hálózati eszközök redundáns tápellátása és hűtése tovább növeli a magas rendelkezésre állást.

### <a name="first-mile-physical-layer-design-considerations"></a>Az első mérföldes fizikai réteg tervezési szempontjai

 Ha leállítja egy ExpressRoute-áramkör elsődleges és másodlagos kapcsolatát ugyanazon az ügyfél-telephelyi berendezésen (CPE), akkor a helyszíni hálózaton belüli magas rendelkezésre állást is veszélyeztetné. Továbbá, ha az elsődleges és a másodlagos kapcsolatokat is egy CPE-porton keresztül konfigurálja (akár a két kapcsolat leállításával, akár a partneri hálózaton belüli két kapcsolat összevonásával), akkor arra kényszeríti a partnert, hogy a hálózati szegmensen is magas rendelkezésre állást veszélyeztesse. Ezt a kompromisszumot az alábbi ábra mutatja be.

[![2]][2]

Ha azonban egy ExpressRoute-áramkör elsődleges és másodlagos kapcsolatait a különböző földrajzi helyszíneken állítja le, akkor a kapcsolat hálózati teljesítménye is veszélyeztetheti. Ha a forgalom aktív terheléssel van elfoglalva az elsődleges és a különböző földrajzi helyeken leállított másodlagos kapcsolatok között, akkor a két útvonal közötti hálózati késés lehetséges jelentős eltérése az optimálisnál rosszabb hálózati teljesítményt eredményez. 

A Geo-redundáns kialakítással kapcsolatos megfontolásokért lásd: a vész- [helyreállítás tervezése a ExpressRoute][DR]-mel.

### <a name="active-active-connections"></a>Aktív-aktív kapcsolatok

A Microsoft Network a ExpressRoute-áramkörök elsődleges és másodlagos kapcsolatainak aktív-aktív módban való üzemeltetésére van konfigurálva. Az útválasztási hirdetmények segítségével azonban kényszerítheti a ExpressRoute-áramkör redundáns kapcsolatait aktív-passzív üzemmódban való működésre. A további specifikus útvonalak és BGP elérési útjaként való hirdetése a közös módszer, amellyel az egyik elérési út előnyben részesített.

A magas rendelkezésre állás javítása érdekében javasoljuk, hogy az ExpressRoute-áramkör kapcsolatait aktív-aktív módban is kezelni lehessen. Ha lehetővé teszi, hogy a kapcsolatok aktív-aktív módban működjenek, a Microsoft Network a kapcsolatokon keresztül terheléselosztást hajt végre a forgalomban.

Ha egy ExpressRoute áramkör elsődleges és másodlagos kapcsolatait aktív-passzív módban futtatja, azzal a kockázattal jár, hogy mindkét kapcsolat meghiúsul az aktív elérési úton történt meghibásodás után. A váltás hibájának gyakori okai a passzív kapcsolat aktív felügyeletének hiánya, a passzív kapcsolatok pedig elavult útvonalak.

Egy ExpressRoute áramkör elsődleges és másodlagos kapcsolatainak aktív-aktív módban való futtatásával a ExpressRoute kapcsolódási hibáját követve csak a folyamatok felét, illetve az újrairányítást eredményező folyamatokat hajtja végre. Így az aktív-aktív üzemmód jelentősen segít javítani a helyreállításhoz szükséges átlagos időt (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT a Microsoft-partnerek számára 

A Microsoft-partnerek a nyilvános végpontok közötti kommunikációra szolgálnak. Így általában a helyszíni privát végpontok hálózati címfordítást végeznek a nyilvános IP-címmel az ügyfélen vagy a partner hálózaton, mielőtt a Microsofttal kommunikálnak. Ha az elsődleges és a másodlagos kapcsolatokat is aktív-aktív módban használja, a NAT-nak a ExpressRoute-kapcsolatok egyikének meghibásodása esetén milyen gyorsan kell helyreállítani a hibát. Az alábbi ábrán két különböző NAT-beállítás látható:

[![3]][3]

Az 1. lehetőségnél a NAT a ExpressRoute elsődleges és másodlagos kapcsolatai közötti forgalom felosztása után lesz alkalmazva. A NAT állapot-nyilvántartó követelményeinek teljesítéséhez független NAT-készletek vannak használatban az elsődleges és a másodlagos eszközök között, így a visszaküldött forgalom ugyanarra a peremhálózati eszközre fog érkezni, amelyen keresztül a folyamat egressed.

A 2. lehetőségnél általános NAT-készletet használ a ExpressRoute elsődleges és másodlagos kapcsolatai közötti forgalom felosztásához. Fontos, hogy a forgalom felosztása előtt a közös NAT-készlet megkülönböztetése ne vezessen be egypontos meghibásodást, így a magas rendelkezésre állást is veszélyeztetné.

Ha a ExpressRoute kapcsolódási hibát követően az 1. lehetőséget választja, a rendszer megszakítja a megfelelő NAT-készlet elérésének lehetőségét. Ezért az összes törött folyamatot újra kell létesíteni a TCP-vagy az alkalmazási rétegben, a megfelelő időkorlátot követve. Ha a NAT-készletek bármelyike felhasználható a helyszíni kiszolgálók bármelyikére, és ha a megfelelő kapcsolat meghiúsult, a helyszíni kiszolgálók nem érhetők el az Azure-ból, amíg a kapcsolat nem rögzül.

Míg a 2. lehetőséggel a NAT egy elsődleges vagy másodlagos kapcsolat meghibásodása után is elérhető. A hálózati réteg tehát maga is átirányíthatja a csomagokat, és a hiba után gyorsabban helyreállíthatja a helyreállítást. 

> [!NOTE]
> Ha 1. NAT-beállítást (független NAT-készletek elsődleges és másodlagos ExpressRoute-kapcsolatokhoz) használ, és az egyik NAT-készletből egy helyi kiszolgálóra rendel IP-címet, akkor a kiszolgáló nem érhető el a ExpressRoute áramkörön keresztül, ha a megfelelő kapcsolat meghiúsul.
> 

## <a name="fine-tuning-features-for-private-peering"></a>A privát társak finomhangolásához szükséges funkciók

Ebben a szakaszban a nem kötelező (az Azure-beli üzembe helyezéstől és a MTTR érzékeny adatoktól függően) áttekintését ajánljuk, amelyek segítenek a ExpressRoute-áramkör magas rendelkezésre állásának javításában. Pontosabban tekintse át a ExpressRoute virtuális hálózati átjárók és a kétirányú továbbítás észlelése (BFD) zóna-kompatibilis telepítését.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Rendelkezésre állási zóna – ExpressRoute virtuális hálózati átjárók

Az Azure-régiók rendelkezésre állási zónái egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha a IaaS-alapú Azure-beli üzembe helyezést választja, akkor érdemes lehet olyan zónában is konfigurálni a virtuális hálózati átjárókat, amelyek leállítják a ExpressRoute privát társait. További tudnivalókért tekintse meg a [virtuális hálózati átjárók a Azure Availability Zones-ben][zone redundant vgw]című témakört. A zóna-redundáns virtuális hálózati átjáró konfigurálásával kapcsolatban lásd: [zóna – redundáns virtuális hálózati átjáró létrehozása Azure Availability Zonesban][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>A hiba észlelési idejének javítása

A ExpressRoute támogatja a BFD. A BFD csökkenti a hiba észlelési idejét a 2. rétegbeli hálózat között a Microsoft Enterprise Edge (Msee) és a helyi oldalon lévő BGP-szomszédok között, körülbelül 3 percen belül (alapértelmezett), és kevesebb mint egy másodpercig. A gyors észlelési idő segít felgyorsítani a sikertelen helyreállítást. További információ: [BFD konfigurálása ExpressRoute][BFD]-en keresztül.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy ExpressRoute-áramköri kapcsolat magas rendelkezésre állásának kialakítását ismertetjük. Egy ExpressRoute-áramköri pont egy földrajzi helyre van rögzítve, ezért a teljes helyet érintő katasztrofális meghibásodás okozhatja. 

Ahhoz, hogy tervezési megfontolások legyenek olyan geo-redundáns hálózati kapcsolatok kiépítéséhez a Microsoft-gerinchez, amelyek a teljes régiót érintő katasztrofális hibákat okozhatnak, lásd: a vész- [helyreállítás megtervezése a ExpressRoute privát][DR]társításával.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ajánlott módszer a ExpressRoute használatával való kapcsolódásra"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "optimális, utolsó mérföldes kapcsolat"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-beállítás"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md