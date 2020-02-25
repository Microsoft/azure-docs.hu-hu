---
title: Alapvető fogalmak az Azure VMware-megoldás CloudSimple általi felügyeletéhez
titleSuffix: Azure VMware Solution by CloudSimple
description: Az Azure VMware-megoldások CloudSimple általi felügyeletének főbb fogalmait ismerteti
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564619"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Alapvető fogalmak az Azure VMware-megoldások CloudSimple általi felügyeletéhez

Az Azure VMware-megoldások CloudSimple általi felügyelete a következő fogalmak ismeretét igényli:

* CloudSimple szolgáltatás, amely Azure VMware-megoldásként jelenik meg a CloudSimple-Service-ben
* CloudSimple csomópont, amely Azure VMware-megoldásként jelenik meg a CloudSimple-Node alapján
* CloudSimple privát felhő
* Szolgáltatás hálózatkezelése
* CloudSimple virtuális gép, amely Azure VMware-megoldásként jelenik meg a CloudSimple-Virtual Machine használatával

## <a name="cloudsimple-service"></a>CloudSimple szolgáltatás

A CloudSimple szolgáltatással a VMware-megoldásokhoz társított összes erőforrást létrehozhatja és kezelheti az Azure Portal CloudSimple. Hozzon létre egy szolgáltatási erőforrást minden olyan régióban, ahol használni szeretné a szolgáltatást.

További információ a [CloudSimple szolgáltatásról](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple csomópont

A CloudSimple csomópont egy dedikált, operációs rendszer nélküli, hiperkonvergens számítási és tárolási gazdagép, amelybe a VMware ESXi hypervisor telepítve van. Ezt a csomópontot ezután beépítjük a VMware vSphereba, a vCenter, a vSAN és a NSX platformba. A CloudSimple hálózatkezelési szolgáltatásai és a peremhálózati hálózati szolgáltatások is engedélyezve vannak. Az egyes csomópontok számítási és tárolókapacitási egységként szolgálnak, amelyet létrehozhat a [CloudSimple privát felhők](cloudsimple-private-cloud.md)létrehozásához. A csomópontokat olyan régióban kell kiépíteni vagy fenntartani, ahol a CloudSimple szolgáltatás elérhető.

További információ a [CloudSimple-csomópontokról](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privát felhő

A CloudSimple privát felhő egy elkülönített VMware stack-környezet, amelyet egy vCenter-kiszolgáló felügyel a saját felügyeleti tartományában. A VMware-verem ESXi-gazdagépeket, vSphere, vCenter, vSAN és NSX tartalmaz. A verem dedikált csomópontokon fut (dedikált és elkülönített operációs rendszer nélküli hardver), és a felhasználók a vCenter és a NSX Managert is tartalmazó natív VMware-eszközökkel használják. A dedikált csomópontok üzembe helyezése az Azure-beli helyszíneken történik, és az Azure kezeli őket. Az egyes privát felhőket a hálózati szolgáltatások, például a VLAN-ok, az alhálózatok és a tűzfalak használatával szegmentálhatja és biztonságossá teheti. A helyszíni környezettel és az Azure-hálózattal létesített kapcsolatok biztonságos, privát VPN-és Azure ExpressRoute-kapcsolatok használatával jönnek létre.

További információ a [CloudSimple privát felhőről](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Szolgáltatás hálózatkezelése

A CloudSimple szolgáltatás olyan hálózatot biztosít régiónként, ahol a CloudSimple szolgáltatás telepítve van. A hálózat egy olyan TCP-rétegbeli 3 címtartomány, amelynek alapértelmezés szerint engedélyezve van az Útválasztás. Az ebben a régióban létrehozott privát felhők és alhálózatok további beállítások nélkül kommunikálnak egymással. A VLAN-ok használatával elosztott vCenter hozhat létre a virtuális helyi hálózatokon. A következő hálózati szolgáltatások használatával konfigurálhatja és biztonságossá teheti a számítási feladatok erőforrásait a saját felhőben:

* [VLAN-ok és alhálózatok](cloudsimple-vlans-subnets.md)
* [Tűzfalak](cloudsimple-firewall-tables.md)
* [VPN-átjárók](cloudsimple-vpn-gateways.md)
* [Nyilvános IP-cím](cloudsimple-public-ip-address.md)
* [Azure-beli hálózati kapcsolatok](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuális gép

A CloudSimple szolgáltatással felügyelheti a VMware virtuális gépeket a Azure Portal. A vSphere-környezetből egy vagy több fürt vagy erőforráskészlet képezhető le ahhoz az előfizetéshez, amelyen a szolgáltatást létrehozták.

További információk:

* [CloudSimple virtuális gépek](cloudsimple-virtual-machines.md)
* [Azure-előfizetés leképezése](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
