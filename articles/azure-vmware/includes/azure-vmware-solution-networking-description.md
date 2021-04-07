---
title: Azure VMware megoldás Hálózatkezelés és kapcsolat
description: Azure VMware megoldás Hálózatkezelés és kapcsolat leírása.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462582"
---
<!-- Used in introduction.md and concepts-networking.md -->

Az Azure VMware megoldás a helyszíni és az Azure-alapú erőforrásokból elérhető privát felhőalapú környezetet kínál. Az Azure ExpressRoute, a VPN-kapcsolatok vagy az Azure Virtual WAN szolgáltatásai biztosítják a kapcsolatot. Ezeknek a szolgáltatásoknak konkrét hálózati címtartományok és tűzfal-portok szükségesek a szolgáltatások engedélyezéséhez.

Privát felhő üzembe helyezése esetén a rendszer létrehoz egy privát hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezeket a magánhálózatok használatával érheti el a vCenter és a NSX-T kezelőt, illetve a virtuális gépek vMotion vagy üzembe helyezését.  

A ExpressRoute Global Reach a privát felhők helyszíni környezetekhez való összekapcsolására szolgál. A kapcsolathoz szükség van egy ExpressRoute áramkört tartalmazó virtuális hálózatra az előfizetésben.

A privát felhőbe telepített virtuális gépek (VM-EK) elérhetők az interneten keresztül az Azure Virtual WAN nyilvános IP-funkcióival.  Alapértelmezés szerint az Internet-hozzáférés le van tiltva az új privát felhőknél. További információkért lásd: [a nyilvános IP-funkciók használata az Azure VMware megoldásban](../public-ip-usage.md).

