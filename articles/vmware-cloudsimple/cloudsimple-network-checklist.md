---
title: Azure VMware-megoldás a CloudSimple-Network ellenőrzőlista
description: Ellenőrzőlista az Azure VMware-megoldás hálózati CIDR CloudSimple általi lefoglalásához
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025010"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Az Azure VMware-megoldás hálózatkezelési előfeltételei a CloudSimple

A CloudSimple által kínált Azure VMware-megoldás olyan VMware Private Cloud Environment-környezetet kínál, amely elérhető a helyi környezetek, a nagyvállalatok által felügyelt eszközök és az Azure-erőforrások felhasználói és alkalmazásai számára. A kapcsolat olyan hálózati szolgáltatásokon keresztül történik, mint a VPN-EK és az Azure ExpressRoute-kapcsolatok. Néhány hálózati szolgáltatáshoz meg kell adnia a szolgáltatások engedélyezéséhez szükséges hálózati címtartományt. 

A cikkben szereplő táblázatok a címtartományok és a megadott címeket használó kapcsolódó szolgáltatások leírását ismertetik. Néhány cím megadása kötelező, és néhány a telepíteni kívánt szolgáltatástól függ. Ezek a Címterület nem fedik át egymást a helyszíni alhálózatok, az Azure Virtual Network alhálózatok vagy a tervezett CloudSimple számítási feladatok alhálózatai között.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>A privát felhő létrehozásához szükséges hálózati címtartományok

A CloudSimple szolgáltatás és a privát felhő létrehozása során a következő módon be kell tartania a megadott hálózati osztály nélküli tartományok közötti útválasztási (CIDR) tartományokat.

| Név/használat a következőhöz:     | Description                                                                                                                            | Címtartomány            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Átjáró CIDR      | Az Edge-szolgáltatásokhoz (VPN-átjárók) szükséges.  Ez a CIDR a CloudSimple szolgáltatás létrehozásakor szükséges, és az RFC 1918 területről kell származnia. | /28                      |
| vSphere/vSAN CIDR | A VMware felügyeleti hálózatokhoz szükséges. Ezt a CIDR meg kell adni a saját felhő létrehozásakor.                                    | /24 vagy/23 vagy/22 vagy/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Egy helyszíni hálózathoz való Azure-beli hálózati kapcsolódáshoz szükséges hálózati címtartomány

[A helyszíni hálózatról a ExpressRoute](on-premises-connection.md) -re való csatlakozás egy Global REACH kapcsolatot létesít.  A kapcsolat Border Gateway Protocol (BGP) protokollt használ a helyszíni hálózat, a saját felhőalapú hálózata és az Azure-hálózatok közötti útvonalak cseréjéhez.

| Név/használat a következőhöz:             | Description                                                                                                                                                                             | Címtartomány |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | A helyszíni kapcsolat ExpressRoute-Global Reach használata esetén szükséges. Ezt a CIDR akkor kell megadni, ha egy Global Reach kapcsolódási kérelem egy támogatási jegyen keresztül történik. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>A helyek közötti VPN-kapcsolat helyszíni hálózathoz való használatának szükséges hálózati címtartomány

A helyszíni [hálózatról a saját felhőalapú hálózatra helyek közötti VPN](vpn-gateway.md) -kapcsolaton keresztül történő csatlakozáshoz a következő IP-címekre, helyszíni hálózatra és azonosítóra van szükség. 

| Címek/címtartomány | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Társ IP-címe               | Helyszíni VPN-átjáró nyilvános IP-címe. Helyek közötti VPN-kapcsolat létesítéséhez szükséges a helyszíni adatközpont és a CloudSimple szolgáltatási régiója között. Ez az IP-cím szükséges a helyek közötti VPN-átjáró létrehozásakor.                                         |
| Társ-azonosító       | A helyszíni VPN-átjáró társ-azonosítója. Ez általában ugyanaz, mint a **társ IP-címe**.  Ha a helyszíni VPN-átjárón egyedi azonosító van megadva, az azonosítót meg kell adni.  A társ-azonosító megadása kötelező a helyek közötti VPN-átjáró létrehozásakor.   |
| Helyszíni hálózatok   | A régióban elérhető CloudSimple-hálózatokat igénylő helyszíni előtagok.  Minden olyan előtagok belefoglalása egy helyszíni hálózatból, amely hozzáfér a CloudSimple hálózathoz, beleértve az ügyfél hálózatát, ahonnan a felhasználók hozzáférnek a hálózathoz.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>A pont – hely VPN-kapcsolatok használatához szükséges hálózati címtartomány

A pont – hely típusú VPN-kapcsolat lehetővé teszi a CloudSimple-hálózat elérését egy ügyfélgépről.  [Pont – hely típusú VPN beállításához](vpn-gateway.md)a következő hálózati címtartományt kell megadnia.

| Címek/címtartomány | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél-alhálózat         | A DHCP-címeket az ügyfél alhálózata teszi elérhetővé, ha pont – hely típusú VPN-kapcsolaton keresztül csatlakozik. Erre az alhálózatra akkor van szükség, amikor pont – hely típusú VPN-átjárót hoz létre egy CloudSimple-portálon.  A hálózat két alhálózatra van osztva; egyet az UDP-kapcsolathoz, a másikat a TCP-kapcsolatokhoz. |

## <a name="next-steps"></a>Következő lépések

* [Helyszíni tűzfal beállítása a saját felhőhöz való hozzáféréshez](on-premises-firewall-configuration.md)
* [Rövid útmutató – CloudSimple-szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Rövid útmutató – privát felhő konfigurálása](quickstart-create-private-cloud.md)
* További információ az [Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)
* További információ a [VPN-átjárókkal](cloudsimple-vpn-gateways.md) kapcsolatban
