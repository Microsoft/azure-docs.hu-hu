---
title: Hálózatkezelési megfontolások – Azure dedikált HSM | Microsoft Docs
description: Az Azure dedikált HSM üzemelő példányokra vonatkozó hálózati megfontolások áttekintése
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91359154"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure dedikált HSM hálózatkezelés

Az Azure dedikált HSM-hez rendkívül biztonságos hálózati környezet szükséges. Ez igaz, hogy az Azure-felhőből vissza kell-e térni az ügyfél IT-környezetére (helyszíni), elosztott alkalmazások vagy magas rendelkezésre állási forgatókönyvek használatával. Az Azure hálózatkezelés biztosítja ezt, és négy különböző területet kell megoldani.

- HSM-eszközök létrehozása a Virtual Networkban (VNet) az Azure-ban
- A helyszíni és a felhőalapú erőforrások csatlakoztatása a HSM-eszközök konfigurálásához és kezeléséhez
- Virtuális hálózatok létrehozása és csatlakoztatása összekötő alkalmazás-erőforrások és HSM-eszközök között
- A virtuális hálózatok összekapcsolása régiók között a kommunikáció és a magas rendelkezésre állási helyzetek lehetővé tételéhez is

## <a name="virtual-network-for-your-dedicated-hsms"></a>A dedikált HSM virtuális hálózata

A dedikált HSM egy Virtual Networkba vannak integrálva, és az Azure-ban az ügyfelek saját magánhálózaton vannak elhelyezve. Ez lehetővé teszi az eszközök számára a virtuális hálózatban lévő virtuális gépek vagy számítási erőforrások elérését.  
További információ az Azure-szolgáltatások virtuális hálózathoz való integrálásáról és az általa biztosított képességekről: [Virtual Network for Azure Services](../virtual-network/virtual-network-for-azure-services.md) dokumentáció.

### <a name="virtual-networks"></a>Virtuális hálózatok

A dedikált HSM-eszközök kiépítés előtt az ügyfeleknek először létre kell hozniuk egy Virtual Network az Azure-ban, vagy az ügyfél-előfizetésben már meglévőt kell használniuk. A virtuális hálózat határozza meg a dedikált HSM-eszköz biztonsági kerületét. A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [Virtual Network dokumentáció](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alhálózatok

Az alhálózatok a virtuális hálózatot különálló, a bennük található Azure-erőforrások által használható címekre osztják. A dedikált HSM üzembe helyezése a virtuális hálózat egyik alhálózatán történik. Az ügyfél alhálózatán üzembe helyezett minden egyes dedikált HSM-eszköz kap egy magánhálózati IP-címet ebből az alhálózatból. Az alhálózatot, amelyben a HSM-eszközt telepítették, explicit módon delegálni kell a szolgáltatásnak: Microsoft. HardwareSecurityModules/dedicatedHSMs. Ez bizonyos engedélyeket biztosít a HSM szolgáltatásnak az alhálózatba történő üzembe helyezéshez. A dedikált HSM való delegálás bizonyos házirend-korlátozásokat ír elő az alhálózaton. A hálózati biztonsági csoportok (NSG) és a User-Defined útvonalak (UDR-EK) jelenleg nem támogatottak a delegált alhálózatokon. Ennek eredményeképpen, ha egy alhálózat dedikált HSM van delegálva, akkor csak a HSM-erőforrások üzembe helyezésére használható. A más ügyfelek erőforrásainak az alhálózatba való telepítése sikertelen lesz.


### <a name="expressroute-gateway"></a>ExpressRoute-átjáró

Az aktuális architektúra követelménye egy ABB-átjáró konfigurálása abban az ügyfelek alhálózatban, ahol HSM-eszközt kell elhelyezni a HSM-eszköz Azure-ba való integrálásának engedélyezéséhez. Ez az ABB-átjáró nem használható a helyszíni helyeknek az Azure-beli ügyfelek HSM-eszközeihez való csatlakoztatásához.

## <a name="connecting-your-on-premises-it-to-azure"></a>A helyszíni IT csatlakoztatása az Azure-hoz

Felhőalapú erőforrások létrehozásakor ez egy tipikus követelmény a privát kapcsolatok számára a helyszíni informatikai erőforrásokhoz. A dedikált HSM esetében ez elsősorban a HSM-ügyfélszoftver konfigurálására szolgál a HSM-eszközök, valamint olyan tevékenységek esetében, mint például a biztonsági mentések és a naplók HSM for Analysis. A legfontosabb döntési pont itt a kapcsolódás természete, mint lehetőség.  A legrugalmasabb lehetőség a helyek közötti VPN, mivel valószínűleg több helyszíni erőforrásra van szükség, amely biztonságos kommunikációt igényel az Azure-felhőben található erőforrásokkal (beleértve a HSM is). Ehhez szükség lesz egy ügyfél-szervezetre, hogy a kapcsolat megkönnyítése érdekében VPN-eszköz legyen. Pont – hely típusú VPN-kapcsolat akkor használható, ha csak egyetlen végpont a helyszínen, például egyetlen felügyeleti munkaállomás.
További információ a kapcsolódási lehetőségekről: [VPN Gateway tervezési beállítások](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Jelenleg a ExpressRoute nem áll rendelkezésre helyszíni erőforrásokhoz való kapcsolódásra. Azt is figyelembe kell venni, hogy a fent leírtak szerint használt ExpressRoute-átjáró nem a helyszíni infrastruktúrához való kapcsolódáshoz szükséges.

### <a name="point-to-site-vpn"></a>Pont – hely típusú VPN

A pont – hely virtuális magánhálózat a biztonságos kapcsolat legegyszerűbb formája a helyszíni egyetlen végponthoz. Ez akkor lehet hasznos, ha csak egyetlen felügyeleti munkaállomást szeretne használni az Azure-alapú dedikált HSM.

### <a name="site-to-site-vpn"></a>Helyek közötti VPN

A helyek közötti virtuális magánhálózat lehetővé teszi az Azure-alapú dedikált HSM és a helyszíni IT közötti biztonságos kommunikációt. Ennek oka, hogy a HSM helyszíni biztonsági mentési lehetőséggel rendelkezik, és a biztonsági mentés futtatásához kapcsolatra van szükség a kettő között.

## <a name="connecting-virtual-networks"></a>Virtuális hálózatok csatlakoztatása

A dedikált HSM tipikus telepítési architektúrája egyetlen virtuális hálózattal és a HSM-eszközök létrehozásához és üzembe helyezéséhez szükséges alhálózattal kezdődik. Ugyanezen a régión belül további virtuális hálózatok és alhálózatok is lehetnek a dedikált HSM-et használó alkalmazás-összetevők számára. Ezen hálózatok közötti kommunikáció engedélyezéséhez Virtual Network-társítást használunk.

### <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ha egy régión belül több virtuális hálózat is van, amelyeknek hozzá kell férniük egymás erőforrásaihoz, Virtual Network a társítással biztonságos kommunikációs csatornák hozhatók létre egymás között.  A virtuális hálózatok társítása nemcsak biztonságos kommunikációt tesz lehetővé, hanem alacsony késleltetésű és nagy sávszélességű kapcsolatokat is biztosít az Azure-beli erőforrások között.

![hálózati társítás](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Csatlakozás az Azure-régiók között

A HSM-eszközök lehetővé teszi, hogy a szoftvereken keresztül átirányítsák a forgalmat egy másik HSM-be. A forgalom átirányítása akkor hasznos, ha az eszközök meghibásodása vagy az eszközhöz való hozzáférés elvész. A regionális szintű meghibásodási forgatókönyvek a HSM más régiókban való üzembe helyezésével csökkenthetők, és a virtuális hálózatok közötti kommunikációt is lehetővé teszik a régiók között.

### <a name="cross-region-ha-using-vpn-gateway"></a>Régión átívelő, HA VPN-átjárót használ

Globálisan elosztott alkalmazások esetén vagy magas rendelkezésre állású regionális feladatátvételi helyzetekben a virtuális hálózatok régiók közötti összekapcsolására van szükség. Az Azure dedikált HSM-vel a magas rendelkezésre állást olyan VPN Gateway használatával lehet megvalósítani, amely biztonságos alagutat biztosít a két virtuális hálózat között. A VPN Gateway használatával történő vnet-kapcsolatokkal kapcsolatos további információkért tekintse meg a [mi VPN Gateway?](../vpn-gateway/design.md#V2V) című cikket.

> [!NOTE]
> A globális vnet-társítás jelenleg nem érhető el a régiók közötti kapcsolódási forgatókönyvekben a dedikált HSM, és helyette a VPN-átjárót kell használni. 

![Az ábrán két, két V P N átjáróval összekapcsolt régió látható. Minden régió egyenrangú virtuális hálózatokat tartalmaz.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Következő lépések

- [Gyakori kérdések](faq.md)
- [Támogatási lehetőségek](supportability.md)
- [Magas rendelkezésre állás](high-availability.md)
- [Fizikai biztonság](physical-security.md)
- [Figyelés](monitoring.md)
- [Üzembe helyezési architektúra](deployment-architecture.md)