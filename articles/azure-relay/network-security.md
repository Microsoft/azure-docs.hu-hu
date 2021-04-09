---
title: Azure Relay hálózati biztonsága
description: Ez a cikk azt ismerteti, hogyan használhatók az IP-tűzfalszabályok és a magánhálózati végpontok a Azure Relay használatával.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 735a0b7dc303ad63cb301151e3e14e4488c856b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217243"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay hálózati biztonsága 
Ez a cikk azt ismerteti, hogyan használhatók a következő biztonsági szolgáltatások a Azure Relay használatával: 

- IP-tűzfalszabályok
- Privát végpontok 

> [!NOTE]
> A Azure Relay nem támogatja a hálózati szolgáltatás végpontját. 


## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint a továbbítási névterek elérhetők az internetről, feltéve, hogy a kérelem érvényes hitelesítést és engedélyezést tartalmaz. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Relay csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)használatával használ továbbítót, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 

Az IP-tűzfalszabályok a továbbítási névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. A továbbítási névtérben lévő engedélyezett IP-szabálynak nem megfelelő IP-címről érkező kapcsolódási kísérleteket a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

További információ: az [IP-tűzfal konfigurálása továbbító névtérhez](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Privát végpontok

Az Azure **Private link Service** lehetővé teszi az Azure-szolgáltatások (például a Azure Relay, a Azure Service Bus, az Azure Event Hubs, az Azure Storage és a Azure Cosmos db) és az Azure által üzemeltetett ügyfél-partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

A **privát végpont** egy olyan hálózati adapter, amely lehetővé teszi, hogy a virtuális hálózaton futó munkaterhelések privát és biztonságos módon kapcsolódjanak egy olyan szolgáltatáshoz, amelynek van **privát kapcsolati erőforrása** (például egy továbbító névtér). A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute, VPN-kapcsolatra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom áthalad a Microsoft gerinc hálózatán, és így megszűnik a nyilvános internetről való kitettség. Megadhatja a hozzáférés-vezérlés részletességi szintjét azáltal, hogy engedélyezi a kapcsolódást az adott Azure Relay névterekhez.

További információ: [privát végpontok konfigurálása](private-link-service.md)


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal konfigurálása](ip-firewall-virtual-networks.md)
- [Privát végpontok konfigurálása](private-link-service.md)
