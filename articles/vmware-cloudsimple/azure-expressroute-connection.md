---
title: Azure VMware-megoldás CloudSimple – privát felhő összekötése az Azure-hálózathoz a ExpressRoute használatával
description: Ismerteti, hogyan csatlakoztatható a CloudSimple saját felhőalapú környezete az Azure-beli virtuális hálózathoz a ExpressRoute használatával
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895988"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>A CloudSimple saját felhőalapú környezetének összekötése az Azure-beli virtuális hálózattal a ExpressRoute használatával

Az CloudSimple saját felhője az Azure ExpressRoute használatával csatlakoztatható az Azure-beli virtuális hálózathoz.  Ez a nagy sávszélességű, kis késleltetésű kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó szolgáltatásokhoz a saját felhőalapú környezetében.

A virtuális hálózati kapcsolatok a következőket teszik lehetővé:

* Használja az Azure-t biztonsági mentési célként a saját felhőben lévő virtuális gépekhez.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben a saját felhőalapú vSAN-adattár titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje a nyilvános felhőben fut, miközben az alkalmazás és az adatbázis szintjei a saját felhőben futnak.

![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Virtuális hálózati kapcsolatok beállítása

A magánhálózati felhőhöz való virtuális hálózati kapcsolat beállításához az engedélyezési kulcsra, a társ áramköri URI-ra és az Azure-előfizetéshez való hozzáférésre van szükség. Ezek az információk a CloudSimple-portál Virtual Network kapcsolatok lapján érhetők el. Útmutatásért lásd: az [Azure-beli virtuális hálózatra vonatkozó információk beszerzése az CloudSimple való kapcsolódáshoz](virtual-network-connection.md). Ha bármilyen problémája van az információ beszerzésével, küldjön egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérést</a>.

> [!TIP]
> Ha már rendelkezik Azure-beli virtuális hálózattal, átjáró-alhálózattal és virtuális hálózati átjáróval, ugorjon a 4. lépésre.

1. Hozzon létre egy virtuális hálózatot az Azure-előfizetésében, és ellenőrizze, hogy a kiválasztott címterület különbözik-e a privát felhő címterület-területtől.  Ha már rendelkezik Azure-beli virtuális hálózattal, használhatja a meglévőt.  Részletekért lásd: [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md).
2. Hozza létre az átjáró-alhálózatot az Azure-beli virtuális hálózaton.  Ha már rendelkezik átjáró-alhálózattal az Azure-beli virtuális hálózatban, használhatja a meglévőt. Részletekért lásd: [átjáró-alhálózat létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Hozza létre a virtuális hálózati átjárót a virtuális hálózaton.  Ha meglévő virtuális hálózati átjáróval rendelkezik, használhatja a meglévőt. Részletekért lásd: [a virtuális hálózati átjáró létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Hozza létre a kapcsolatot a virtuális hálózat és a saját felhő között úgy, hogy beváltja az engedélyezési kulcsot a [virtuális hálózat csatlakoztatása egy áramkörhöz – eltérő előfizetéshez](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)című témakörben leírtak szerint.

> [!WARNING]
> Ha meglévő virtuális hálózati átjárót használ, és egy ExpressRoute-kapcsolattal rendelkezik ugyanahhoz a helyhez, mint a CloudSimple ExpressRoute-áramkör, a kapcsolat nem lesz létrehozva.  Hozzon létre egy új virtuális hálózatot, és kövesse az előző lépéseket.

## <a name="test-the-virtual-network-connection"></a>A virtuális hálózati kapcsolatok tesztelése

A létrehozást követően a **Tulajdonságok** elemre kattintva megtekintheti a kapcsolatok **állapotát.**  Az állapot és a kiépítési állapot megjelenítésének **sikeresnek** kell lennie.

![A kapcsolatok állapota](media/azure-expressroute-connection.png)

A virtuális hálózati kapcsolatok tesztelése:

1. Hozzon létre egy virtuális gépet az Azure-előfizetésében.
2. Keresse meg a saját felhőalapú vCenter IP-címét (tekintse meg az üdvözlő e-mailt).
3. Pingelje Felhőbeli vCenter az Azure Virtual Networkben létrehozott virtuális gépről.
4. Pingelje az Azure-beli virtuális gépet a saját felhőalapú vCenter futó virtuális gépekről.

Ha problémák merülnek fel a kapcsolat létrehozásakor, küldjön egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérést</a>.
