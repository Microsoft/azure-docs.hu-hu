---
title: Magánhálózati IP-címek az Azure-ban
titlesuffix: Azure Virtual Network
description: Ismerje meg a magánhálózati IP-címeket az Azure-ban.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223261"
---
# <a name="private-ip-addresses"></a>Privát IP-címek
A magánhálózati IP-címek lehetővé teszik az Azure-beli erőforrások közötti kommunikációt. 

Az erőforrások a következőket tehetik:
* Azure-szolgáltatások, például:
    * Virtuális gépek hálózati adapterei
    * Belső terheléselosztók (ILB-k)
    * Alkalmazásátjárók
* Egy [virtuális hálózatban](virtual-networks-overview.md).
* Helyszíni hálózat VPN-átjárón vagy ExpressRoute-áramkörön keresztül.

A magánhálózati IP-címek lehetővé teszik, hogy nyilvános IP-cím használata nélkül engedélyezzék a kommunikációt ezekhez az erőforrásokhoz.

## <a name="allocation-method"></a>Lefoglalási módszer

Az Azure magánhálózati IP-címeket rendel az erőforrásokhoz az erőforrást tartalmazó virtuális hálózat alhálózatának címtartományból.

Az Azure minden alhálózat-címtartomány első négy címét lefoglalja. A címek nem rendelhetők hozzá erőforrásokhoz. Ha például az alhálózat címtartománya 10.0.0.0/16, a 10.0.0.0-10.0.0.3 és a 10.0.255.255 címek nem érhetők el. Az alhálózati címtartományon belüli IP-címeket egyszerre csak egy erőforráshoz lehet rendelni. 

A magánhálózati IP-címek két módon vannak megadva:

- **Dinamikus**: Az Azure az alhálózat címtartományának következő elérhető, nem hozzárendelt vagy nem lefoglalt IP-címét rendeli hozzá. Például az Azure a 10.0.0.10 címet rendeli hozzá egy új erőforráshoz, ha a 10.0.0.4–10.0.0.9 címek már más erőforrásokhoz lettek rendelve. 

    Az alapértelmezett lefoglalási módszer a dinamikus. A hozzárendelés után a dinamikus IP-címek akkor jelennek meg, ha a hálózati adapter:
    
    * Törölve
    * Az azonos virtuális hálózaton belül egy másik alhálózatra van rendelve.
    * A kiosztási módszer statikusra változik, és egy másik IP-cím van megadva. 
    
    Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet.

- **Statikus**: Ön választhat ki és rendelhet hozzá az alhálózat címtartományából egy nem hozzárendelt vagy nem lefoglalt IP-címet. 

    Az alhálózat címtartomány például 10.0.0.0/16, a 10.0.0.4-10.0.0.9 címek pedig más erőforrásokhoz van rendelve. A 10.0.0.10-10.0.255.254 között bármilyen címeket hozzárendelhet. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. 
    
    Az Azure a statikus IP-címet a kiosztási módszer módosításakor dinamikus IP-ként rendeli hozzá. Az ismételt hozzárendelés akkor is megtörténik, ha a címe nem a következő elérhető az alhálózatban. A címek akkor változnak, ha a hálózati adapter egy másik alhálózathoz van rendelve.
    
    Ha a hálózati adaptert egy másik alhálózathoz szeretné rendelni, a kiosztási módszert a statikusról a dinamikus értékre kell módosítania. Rendelje hozzá a hálózati adaptert egy másik alhálózathoz, majd állítsa vissza a kiosztási módszert statikusra. Rendeljen hozzá egy IP-címet az új alhálózat címtartomány-tartományához.
    
## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

Egy vagy több magánhálózati IP-cím hozzá van rendelve egy vagy több **hálózati adapterhez**. A hálózati adapterek egy Windows vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális géphez vannak rendelve. A lefoglalási módszert minden magánhálózati IP-cím esetében meghatározhatja dinamikusként vagy statikusként is.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Belső DNS-állomásnév feloldás (virtuális gépek esetén)

Az Azure-beli virtuális gépek alapértelmezés szerint az [Azure által felügyelt DNS-kiszolgálókkal](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) vannak konfigurálva. Explicit módon konfigurálhat egyéni DNS-kiszolgálókat. Ezek a DNS-kiszolgálók belső névfeloldást biztosítanak az azonos virtuális hálózatban található virtuális gépekhez.

Az állomásnévnek a virtuális gép magánhálózati IP-címére való leképezése hozzá lesz adva az Azure által felügyelt DNS-kiszolgálókhoz. 

Az állomásnév a fő hálózati adapter elsődleges IP-címére van leképezve, amikor egy virtuális gép:

* Több hálózati adapter
* Több IP-cím
* Mindkettő

Az Azure által felügyelt DNS-sel konfigurált virtuális gépek az ugyanazon a virtuális hálózaton belüli gazdagépeket oldják fel. Egy egyéni DNS-kiszolgáló használatával oldja fel a csatlakoztatott virtuális hálózatokban található virtuális gépek állomásnevét.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók

Egy magánhálózati IP-címet rendelhet az **előtér-** konfigurációhoz:

* [Azure belső terheléselosztó](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Ez a magánhálózati IP-cím belső végpontként szolgál. A belső végpont csak a virtuális hálózaton belüli erőforrásokhoz és a hozzájuk kapcsolódó távoli hálózatokhoz érhető el. Dinamikus vagy statikus IP-címet rendelhet hozzá.

## <a name="at-a-glance"></a>Egy pillantásra
A következő táblázat azt a tulajdonságot mutatja be, amelyen keresztül a magánhálózati IP-cím társítható egy erőforráshoz. 

A lehetséges használható kiosztási módszerek is megjelennek:

* Dinamikus
* Statikus

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati adapter |Igen |Yes |
| Terheléselosztóval |Előtér-konfiguráció |Igen |Yes |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen |Yes |

## <a name="limits"></a>Korlátok
Az IP-címzés korlátai az Azure-ban a [hálózatkezelés korlátainak](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) teljes készletében találhatók. A korlátok régiónként és előfizetésenként értendőek. [Vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és növelje az alapértelmezett korlátokat az üzleti igényeknek megfelelő maximális korlátig.

## <a name="next-steps"></a>Következő lépések
Az [Azure-nyilvános IP-címek](public-ip-addresses.md) megismerése
* [Statikus magánhálózati IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-networks-static-private-ip-arm-pportal.md)