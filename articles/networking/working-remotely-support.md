---
title: Távoli munka az Azure Networking Services használatával
description: Ez a lap leírja, hogyan használhatja az Azure hálózati szolgáltatásokat, amelyek lehetővé teszik a távoli munkát, valamint a távolról dolgozó személyek számának növelését eredményező forgalmi problémák enyhítését.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98231842"
---
# <a name="working-remotely-using-azure-networking-services"></a>Távoli munka az Azure Networking Services használatával

>[!NOTE]
> Ez a cikk azt ismerteti, hogyan használhatja az Azure hálózati szolgáltatásokat, a Microsoft hálózatot és az Azure partneri ökoszisztémát, hogy távolról működjön, és enyhítse a COVID-19 válság miatt esetlegesen felmerülő hálózati problémákat.

Ez a cikk azokat a lehetőségeket ismerteti, amelyekkel a szervezetek távelérést állíthatnak be a felhasználók számára, illetve a meglévő megoldásaikat a maximális kihasználtság időtartama alatt további kapacitással kiegészítve. A hálózati építészek az alábbi kihívásokkal szembesülnek:

- A hálózati kihasználtság növelésének kezelése.
- Megbízhatóan biztonságos kapcsolatot biztosít a vállalata és ügyfelei több alkalmazottja számára.
- A távoli helyekhez való kapcsolódás biztosítása a világ minden részén.

Nem minden hálózat (például a privát WAN és a vállalati alaphálózatok) torlódást tapasztal a távoli munkavégző terhelése alól. A szűk keresztmetszeteket általában csak a Home broadband Networks és a vállalati hálózatok helyi hálózatának VPN-átjárói jelentik.

A hálózati tervezők megkönnyítik a szűk keresztmetszetek és a hálózati torlódások enyhítését azáltal, hogy a különböző típusú adatforgalmi típusoknak eltérő hálózati kezelési prioritásokra, valamint egy intelligens betöltési átirányítás/terjesztésre van szükségük. Például a valós idejű médecine-forgalom az orvos-beteg interakció nagy fontossággal bír, és a késleltetés/Jitter érzékeny. Míg a tárolók közötti azonos forgalom replikálása nem késlelteti a bizalmas adatokat. A korábbi forgalmat a legoptimálisabb hálózati útvonalon keresztül kell átirányítani a magasabb színvonalú szolgáltatással. mivel a későbbi adatforgalom az optimálisnál rosszabb útvonalon keresztül irányítható.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Ajánlott eljárások megosztása – az Azure-hálózat rugalmasságot és magas rendelkezésre állást biztosít

Az Azure úgy lett kialakítva, hogy ellenálljanak az erőforrások kihasználtságának hirtelen változásának, és nagy mértékben segítséget nyújtson a maximális kihasználtság időtartama alatt. A Microsoft a világ egyik legnagyobb hálózatát is karbantartja és üzemelteti. A Microsoft hálózata olyan magas rendelkezésre állásra készült, amely képes ellenállni a különböző típusú hibáknak: egyetlen hálózati elem meghibásodása esetén a teljes régió meghiúsul.

A Microsoft hálózat úgy van kialakítva, hogy megfeleljen a követelményeknek, és optimális teljesítményt biztosítson a különböző típusú hálózati forgalomhoz, beleértve a Skype és a csapatok, a CDN, a valós idejű big data elemzés, az Azure Storage, a Bing és az Xbox közötti érzékeny multimédiás forgalmat. A különböző típusú forgalom optimális teljesítményének biztosítása érdekében a Microsoft-hálózat minden olyan forgalmat felhasznál, amely a-re irányul, vagy az erőforrásokon keresztül a lehető leghamarabb áthalad a forgalom forrásaként.

>[!NOTE] 
>Az alábbi Azure-hálózatkezelési funkciók használatával a Microsoft globális hálózatának adatforgalmi vonzereje kihasználható a jobb ügyfél-hálózatkezelési élmény biztosítása érdekében. A forgalom vonzereje a Microsoft-hálózatnak köszönhetően a lehető leghamarabb betöltődik a forgalomból az első/utolsó mérföldes hálózatról, amely torlódást tapasztalhat a maximális kihasználtság időtartama alatt.
>

## <a name="enable-employees-to-work-remotely"></a>Az alkalmazottak távolról való működésének engedélyezése

Az Azure VPN Gateway mind a pont – hely (P2S), mind a helyek közötti (S2S) VPN-kapcsolatokat támogatja. Az Azure VPN Gateway használatával méretezheti az alkalmazottak kapcsolatait, hogy biztonságosan hozzáférjenek az Azure-beli üzembe helyezett erőforrásokhoz és a helyszíni erőforrásokhoz is. További információ: [a felhasználók távoli működésének engedélyezése](../vpn-gateway/work-remotely-support.md). 

Ha az SSTP protokollt használja, az egyidejű kapcsolatok száma 128-ra van korlátozva. Ha nagyobb számú kapcsolatot szeretne kapni, javasoljuk, hogy váltson az OpenVPN-re vagy a IKEv2-ra. További információ: [áttérés az OpenVPN protokollra vagy a IKEv2 az SSTP-ből](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Az Azure-ban üzembe helyezett erőforrások eléréséhez a távoli fejlesztők az Azure-beli megerősített megoldást használhatják VPN-kapcsolat helyett a biztonságos rendszerhéj-hozzáféréshez (RDP vagy SSH) anélkül, hogy nyilvános IP-címeket kellene megadniuk az elérni kívánt virtuális gépeken. További információ: [távoli munka az Azure Bastion használatával](../bastion/work-remotely-support.md).

Nagyméretű VPN-kapcsolat összevonása érdekében a különböző helyszíni globális helyeken lévő erőforrások közötti, különböző regionális és küllős virtuális hálózatokon belüli kapcsolatok támogatása, valamint a több otthoni szélessávú hálózat kihasználtságának optimalizálása érdekében használhatja az Azure Virtual WAN-ot. További információkért lásd: az [otthoni igények kielégítése? Itt találja az Azure Virtual WAN segítségét](../virtual-wan/work-remotely-support.md).

A távoli munkaerő támogatásának egy másik módja az Azure-beli virtuális hálózatban üzemeltetett virtuális asztali infrastruktúra (VDI) üzembe helyezése, amelyet egy Azure Firewall biztosítanak. Például a Windows Virtual Desktop (WVD) az Azure-ban futó asztali és app Virtualization szolgáltatás. A Windows virtuális asztal használatával méretezhető és rugalmas környezetet állíthat be az Azure-előfizetésben anélkül, hogy további átjárókiszolgáló-kiszolgálókat kellene futtatnia. Csak a virtuális hálózat WVD virtuális gépei felelősek. További információ: [Azure Firewall távoli munkahelyi támogatás](../firewall/remote-work-support.md). 

Az Azure számos Eco-partnert is tartalmaz. Partnereink hálózati virtuális berendezések az Azure-ban is segíthetnek a VPN-kapcsolatok méretezésében. További információ: [Network Virtual Appliance (NVA) megfontolások a távoli működéshez](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Az alkalmazottak kapcsolatának kiterjesztése a globálisan elosztott erőforrások elérésére

A következő Azure-szolgáltatások segíthetnek az alkalmazottaknak a globálisan elosztott erőforrások elérésében. Erőforrásai lehetnek az Azure-régiókban, a helyszíni hálózatokban, vagy akár más nyilvános vagy privát felhőkben is. 

- **Azure-beli virtuális hálózati** társítás: Ha több Azure-régióban helyezi üzembe az erőforrásokat, és/vagy ha a távoli munkatársainak több virtuális hálózattal való összevonásával összekapcsolja a kapcsolatot, a virtuális hálózati társítást használó több Azure-beli virtuális hálózat között kapcsolatot létesíthet. További információ: [Virtual Network peering][VNet-peer].

- **Azure VPN-alapú megoldás**: az Azure-hoz P2S vagy S2S VPN-en keresztül csatlakozó távoli alkalmazottak számára engedélyezheti a helyszíni hálózatokhoz való hozzáférést úgy, hogy a S2S VPN-t konfigurálja a helyszíni hálózatok és az Azure VPN Gateway között. További információ: helyek közötti [kapcsolat létrehozása][S2S].

- **ExpressRoute**: a ExpressRoute privát társításának használata lehetővé teszi, hogy az Azure-környezetek és a helyszíni infrastruktúra, illetve az infrastruktúra közös elhelyezési létesítményben való magánhálózati kapcsolata is elérhető legyen. A Microsoft-ExpressRoute keresztül a helyszíni hálózatról is engedélyezi a nyilvános végpontok elérését. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Biztonságos kapcsolatot, megbízhatóságot, nagyobb átviteli sebességet biztosítanak, és alacsonyabb és konzisztens késéssel rendelkeznek, mint a szokásos kapcsolatok az interneten. További információkat az [ExpressRoute áttekintésében][ExR] találhat. A [ExpressRoute partner ökoszisztémájának][ExR-eco] részét képező meglévő hálózati szolgáltató kihasználása segít csökkenteni a nagy sávszélességű kapcsolatok elérésének idejét a Microsoft számára.  A [ExpressRoute Direct][ExR-D] használatával közvetlenül kapcsolódhat a helyszíni hálózathoz a Microsoft-gerinchez. A ExpressRoute Direct két különböző, kettős 10 GB/s vagy 100 GB/s sebességű kétirányú lehetőséget kínál. 

- **Azure Virtual WAN**: az Azure Virtual WAN lehetővé teszi a VPN-kapcsolatok és a ExpressRoute-áramkörök zökkenőmentes együttműködését. Ahogy azt korábban említettük, az Azure Virtual WAN a különböző helyszíni, különböző régiókban és küllős virtuális hálózatokban lévő erőforrások közötti kapcsolatokat is támogatja.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Ügyfélkapcsolatok méretezése a frontend erőforrásaihoz

Amikor egyre több ember online állapotba kerül, számos vállalati webhely növeli az ügyfelek forgalmát. Az Azure Application Gateway segítségével kezelheti ezt a nagyobb előtér-számítási feladatot. További információ: [Application Gateway nagy forgalmú támogatás](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-támogatás a többfelhős forgalomhoz

Más nyilvános felhőkben üzemelő példányok esetén a Microsoft globális kapcsolatot tud biztosítani. Az Azure Virtual WAN, VPN vagy ExpressRoute segíthetnek ebben a tekintetben. Az Azure-ból a többi felhőhöz való kapcsolódás kiterjesztéséhez konfigurálhatja a S2S VPN-t a két felhő között. Az Azure-ból más nyilvános felhőkhez is létesíthet kapcsolatot az ExpressRoute használatával. Az Oracle Cloud a ExpressRoute partner ökoszisztémájának része. [Beállíthat egy közvetlen kapcsolatot az Azure és az Oracle felhőalapú infrastruktúra között][Az-OCI]. A ExpressRoute partner ökoszisztémájának részét képező szolgáltatók többsége privát kapcsolatot is biztosít más nyilvános Felhőkkel. Ezen szolgáltatók kihasználása révén privát kapcsolatot létesíthet az Azure-beli és más felhőkben üzemelő példányok között a ExpressRoute-on keresztül.

## <a name="next-steps"></a>Következő lépések

A következő cikkek azt ismertetik, hogyan használhatók a különböző Azure-hálózatkezelési funkciók a felhasználók távoli működésének méretezéséhez:

| **Cikk** | **Leírás** |
| --- | --- |
| [A felhasználók távoli működésének engedélyezése](../vpn-gateway/work-remotely-support.md) | Tekintse át a rendelkezésre álló lehetőségeket a távelérés felhasználók számára való beállításához, illetve a meglévő megoldások kiegészítéséhez a szervezete számára további kapacitással.|
| [Az otthoni igényeknek megfelelően működik? Itt látható, hogy az Azure Virtual WAN hogyan segíthet](../virtual-wan/work-remotely-support.md) | Az Azure Virtual WAN használatával kezelheti a szervezet távoli kapcsolati igényeit.|
| [Nagy mennyiségű forgalom támogatása az Application Gatewayben](../application-gateway/high-traffic-support.md) | A Web Application Firewall (WAF) Application Gateway használatával méretezhető és biztonságos módon kezelheti a webalkalmazások forgalmát. |
| [A hálózati virtuális berendezés (NVA) szempontjai távoli munkához](../vpn-gateway/nva-work-remotely-support.md)|A távelérési megoldások biztosításához tekintse át a NVA az Azure-ban való kihasználásával kapcsolatos útmutatót. |
| [Áttérés az OpenVPN protokollra vagy az SSTP-IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md) | A 128 párhuzamos kapcsolati korlátjának leküzdése az OpenVPN protokollra vagy a IKEv2-re való áttéréssel.|
| [Távoli munka az Azure Bastion használatával](../bastion/work-remotely-support.md) | Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosíthat a virtuális gépekhez az Azure-beli virtuális hálózaton belül, közvetlenül a Azure Portal, nyilvános IP-cím használata nélkül. |
| [Hibrid kapcsolat létrehozása az Azure ExpressRoute használatával a távoli felhasználók támogatásához](../expressroute/work-remotely-support.md) | A hibrid kapcsolat ExpressRoute használatával engedélyezheti a szervezet felhasználói számára a távoli munkát.|
| [Azure Firewall távoli munkahelyi támogatás](../firewall/remote-work-support.md)|Az Azure-beli virtuális hálózati erőforrások Azure Firewall használatával védhetők. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md