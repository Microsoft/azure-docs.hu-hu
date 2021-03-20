---
title: VMware-megoldás CloudSimple szerint – Azure hálózati kapcsolatok
description: Ismerje meg, hogyan csatlakoztathatja Azure-beli virtuális hálózatát a CloudSimple-régió hálózatához
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025078"
---
# <a name="azure-network-connections-overview"></a>Az Azure hálózati kapcsolatainak áttekintése

Amikor CloudSimple-szolgáltatást hoz létre egy régióban, és csomópontokat hoz létre, a következőket teheti:

* Igényeljen egy Azure ExpressRoute-áramkört, és csatolja az adott régióban található CloudSimple-hálózathoz.
* Az Azure ExpressRoute használatával összekapcsolhatja az CloudSimple-régió hálózatát az Azure-beli virtuális hálózattal vagy a helyszíni hálózattal.
* Hozzáférés biztosítása az Azure-előfizetésben vagy a helyszíni hálózaton futó szolgáltatásokhoz a saját felhőalapú környezetből.

A ExpressRoute-kapcsolat nagy sávszélességű, kis késleltetésű.

## <a name="benefits"></a>Előnyök

Az Azure-beli hálózati kapcsolatok a következőket teszik lehetővé:

* Használja az Azure-t biztonsági mentési célként a saját felhőben lévő virtuális gépekhez.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben a saját felhőalapú vSAN-adattár titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje a nyilvános felhőben fut, miközben az alkalmazás és az adatbázis szintjei a saját felhőben futnak.

## <a name="azure-virtual-network-connection"></a>Azure-beli virtuális hálózati kapcsolatok

A privát felhők a ExpressRoute használatával csatlakoztathatók az Azure-erőforrásokhoz.  A ExpressRoute-kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó erőforrásokhoz a privát felhőből.  Ezzel a kapcsolódással kiterjesztheti saját felhőalapú hálózatát az Azure-beli virtuális hálózatra.  A CloudSimple hálózatról érkező útvonalakat a BGP-n keresztül az Azure-beli virtuális hálózattal cseréli ki a rendszer.  Ha konfigurálta a virtuális hálózatokat, az összes társ virtuális hálózat elérhető lesz a CloudSimple-hálózatról.

![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-kapcsolódás a helyszíni hálózathoz

A meglévő Azure ExpressRoute-áramkört a CloudSimple-régióhoz is összekapcsolhatja. A ExpressRoute Global Reach funkció a két áramkör egymással való összekapcsolására szolgál.  Létrejön egy kapcsolat a helyszíni és a CloudSimple ExpressRoute-áramkörök között.  Ez a kapcsolódás lehetővé teszi a helyszíni hálózatok kibővítését a saját felhőalapú hálózatra. A CloudSimple hálózatról érkező útvonalakat a rendszer a BGP és a helyszíni hálózat között cseréli ki.

![Helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Kapcsolódás helyszíni hálózathoz és Azure-beli virtuális hálózathoz

A helyszíni hálózattal és az Azure-beli virtuális hálózattal létesített kapcsolatok a CloudSimple-hálózatról is létezhetnek.  A kapcsolat BGP-t használ a helyszíni hálózat, az Azure Virtual Network és a CloudSimple hálózat közötti útvonalak cseréjéhez.  Ha Global Reach kapcsolat jelenlétében csatlakoztatja a CloudSimple-hálózatot az Azure-beli virtuális hálózathoz, az Azure-beli virtuális hálózati útvonalak láthatók lesznek a helyszíni hálózaton.  Az útvonalak cseréje az Azure-ban az Edge-útválasztók között történik.

![Helyszíni ExpressRoute-és Azure-beli virtuális hálózati kapcsolatok](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Fontos szempontok

A helyszíni hálózatról és az Azure Virtual Network hálózatról érkező CloudSimple-hálózathoz való csatlakozás lehetővé teszi az összes hálózat közötti váltást.

* Az Azure Virtual Network mind a helyszíni, mind a CloudSimple hálózatról látható lesz.
* Ha a helyszíni hálózatról kapcsolódott az Azure-beli virtuális hálózathoz, a CloudSimple-hálózathoz való csatlakozás Global Reach lehetővé teszi a virtuális hálózatokhoz való hozzáférést a CloudSimple hálózatról.
* Az alhálózati címek **nem** lehetnek átfedésben a csatlakoztatott hálózatok között.
* A CloudSimple **nem** hirdeti meg az alapértelmezett útvonalat a ExpressRoute-kapcsolatokhoz
* Ha a helyszíni útválasztó meghirdeti az alapértelmezett útvonalat, a CloudSimple hálózatról és az Azure Virtual networkről érkező forgalom a meghirdetett alapértelmezett útvonalat fogja használni.  Ennek eredményeképpen az Azure-beli virtuális gépek nem érhetők el nyilvános IP-címek használatával.

## <a name="next-steps"></a>Következő lépések

* [Azure-beli virtuális hálózat összekötése a CloudSimple a ExpressRoute használatával](virtual-network-connection.md)
* [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](on-premises-connection.md)
