---
title: VLAN-ok és alhálózatok az Azure VMware megoldásban a CloudSimple
description: Ismerje meg a CloudSimple-beli VLAN-okat és alhálózatokat, valamint azt a hálózatot, amelyet a CloudSimple biztosít a CloudSimple szolgáltatás üzembe helyezéséhez.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140666"
---
# <a name="vlans-and-subnets-overview"></a>VLAN-ok és alhálózatok áttekintése

A CloudSimple olyan hálózatot biztosít régiónként, ahol a CloudSimple szolgáltatás telepítve van.  A hálózat egy olyan TCP-rétegbeli 3 címtartomány, amelynek alapértelmezés szerint engedélyezve van az Útválasztás.  Az ebben a régióban létrehozott privát felhők és alhálózatok további beállítások nélkül kommunikálhatnak egymással.  A VLAN-ok használatával elosztott portokat hozhat létre a vCenter.

![CloudSimple hálózati topológia](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Minden egyes privát felhőhöz létrejön egy VLAN (2. rétegbeli hálózat).  A 2. rétegbeli forgalom egy privát felhő határain belül marad, ami lehetővé teszi a helyi forgalom elkülönítését a privát felhőben.  A privát felhőben létrehozott VLAN-hoz csak az adott privát felhőben hozhatók létre elosztott portok.  A privát felhőben létrehozott VLAN-t a rendszer automatikusan konfigurálja az összes olyan kapcsolón, amely a privát felhő gazdagépéhez csatlakozik.

## <a name="subnets"></a>Alhálózatok

Alhálózatot úgy hozhat létre, amikor létrehoz egy VLAN-t az alhálózat címterület definiálásával. A Címterület IP-címe alhálózati átjáróként van hozzárendelve. Az ügyfél és a régió számára egy privát 3. rétegbeli címtartomány van hozzárendelve. A hálózati régióban bármely, a helyszíni vagy az Azure-beli virtuális hálózattal nem átfedésben lévő RFC 1918-es címtartomány konfigurálható.

Alapértelmezés szerint minden alhálózat képes kommunikálni egymással, így csökkentve a privát felhők közötti útválasztás konfigurációját. Az ugyanabban a régióban található számítógépeken található kelet-nyugati adat ugyanabban a 3. rétegbeli hálózaton marad, és a helyi hálózati infrastruktúrán keresztül továbbítja a régión belüli forgalmat. Egy régióban a privát felhők közötti kommunikációhoz nincs szükség kimenő forgalomra. Ez a megközelítés kiküszöböli a különböző számítási feladatok különböző privát felhőkben való üzembe helyezése során felmerülő WAN/kimenő teljesítménybeli szankciókat.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN alhálózatok CIDR tartománya

A privát felhőt egy vCenter-kiszolgáló által kezelt elkülönített VMware stack (ESXi-gazdagépek, vCenter, vSAN és NSX) környezete hozza létre.  A felügyeleti összetevők a vSphere/vSAN alhálózatok CIDR kiválasztott hálózatban vannak telepítve.  A hálózati CIDR tartománya különböző alhálózatokra van osztva az üzembe helyezés során.

* Minimális vSphere/vSAN alhálózatok CIDR tartományának előtagja: **/24**
* VSphere/vSAN alhálózatok maximális CIDR-tartományának előtagja: **/21**

> [!CAUTION]
> A vSphere/vSAN CIDR-tartomány IP-címei a saját felhőalapú infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban bármely virtuális gépen.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN alhálózatok CIDR tartományának korlátai

A vSphere/vSAN alhálózatok CIDR tartomány méretének kiválasztásával hatással van a saját felhő méretére.  A következő táblázat az vSphere-/vSAN-alhálózatok CIDR méretétől függően a csomópontok maximális számát jeleníti meg.

| Megadott vSphere/vSAN alhálózatok CIDR előtag hossza | Csomópontok maximális száma |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Privát felhőben létrehozott felügyeleti alhálózatok

A következő felügyeleti alhálózatok jönnek létre a privát felhő létrehozásakor.

* **Rendszerfelügyeleti webszolgáltatások**. Az ESXi-gazdagépek felügyeleti hálózata, a DNS-kiszolgáló, a vCenter-kiszolgáló VLAN-és alhálózata.
* **VMotion**. Az ESXi gazdagépek vMotion-hálózatának VLAN-és alhálózata.
* **VSAN**. Az ESXi gazdagépek vSAN-hálózatának VLAN-és alhálózata.
* **NsxtEdgeUplink1**. VLAN és alhálózat a VLAN-hoz a külső hálózathoz való kapcsolódáshoz.
* **NsxtEdgeUplink2**. VLAN és alhálózat a VLAN-hoz a külső hálózathoz való kapcsolódáshoz.
* **NsxtEdgeTransport**. A VLAN és az alhálózat a szállítási zónák számára a 2. rétegbeli hálózatok elérését szabályozza a NSX-T-ben.
* **NsxtHostTransport**. VLAN és alhálózat a gazdagép átviteli zónájához.

### <a name="management-network-cidr-range-breakdown"></a>Felügyeleti hálózat CIDR-tartományának bontása

a vSphere/vSAN alhálózatok CIDR megadott tartománya több alhálózatra van osztva.  Az alábbi táblázat egy példát mutat be az engedélyezett előtagok bontására.  A példa a 192.168.0.0 használja CIDR-tartományként.

Példa:

| Megadott vSphere/vSAN alhálózatok CIDR/előtag | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Rendszerkezelés | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T gazdagép átvitele | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX – T Edge-átvitel | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX – T Edge-Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX – T Edge-uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Következő lépések

* [Virtuális helyi hálózatok és alhálózatok létrehozása és kezelése](create-vlan-subnet.md)
