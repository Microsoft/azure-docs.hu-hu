---
title: Azure nyilvános IP-cím előtagja
description: Ismerje meg, hogy mi az Azure nyilvános IP-cím előtagja, és hogyan segíthet kiszámítható nyilvános IP-címek hozzárendeléséhez az erőforrásokhoz.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 8bfc76afc1ef799be0fb29654bd341f53d1b0a8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048459"
---
# <a name="public-ip-address-prefix"></a>Nyilvános IP-cím előtagja

A nyilvános IP-cím előtag az IP-címek fenntartott tartománya az Azure-ban. Az Azure a megadott számon alapuló címtartományt biztosít az előfizetéshez. 

Ha még nem ismeri a nyilvános címeket, tekintse meg a [nyilvános IP-címek](./public-ip-addresses.md#public-ip-addresses) című témakört.

A nyilvános IP-címek az egyes Azure-régiók címeinek egy készletéhez vannak rendelve. Az egyes régiókban az Azure által használt tartományok listáját is [letöltheti](https://www.microsoft.com/download/details.aspx?id=56519) . Például a 40.121.0.0/16 az egyik az USA keleti régiójában az Azure által használt 100-tartomány. A tartomány tartalmazza a 40.121.0.1-40.121.255.254 használható címét.

Egy nyilvános IP-cím előtagot hoz létre egy Azure-régióban és-előfizetésben egy név megadásával, valamint azt, hogy az előtag hány címet tartalmazzon. 

A nyilvános IP-címtartományok a választott előtaggal vannak társítva. Ha/28 előtagot hoz létre, az Azure 16 IP-címet ad meg az egyik tartományból.

Nem tudja, hogy az Azure melyik tartományhoz lesz hozzárendelve, amíg létre nem hozza a tartományt, de a címek folytonos. 

A nyilvános IP-címek előtagjainak díja, további információ: [nyilvános IP-cím díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Miért érdemes létrehozni egy nyilvános IP-cím előtagot?

Nyilvános IP-cím erőforrások létrehozásakor az Azure egy elérhető nyilvános IP-címet rendel az adott régióban használt bármely tartományból. 

Amíg az Azure nem rendeli hozzá az IP-címet, nem fogja tudni a pontos IP-címet. Ez a folyamat problémát okozhat, ha adott IP-címeket engedélyező tűzfalszabályok hozhatók létre. Minden hozzáadott IP-cím esetében hozzá kell adni egy megfelelő tűzfalszabály-szabályt.

Ha a nyilvános IP-cím előtagból oszt ki címeket az erőforrásokhoz, a tűzfalszabályok frissítései nem szükségesek. A rendszer hozzáadja a teljes tartományt a szabályhoz.

## <a name="benefits"></a>Előnyök

- Nyilvános IP-címek erőforrásainak létrehozása ismert tartományból.
- Tűzfalszabály-konfiguráció olyan tartományokkal, amelyek tartalmazzák a jelenleg hozzárendelt nyilvános IP-címeket, valamint azokat a címeket, amelyekhez még nincs hozzárendelve. Ezzel a konfigurációval nem szükséges módosítani a tűzfalszabályok új erőforrások IP-címeinek hozzárendelésével.
- A létrehozható tartomány alapértelmezett mérete/28 vagy 16 IP-cím.
- A létrehozott tartományok száma nem korlátozott. Az Azure-előfizetésekben elérhető statikus nyilvános IP-címek maximális száma korlátozott. A létrehozott tartományok száma nem terjedhet ki több statikus nyilvános IP-címre, mint amennyit az előfizetésében lehet. További információ: Azure- [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Az előtagból származó címek használatával létrehozott címek bármely olyan Azure-erőforráshoz hozzárendelhetők, amelyhez nyilvános IP-címet rendelhet hozzá.
- Egyszerűen megtekintheti, hogy mely IP-címek legyenek megadva, és hogy a tartományon belül ne legyenek megadva.

## <a name="scenarios"></a>Forgatókönyvek
A következő erőforrásokat a statikus nyilvános IP-címekhez rendelheti hozzá egy előtagból:

|Erőforrás|Eset|Lépések|
|---|---|---|
|Virtual machines (Virtuális gépek)| A nyilvános IP-címek az Azure-beli virtuális gépekhez való társítása csökkenti a felügyeleti terhelést, amikor IP-címeket ad hozzá a tűzfal engedélyezési listájához. Egyetlen tűzfalszabály használatával teljes előtagot adhat hozzá. Az Azure-beli virtuális gépekkel való skálázás során az IP-címeket ugyanahhoz az előtagokhoz társíthatja, így a költségek, az idő és a felügyelet terhelése is megtakarítható.| IP-címek hozzárendelése egy előtagból a virtuális géphez: </br> 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) </br> 2. [hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) </br> 3. [rendelje hozzá az IP-címet a virtuális gép hálózati adapteréhez.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [Az IP-címeket egy virtuálisgép-méretezési csoporthoz is hozzárendelheti](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standard Load Balancer | A nyilvános IP-címek egy előtagból az előtérbeli IP-konfigurációba való társítása vagy a terheléselosztó kimenő szabálya biztosítja az Azure nyilvános IP-címtartomány egyszerűsítését. Egyszerűsítse a forgatókönyvet, ha a kimenő kapcsolatokat számos összefüggő IP-címről szeretné kiszolgálni. | IP-címek hozzárendelése egy előtagból a terheléselosztó számára: </br> 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) </br> 2. [hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) </br> 3. a terheléselosztó létrehozásakor válassza ki vagy frissítse a fenti 2. lépésben létrehozott IP-címet a terheléselosztó előtérbeli IP-címével. |
| Azure Firewall | A kimenő SNAT előtagjaként nyilvános IP-címet is használhat. Minden kimenő virtuális hálózati forgalom a [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános IP-címére van lefordítva. | IP-cím hozzárendelése egy előtagból a tűzfalhoz: </br> 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) </br> 2. [hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) </br> 3. [Az Azure tűzfal telepítésekor](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)ügyeljen arra, hogy az előtagja által korábban megadott IP-címet válassza ki.|
| VPN Gateway (AZ SKU) vagy Application Gateway v2 | Nyilvános IP-címet is használhat a zóna – redundáns VPN vagy Application Gateway v2 előtaggal. | IP-cím hozzárendelése egy előtagból az átjáróhoz: </br> 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) </br> 2. [hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) </br> 3. a [VPN Gateway](../vpn-gateway/tutorial-create-gateway-portal.md) vagy [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)központi telepítésekor ügyeljen arra, hogy az előtagja által korábban megadott IP-címet válassza ki.|

## <a name="constraints"></a>Korlátozások

- Nem adhatja meg az előtaghoz tartozó IP-címeket. Az Azure a megadott méret alapján adja meg az előtag IP-címeit.
- Alapértelmezés szerint legfeljebb 16 IP-cím vagy/28 előtagot hozhat létre. További információkért tekintse át a [hálózati korlátokat a kérések](../azure-portal/supportability/networking-quota-requests.md) és az [Azure-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) növelésével.
- A tartomány nem módosítható, miután létrehozta az előtagot.
- Csak a standard SKU-val létrehozott statikus nyilvános IP-címek rendelhetők hozzá az előtag tartományához. Ha többet szeretne megtudni a nyilvános IP-címekről, tekintse meg a [nyilvános IP-cím](./public-ip-addresses.md#public-ip-addresses)című témakört.
- A tartomány címei csak Azure Resource Manager erőforrásokhoz rendelhetők hozzá. A klasszikus üzemi modellben nem lehet címeket hozzárendelni az erőforrásokhoz.
- Az előtagból létrehozott összes nyilvános IP-címnek ugyanabban az Azure-régióban és előfizetésben kell lennie, mint az előtagja. A címeket ugyanahhoz a régióhoz és előfizetéshez tartozó erőforrásokhoz kell rendelni.
- Egy előtag nem törölhető, ha az ahhoz tartozó címek hozzá vannak rendelve egy erőforráshoz társított nyilvános IP-címek erőforrásaihoz. Szüntesse meg az összes olyan nyilvános IP-cím-erőforrást, amely először az előtaghoz rendelt IP-címet.


## <a name="next-steps"></a>Következő lépések

- Nyilvános IP-cím előtagjának [létrehozása](manage-public-ip-address-prefix.md)