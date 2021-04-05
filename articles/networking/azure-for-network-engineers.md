---
title: 'Azure ExpressRoute: Azure hálózati mérnököknek'
description: Ez az oldal ismerteti a hagyományos hálózati mérnököket, hogy a hálózatok hogyan működnek az Azure-ban.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234205"
---
# <a name="azure-for-network-engineers"></a>Azure hálózattervezőknek
Hagyományos hálózati mérnökként olyan fizikai eszközökkel foglalkozott, mint az útválasztók, kapcsolók, kábelek, tűzfalak az infrastruktúra kiépítéséhez. Egy logikai rétegben konfigurálta a virtuális helyi hálózatot (VLAN), az átfedő faprotokollot (STP), az útválasztási protokollokat (RIP, OSPF, BGP). Felügyeli a hálózatot a felügyeleti eszközök és a parancssori felület használatával. A felhőben való hálózatkezelés más, mint a hálózati végpontok logikai és az útválasztási protokollok használata minimális. Az Azure-beli eszközök konfigurálásához és kezeléséhez Azure Resource Manager API-val, az Azure CLI-vel és a PowerShell-lel fog dolgozni. A hálózati utazást a felhőben az Azure Networking alapbérlők megismerésével indíthatja el. 
## <a name="virtual-network"></a>Virtuális hálózat
Ha alulról tervez egy hálózatot, néhány alapvető információt gyűjthet. Ezek az adatok lehetnek a gazdagépek, a hálózati eszközök, az alhálózatok száma, az alhálózatok közötti útválasztás, az elkülönítési tartományok, például a VLAN-ok. Ez az információ segít a hálózati és biztonsági eszközök méretezésében, valamint az alkalmazások és szolgáltatások támogatásához szükséges architektúra létrehozásában.

Az alkalmazások és szolgáltatások az Azure-ban való üzembe helyezése során először hozzon létre egy logikai határt az Azure-ban, amelyet virtuális hálózatnak nevezünk. Ez a virtuális hálózat egy fizikai hálózati határhoz hasonlít. Mivel ez egy virtuális hálózat, nincs szükség fizikai sebességre, de továbbra is meg kell terveznie a logikai entitásokat, például az IP-címeket, az IP-alhálózatokat, az útválasztást és a házirendeket.

Amikor létrehoz egy virtuális hálózatot az Azure-ban, az előre konfigurálva van egy IP-tartománnyal (10.0.0.0/16). Ez a tartomány nincs kijavítva, saját IP-címtartományt is meghatározhat. Az IPv4-és IPv6-címtartományok is meghatározhatók. A virtuális hálózathoz megadott IP-címtartományok nincsenek meghirdetve az interneten. Az IP-címtartományból több alhálózatot is létrehozhat. Ezeket az alhálózatokat a rendszer a virtuális hálózati adapterek (Vnic) IP-címeinek hozzárendelésére használja. Az egyes alhálózatokból származó első négy IP-cím le van foglalva, és nem használható IP-kiosztásra. A nyilvános felhőben nincs a VLAN-ok fogalma. Létrehozhat azonban egy virtuális hálózaton belüli elkülönítést a definiált alhálózatok alapján.

Létrehozhat egy nagyméretű alhálózatot, amely magában foglalja az összes virtuális hálózati címtartomány létrehozását, vagy dönthet úgy, hogy több alhálózatot is létrehoz. Ha azonban virtuális hálózati átjárót használ, az Azure-nak létre kell hoznia egy "átjáró-alhálózat" nevű alhálózatot. Az Azure ezt az alhálózatot fogja használni az IP-címek virtuális hálózati átjárók számára történő hozzárendeléséhez. 

## <a name="ip-allocation"></a>IP-foglalás

Ha IP-címet rendel hozzá egy gazdagéphez, az IP-címeket egy hálózati adapterhez (NIC) rendeli hozzá. Az Azure-ban kétféle IP-címet rendelhet egy hálózati adapterhez:

- Nyilvános IP-címek – a bejövő és kimenő (hálózati címfordítás (NAT) nélküli) kommunikációhoz használható az internettel és más Azure-erőforrásokkal, amelyek nem csatlakoznak virtuális hálózathoz. Nem kötelező nyilvános IP-címet rendelni egy hálózati adapterhez. A nyilvános IP-címek a Microsoft IP-címe területhez tartoznak.
- Magánhálózati IP-címek – virtuális hálózaton, a helyszíni hálózaton és az interneten (NAT) belüli kommunikációhoz használható. A virtuális hálózatban definiált IP-címtartomány a nyilvános IP-címtartomány konfigurálásakor is magánjellegűnek számít. A Microsoft nem hirdeti meg ezt a helyet az interneten. Legalább egy magánhálózati IP-címet hozzá kell rendelni egy virtuális géphez.

A fizikai gazdagépekhez vagy eszközökhöz hasonlóan kétféleképpen lehet IP-címet lefoglalni egy erőforráshoz – dinamikus vagy statikus. Az Azure-ban az alapértelmezett kiosztási módszer a dinamikus, ahol a virtuális gép létrehozásakor és a leállított virtuális gépek indításakor a rendszer IP-címet foglal le. Az IP-cím akkor szabadul fel, ha leállítja vagy törli a virtuális gépet. A lefoglalási módszert állíthatja statikusra is, hogy a virtuális géphez tartozó IP-címek változatlanok maradjanak. Ebben az esetben a rendszer azonnal kioszt egy IP-címet. Az IP-cím csak akkor szabadul fel, ha törli a virtuális gépet, vagy dinamikusra változtatja a lefoglalási módszert. 

A magánhálózati IP-címek kiosztása a virtuális hálózaton belül definiált alhálózatokból történik. Egy virtuális gép esetében válasszon egy alhálózatot az IP-foglaláshoz. Ha egy virtuális gép több hálózati adaptert is tartalmaz, választhat egy másik alhálózatot az egyes hálózati adapterekhez.

## <a name="routing"></a>Útválasztás
Virtuális hálózat létrehozásakor az Azure létrehoz egy útválasztási táblázatot a hálózathoz. Ez az útválasztási táblázat a következő típusú útvonalakat tartalmazza.
- Rendszerútvonalak
- Alhálózat alapértelmezett útvonalai
- Más virtuális hálózatok útvonalai
- BGP-útvonalak
- Szolgáltatás-végponti útvonalak
- Felhasználó által megadott útvonalak (UDR)

Amikor első alkalommal hoz létre egy virtuális hálózatot az alhálózatok meghatározása nélkül, az Azure útválasztási bejegyzéseket hoz létre az útválasztási táblában. Ezeket az útvonalakat rendszerútvonalnak nevezzük. A rendszerútvonalak ezen a helyen vannak meghatározva. Ezeket az útvonalakat nem módosíthatja. A UDR konfigurálásával azonban felülbírálhatja a rendszerek útvonalait.

Ha egy vagy több alhálózatot hoz létre egy virtuális hálózaton belül, az Azure az útválasztási táblázatban alapértelmezett bejegyzéseket hoz létre, amelyek lehetővé teszik a virtuális hálózaton belüli alhálózatok közötti kommunikációt. Ezek az útvonalak a felhasználó által megadott útvonalak (UDR-EK) statikus útvonalak használatával módosíthatók az Azure-ban.

Ha két virtuális hálózat között hoz létre virtuális hálózatot, a rendszer minden egyes címtartomány számára létrehoz egy útvonalat, amely az egyes virtuális hálózatok közötti címtartomány-társítást hozza létre.

Ha a helyszíni hálózati átjáró a BGP-útvonalakat egy Azure-beli virtuális hálózati átjáróval cseréli, a helyszíni hálózati átjáróról propagált minden egyes útvonalhoz hozzá kell adni egy útvonalat. Ezek az útvonalak BGP-útvonalakként jelennek meg az útválasztási táblázatban.

Bizonyos szolgáltatások nyilvános IP-címeit az Azure adja hozzá az útválasztási táblázathoz, amikor engedélyezi egy szolgáltatás-végpontot a szolgáltatás számára. A szolgáltatási végpontok engedélyezve vannak a virtuális hálózaton belüli egyes alhálózatokon. Ha engedélyezi a szolgáltatási végpontot, az útvonal csak a szolgáltatáshoz tartozó alhálózat útválasztási táblájához lesz hozzáadva. Az Azure automatikusan kezeli az útvonaltáblában lévő címeket a címek változásakor.

A felhasználó által definiált útvonalakat egyéni útvonalaknak is nevezzük. UDR hozhat létre az Azure-ban az Azure alapértelmezett rendszerútvonalának felülbírálásához, vagy további útvonalakat adhat hozzá az alhálózat útválasztási táblájához. Az Azure-ban hozzon létre egy útválasztási táblázatot, majd rendelje hozzá az útválasztási táblázatot a virtuális hálózati alhálózatokhoz.

Ha versengő bejegyzéseket tartalmaz egy útválasztási táblában, az Azure a leghosszabb előtag alapján kiválasztja a következő ugrást, amely a hagyományos útválasztókhoz hasonlít. Ha azonban több következő ugrási bejegyzés van ugyanazzal a előtaggal, akkor az Azure a következő sorrendben kiválasztja az útvonalakat.
1. Felhasználó által megadott útvonalak (UDR)
1. BGP-útvonalak
1. Rendszerútvonalak

## <a name="security"></a>Biztonság

Hálózati biztonsági csoportok használatával szűrheti a virtuális hálózatban lévő erőforrásokra irányuló és onnan érkező hálózati forgalmat. Használhat hálózati virtuális berendezéseket (NVA) is, például Azure Firewall vagy más gyártóktól származó tűzfalakat. Megadhatja, hogyan irányítja az Azure az alhálózatokból érkező forgalmat. Azt is korlátozhatja, hogy a szervezeten belül kik is dolgozhatnak a virtuális hálózatok erőforrásaival.

A hálózati biztonsági csoport (NSG) tartalmazza a hozzáférés-vezérlési (ACL) szabályok listáját, amelyek megszabják, hogy milyen típusú hálózati forgalom juthat el az alhálózatokhoz, a hálózati adapterekhez vagy mindkettőhöz. Az NSG-ket alhálózatokhoz vagy alhálózathoz csatlakoztatott hálózati adapterekhez lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuális gépre. Emellett egy adott hálózati adapterre irányuló forgalmat korlátozni is lehet azáltal, hogy egy NSG-t közvetlenül a hálózati adapterhez rendelnek.

Az NSG-k két szabálykészletet tartalmaznak: bejövőt és kimenőt. A szabály prioritásának az egyes készleten belül kell egyedinek lennie. Minden szabály rendelkezik protokolltulajdonságokkal, forrás- és célport-tartományokkal, címelőtagokkal, forgalomiránnyal, prioritással és hozzáférési típussal. Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat.

## <a name="verification"></a>Ellenőrzés
### <a name="routes-in-virtual-network"></a>Útvonalak a virtuális hálózaton
A létrehozott útvonalak, az Azure alapértelmezett útvonalak és a helyszíni hálózatról az Azure VPN Gateway használatával propagált útvonalak kombinációja (ha a virtuális hálózat a helyszíni hálózathoz csatlakozik) a Border Gateway Protocol (BGP) használatával történik, az egy alhálózat összes hálózati adapterének érvényes útvonala. Ezeket a hatékony útvonalakat úgy tekintheti meg, ha a portálon, a PowerShellen vagy a CLI-n keresztül navigál a hálózati adapterhez.
### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A hálózati adapterre alkalmazott érvényes biztonsági szabályok a hálózati adapterhez társított NSG található szabályok összesítései, valamint a hálózati adaptert tartalmazó alhálózat. A virtuális gép hálózati adapterén alkalmazott NSG érvényes biztonsági szabályokat a portálon, a PowerShellen vagy a CLI-n keresztül navigálva tekintheti meg a hálózati ADAPTERen.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a [Virtual networkről][VNet].

Tudnivalók a [virtuális hálózatok útválasztásáról][vnet-routing].

További információ a [hálózati biztonsági csoportokról][network-security].

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md