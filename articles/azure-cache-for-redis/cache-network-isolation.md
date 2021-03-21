---
title: Azure cache a Redis hálózati elkülönítési lehetőségeihez
description: Ebből a cikkből megtudhatja, hogyan határozhatja meg az igényeinek legmegfelelőbb hálózati elkülönítési megoldást. Elsajátítjuk az Azure Private link, az Azure Virtual Network (VNet) injekciójának alapjait, és Azure Firewall szabályokat az előnyökkel és korlátozásokkal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621518"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure cache a Redis hálózati elkülönítési lehetőségeihez 
Ebből a cikkből megtudhatja, hogyan határozhatja meg az igényeinek legmegfelelőbb hálózati elkülönítési megoldást. Elsajátítjuk az Azure Private link, az Azure Virtual Network (VNet) injekciójának alapjait, és Azure Firewall szabályokat az előnyökkel és korlátozásokkal.  

## <a name="azure-private-link-public-preview"></a>Azure Private-hivatkozás (nyilvános előzetes verzió) 
Az Azure Private link privát kapcsolatot biztosít egy virtuális hálózatról az Azure Pásti szolgáltatásaiba. A szolgáltatás leegyszerűsíti a hálózati architektúrát, és az Azure-beli végpontok közötti kapcsolatot a nyilvános internetre való adatvédelem megszüntetésével biztosítja. 

### <a name="advantages"></a>Előnyök
* A Redis-példányok alapszintű, standard és prémium szintű Azure gyorsítótárában támogatott. 
* Az [Azure Private link](../private-link/private-link-overview.md)használatával egy privát végponton keresztül csatlakozhat egy Azure cache-példányhoz a virtuális hálózatból, amely egy magánhálózati IP-címet kap a virtuális hálózaton belüli alhálózatban. Ezzel a gyorsítótár-példányok mind a VNet, mind a nyilvánosan elérhetők.  
* Miután létrehozta a privát végpontot, a nyilvános hálózathoz való hozzáférés a jelzőn keresztül korlátozható `publicNetworkAccess` . Ez a jelző alapértelmezés szerint be van állítva `Disabled` , ami csak a privát hivatkozások elérését engedélyezi. Megadhatja az értéket a vagy a értékre `Enabled` `Disabled` egy patch kéréssel. További információ: [Azure cache for Redis with Azure Private link (előzetes verzió)](cache-private-link.md). 
* Az összes külső gyorsítótár-függőség nem befolyásolja a VNet NSG-szabályait.

### <a name="limitations"></a>Korlátozások 
* A hálózati biztonsági csoportok (NSG-EK) le vannak tiltva a magánhálózati végpontok esetében. Ha azonban más erőforrások is vannak az alhálózaton, akkor a NSG kényszerítés is érvényes lesz ezekre az erőforrásokra.
* A Geo-replikáció, a tűzfalszabályok, a portál konzol támogatása, a fürtözött gyorsítótár több végpontja, a tűzfal és a VNet Beinjektált gyorsítótárai még nem támogatottak. 
* Fürtözött gyorsítótárhoz való kapcsolódáshoz be kell `publicNetworkAccess` állítani a-t, `Disabled` és csak egyetlen privát végponti kapcsolat lehet.

> [!NOTE]
> Ha privát végpontot ad hozzá egy gyorsítótár-példányhoz, a DNS miatt a rendszer az összes Redis-forgalmat a magánhálózati végpontra helyezi át.
> Győződjön meg arról, hogy a korábbi tűzfalszabályok még nincsenek módosítva.  
>
>

## <a name="azure-virtual-network-injection"></a>Azure Virtual Network injekció 
A VNet az Azure-beli magánhálózat alapvető építőeleme. A VNet lehetővé teszi, hogy számos Azure-erőforrás biztonságosan kommunikáljon egymással, az internettel és a helyszíni hálózatokkal. A VNet olyan hagyományos hálózat, amely a saját adatközpontban fog működni, de az Azure-infrastruktúra, a méretezés, a rendelkezésre állás és az elkülönítés előnyeivel. 

### <a name="advantages"></a>Előnyök
* Ha egy Azure cache for Redis-példány VNet van konfigurálva, nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el.  
* Ha a VNet-t korlátozott NSG-szabályzatokkal kombinálják, az segít csökkenteni a kiszűrése kockázatát. 
* A VNet üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure cache Redis, valamint az alhálózatok, a hozzáférés-vezérlési házirendek és más funkciók számára a hozzáférés további korlátozásához. 
* A Geo-replikáció támogatott. 

### <a name="limitations"></a>Korlátozások
* A VNet Beinjektált gyorsítótárak csak a prémium szintű Azure cache esetében érhetők el a Redis. 
* VNet befecskendezett gyorsítótár használatakor meg kell nyitnia a VNet, hogy gyorsítótárazza a függőségeket, például a CRL/PKI, a AKV, az Azure Storage, a Azure Monitor stb.  


## <a name="azure-firewall-rules"></a>Azure Firewall szabályok
A [Azure Firewall](../firewall/overview.md) felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely az Azure VNet-erőforrásait védi. Ez egy teljesen állapot-nyilvántartó tűzfal, amely beépített, magas rendelkezésre állású és korlátlan Felhőbeli méretezhetőséggel rendelkezik. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.  

### <a name="advantages"></a>Előnyök
* A tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz. Az Azure cache Redis-figyelő rendszerekhez való kapcsolatai mindig engedélyezve vannak, még akkor is, ha a tűzfalszabályok konfigurálva vannak. Az Ön által definiált NSG-szabályok szintén engedélyezettek.  

### <a name="limitations"></a>Korlátozások
* A tűzfalszabályok VNet Beinjektált gyorsítótárral együtt használhatók, de jelenleg nem találhatók privát végpontok. 


## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan konfigurálhat [VNet befecskendezett gyorsítótárat a prémium szintű Azure cache](cache-how-to-premium-vnet.md)-hez a Redis-példányhoz.  
* Megtudhatja, hogyan konfigurálhat [tűzfalszabályokat az összes Azure cache-hez a Redis szintjein](cache-configure.md#firewall). 
* Megtudhatja, hogyan [konfigurálhat privát végpontokat az összes Azure cache-hez a Redis szintjein](cache-private-link.md).