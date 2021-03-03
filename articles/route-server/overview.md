---
title: Mi az az Azure Route Server (előzetes verzió)?
description: Ismerje meg, hogy az Azure Route Server hogyan egyszerűsítheti le az útválasztást a hálózati virtuális berendezés (NVA) és a virtuális hálózata között.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 98527ea5227fcdc2c35dbe3579657368e30fcdaa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680623"
---
# <a name="what-is-azure-route-server-preview"></a>Mi az az Azure Route Server (előzetes verzió)? 

Az Azure Route Server leegyszerűsíti a dinamikus útválasztást a hálózati virtuális berendezés (NVA) és a virtuális hálózat között. Lehetővé teszi, hogy az útválasztási adatokat közvetlenül Border Gateway Protocol (BGP) útválasztási protokollon keresztül továbbítsa a BGP útválasztási protokollt és az Azure-Virtual Network (VNET) az Azure-beli szoftveresen definiált hálózati (SDN) protokollt támogató NVA között anélkül, hogy manuálisan kellene konfigurálni vagy karbantartani az útválasztási táblákat. Az Azure Route Server egy teljes körűen felügyelt szolgáltatás, és magas rendelkezésre állással van konfigurálva.

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Hogyan működik?

Az alábbi ábra azt szemlélteti, hogyan működik az Azure Route Server egy SDWAN-NVA és egy virtuális hálózatban található biztonsági NVA. Miután létrehozta a BGP-társat, az Azure Route Server helyszíni útvonalat (10.250.0.0/16) kap a SDWAN készülékről, valamint egy alapértelmezett útvonalat (0.0.0.0/0) a tűzfalból. Ezeket az útvonalakat a rendszer automatikusan konfigurálja a virtuális hálózatban lévő virtuális gépeken. Ennek eredményeképpen a helyszíni hálózatra irányuló összes forgalom a SDWAN készülékre lesz küldve. Amíg az összes internetre kötött forgalom el lesz küldve a tűzfalra. Az ellenkező irányba az Azure Route Server elküldi a virtuális hálózati címeket (10.1.0.0/16) mindkét NVA. A SDWAN készülék tovább propagálhatja a helyszíni hálózatra.

![A virtuális hálózatban konfigurált Azure Route Servert bemutató ábra.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Főbb előnyök 

Az Azure Route Server leegyszerűsíti a NVA konfigurálását, felügyeletét és üzembe helyezését a virtuális hálózaton.  

* Többé nem kell manuálisan frissítenie az útválasztási táblázatot a NVA, amikor a virtuális hálózati címei frissülnek. 

* Többé nem kell manuálisan frissítenie a [felhasználó által megadott útvonalakat](../virtual-network/virtual-networks-udr-overview.md) , amikor a NVA új útvonalakat jelent, vagy visszavonja a régieket. 

* Többé nem kell konfigurálnia a terheléselosztó-t a NVA előtt a rugalmasság és a teljesítmény érdekében. Ha a NVA több példányát az Azure Route Serverrel együtt futtatja, akkor a BGP-attribútumokat a NVA konfigurálhatja. Ezek a BGP-attribútumok lehetővé teszik az Azure Route Server számára, hogy a NVA-példánynak aktívnak vagy passzívnak kell lennie. 

* A NVA és az Azure Route Server közötti interfész egy közös szabványú protokollon alapul. Ha a NVA támogatja a BGP-t, az Azure Route Serverrel is használhatja. További információ: az Útválasztás- [kiszolgáló által támogatott útválasztási protokollok](route-server-faq.md#protocol).

* Az Azure Route Servert bármely új vagy meglévő virtuális hálózatban üzembe helyezheti. 

## <a name="faq"></a>GYIK

Az Azure Route Serverrel kapcsolatos gyakori kérdésekért lásd: [Azure Route Server – gyakori](route-server-faq.md)kérdések.

## <a name="next-steps"></a>Következő lépések

- [Ismerje meg, hogyan konfigurálhatja az Azure Route Servert.](quickstart-configure-route-server-portal.md)
- [Ismerje meg, hogyan működik az Azure Route Server az Azure ExpressRoute és az Azure VPN-sel](expressroute-vpn-support.md)
