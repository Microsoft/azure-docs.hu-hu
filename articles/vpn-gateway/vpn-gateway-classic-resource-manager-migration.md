---
title: Klasszikus – Resource Manager-áttelepítés VPN Gateway | Microsoft Docs
description: Ez az oldal áttekintést nyújt a klasszikus és a Resource Manager áttelepítésének VPN Gatewayéről.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488204"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Klasszikusról VPN Gateway a Resource Manager-áttelepítésre
A VPN-átjárók mostantól áttelepíthetők a Klasszikusból a Resource Manager-alapú üzemi modellbe. További információ: Azure Resource Manager [funkciók és előnyök](../azure-resource-manager/management/overview.md). Ebben a cikkben részletesen ismertetjük, hogyan lehet migrálni a klasszikus üzemelő példányokról az újabb Resource Manager-alapú modellre. 

A VPN-átjárók áttelepítése a Klasszikusból a Resource Managerbe VNet áttelepítés részeként történik. Az áttelepítés egyszerre egy VNet történik. A Migrálás eszközeinek és előfeltételeinek semmilyen további követelménye nincs. Az áttelepítési lépések megegyeznek a meglévő VNet-áttelepítéssel, és dokumentálva vannak a [IaaS-erőforrások áttelepítése lapon](../virtual-machines/migration-classic-resource-manager-ps.md). Az áttelepítés során nincs adatelérési út, így a meglévő munkaterhelések továbbra is működőképesek maradnak a helyi kapcsolat elvesztése nélkül az áttelepítés során. A VPN-átjáróhoz társított nyilvános IP-cím nem változik az áttelepítési folyamat során. Ez azt jelenti, hogy az áttelepítés befejeződése után a helyszíni útválasztót nem kell újrakonfigurálnia.  

A Resource Manager modellje eltér a klasszikus modelltől, és virtuális hálózati átjárók, helyi hálózati átjárók és a kapcsolatok erőforrásaiból áll. Ezek a VPN-átjárót képviselik, a helyszíni címterület és a két közötti kapcsolat közötti kapcsolatot képviselő helyi helyet. Az áttelepítés befejezése után az átjárók nem lesznek elérhetők a klasszikus modellben, és a virtuális hálózati átjárók, a helyi hálózati átjárók és a kapcsolatok objektumainak minden felügyeleti műveletét Resource Manager-modell használatával kell végrehajtani.

## <a name="supported-scenarios"></a>Támogatott esetek
A VPN-kapcsolat leggyakoribb forgatókönyveit a klasszikus és a Resource Manager közötti áttelepítés szabályozza. A támogatott forgatókönyvek a következők:

* Pont – hely kapcsolat
* Helyek közötti kapcsolat a helyszíni helyhez csatlakoztatott VPN Gatewayokkal
* VNet két virtuális hálózatok közötti VNet VPN-átjárók használatával
* Több virtuális hálózatok kapcsolódik ugyanahhoz a helyszíni helyhez
* Többhelyes kapcsolat
* Kényszerített bújtatás engedélyezve virtuális hálózatok

A nem támogatott forgatókönyvek a következők:  

* A ExpressRoute-átjáróval és a VPN Gatewaytel rendelkező VNet jelenleg nem támogatott.
* Átviteli forgatókönyvek, amelyekben a virtuálisgép-bővítmények a helyszíni kiszolgálókhoz csatlakoznak. Az átviteli VPN-kapcsolatokra vonatkozó korlátozások alább láthatók.

> [!NOTE]
> A CIDR érvényesítése a Resource Manager-modellben szigorúbb, mint a klasszikus modellben. A Migrálás előtt gondoskodjon arról, hogy a klasszikus címtartományok érvényes CIDR-formátumra feleljenek meg az áttelepítés megkezdése előtt. A CIDR bármely gyakori CIDR-érvényesítő használatával ellenőrizhető. Az áttelepítéskor érvénytelen CIDR tartományokkal rendelkező VNet vagy helyi helyek sikertelen állapotot eredményeznek.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet a VNet-kapcsolat áttelepítéséhez
A klasszikus VNet kapcsolat VNet a csatlakoztatott VNet helyi képviseletének létrehozásával érhető el. Az ügyfeleknek két olyan helyi helyet kell létrehozniuk, amelyik a csatlakozáshoz szükséges két virtuális hálózatok képviseli. Ezeket a rendszer az IPsec-alagút használatával csatlakoztatta a megfelelő virtuális hálózatok a két virtuális hálózatok közötti kapcsolat létrehozásához. Ez a modell kezelhetőségi kihívásokkal rendelkezik, mivel az egyik VNet lévő címtartomány-változásokat a helyi hely megfelelő képviseletében is meg kell őrizni. A Resource Manager-modellben ez a megkerülő megoldás már nem szükséges. A két virtuális hálózatok közötti kapcsolat közvetlenül is megvalósítható a kapcsolati erőforrás "Vnet2Vnet" kapcsolati típusának használatával. 

![Képernyőkép a VNet és a VNet áttelepítéséről.](./media/vpn-gateway-migration/migration1.png)

A VNet áttelepítése során a rendszer azt érzékelte, hogy a csatlakoztatott entitás a jelenlegi VNet VPN-átjárója egy másik VNet, és gondoskodjon arról, hogy a mindkét virtuális hálózatok áttelepítésének befejezése után ne tekintse meg a többi VNet képviselő két helyi helyet. A két VPN-átjáró klasszikus modelljét, két helyi helyet és két kapcsolatot is át kell alakítani a Resource Manager-modellbe két VPN-átjáróval és két, Vnet2Vnet típusú kapcsolattal.

## <a name="transit-vpn-connectivity"></a>Tranzit VPN-kapcsolat
A VPN-átjárókat úgy is konfigurálhatja egy topológiában, hogy egy VNet helyszíni kapcsolata egy másik, a helyszíni környezethez közvetlenül csatlakozó VNet való csatlakozással érhető el. Ez a tranzit VPN-kapcsolat, ahol az első VNet lévő példányok a helyszíni erőforrásokhoz csatlakoznak, a csatlakoztatott VNet található VPN-átjáró felé, amely közvetlenül a helyszíni hálózathoz csatlakozik. Ahhoz, hogy ezt a konfigurációt a klasszikus üzemi modellben lehessen elérni, létre kell hoznia egy helyi helyet, amely összesített előtagokkal rendelkezik a csatlakoztatott VNet és a helyszíni címterület esetében is. Ez a reprezentációs helyi hely ezután csatlakozik a VNet az átviteli kapcsolat eléréséhez. Ez a klasszikus modell hasonló kezelhetőségi kihívásokkal is rendelkezik, mivel a helyszíni címtartomány bármilyen változását a VNet és a helyszíni összesítést képviselő helyi helyen is fenn kell tartani. A BGP-támogatás bevezetése a Resource Manager által támogatott átjárókban egyszerűbbé teszi a kezelhetőséget, mivel a csatlakoztatott átjárók az előtagok manuális módosítása nélkül is megismerhetik az útvonalakat a helyszínen.

![Képernyőkép az átviteli útválasztási forgatókönyvről.](./media/vpn-gateway-migration/migration2.png)

Mivel a VNet-t a helyi telephelyek megkövetelése nélkül VNet a kapcsolatra, az átviteli forgatókönyv elveszti a helyszíni kapcsolódást a VNet, amely közvetetten kapcsolódik a helyszíni hálózathoz. A kapcsolat elvesztése a következő két módon enyhíthető: az áttelepítés befejezése után – 

* Engedélyezze a BGP-t a közösen és a helyszíni kapcsolattal összekapcsolt VPN-átjárók esetében. A BGP engedélyezése a kapcsolat más konfigurációs változások nélkül visszaállítja a kapcsolatot, mivel az útvonalak megtanultak, és a VNet-átjárók között hirdetik őket. Vegye figyelembe, hogy a BGP-beállítás csak a standard és a magasabb SKU-ban érhető el.
* Hozzon létre explicit kapcsolatot az érintett VNet a helyszíni helyet képviselő helyi hálózati átjáróval. Ehhez az IPsec-alagút létrehozásához és konfigurálásához a helyszíni útválasztón is módosítani kell a konfigurációt.

## <a name="next-steps"></a>Következő lépések
A VPN Gateway áttelepítési támogatásának megismerése után lépjen a [platform által támogatott IaaS-erőforrások áttelepítésére a Klasszikusból a Resource Managerbe](../virtual-machines/migration-classic-resource-manager-ps.md) a kezdéshez.