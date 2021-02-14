---
title: 'Azure VPN Gateway: átjáró törlése: portál'
description: Virtuális hálózati átjáró törlése a Azure Portal használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/10/2021
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 413fd8c7f03ef44abe4bece39ca717c533dea66b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376384"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Virtuális hálózati átjáró törlése a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ez a cikk segítséget nyújt a virtuális hálózati átjárók törlésében. A VPN Gateway-konfigurációhoz tartozó átjárók törlésére több különböző módszer is rendelkezésre áll.

* Ha mindent törölni szeretne, és a tesztelési környezethez hasonlóan szeretné elindulni, akkor törölheti az erőforráscsoportot. Ha töröl egy erőforráscsoportot, az törli a csoporton belüli összes erőforrást. Ez a módszer csak akkor ajánlott, ha nem szeretné megőrizni az erőforráscsoport egyik erőforrását sem. Ezzel a módszerrel nem törölhet szelektíven néhány erőforrást.

* Ha szeretné megőrizni az erőforráscsoport egyes erőforrásait, a virtuális hálózati átjáró törlése valamivel bonyolultabb lesz. A virtuális hálózati átjáró törléséhez először törölnie kell minden olyan erőforrást, amely az átjárótól függ. A követett lépések a létrehozott kapcsolatok típusától és az egyes kapcsolatok függő erőforrásaitól függenek.

> [!IMPORTANT]
> A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. A klasszikus üzemi modellel központilag telepített VPN-átjáró törléséhez kövesse az [átjáró törlése: klasszikus](vpn-gateway-delete-vnet-gateway-classic-powershell.md) cikk utasításait.

## <a name="delete-a-vpn-gateway"></a>VPN Gateway törlése

Virtuális hálózati átjáró törléséhez először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróra vonatkozik. A függőségek miatt bizonyos sorrendben törölni kell az erőforrásokat.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Ekkor a rendszer törli a virtuális hálózati átjárót.

### <a name="to-delete-additional-resources"></a>További erőforrások törlése

A következő lépések segítségével törölheti a már nem használt erőforrásokat.

#### <a name="to-delete-the-local-network-gateway"></a>A helyi hálózati átjáró törlése

1. Az **összes erőforrás** területen keresse meg az egyes kapcsolatok számára társított helyi hálózati átjárókat.
1. A helyi hálózati átjáró **Áttekintés** paneljén kattintson a **Törlés** elemre.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Az átjáró nyilvános IP-címéhez tartozó erőforrás törlése

1. Az **összes erőforrás** területen keresse meg az átjáróhoz társított nyilvános IP-cím erőforrást. Ha a virtuális hálózati átjáró aktív-aktív, két nyilvános IP-címet fog látni.
1. A nyilvános IP-cím **Áttekintés** lapján kattintson a **Törlés**, majd az **Igen** gombra a megerősítéshez.

#### <a name="to-delete-the-gateway-subnet"></a>Az átjáró-alhálózat törlése

1. A **minden erőforrás** területen keresse meg a virtuális hálózatot. 
1. Az **alhálózatok** panelen kattintson a **GatewaySubnet**, majd a **Törlés** elemre. 
1. Az **Igen** gombra kattintva erősítse meg, hogy törölni szeretné az átjáró-alhálózatot.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>VPN-átjáró törlése az erőforráscsoport törlésével

Ha nem érdekli az erőforráscsoport erőforrásainak megtartása, és csak azt szeretné kezdeni, törölheti a teljes erőforráscsoportot. Ez egy gyors módszer, amellyel mindent eltávolíthat. A következő lépések csak a Resource Manager-alapú üzemi modellre vonatkoznak.

1. Az **összes erőforrás** területen keresse meg az erőforráscsoportot, és kattintson a panel megnyitásához.
1. Kattintson a **Törlés** gombra. A törlés panelen tekintse meg az érintett erőforrásokat. Győződjön meg arról, hogy az összes erőforrást törölni kívánja. Ha nem, használja a cikk elején található VPN Gateway törlésének lépéseit.
1. A folytatáshoz írja be a törölni kívánt erőforráscsoport nevét, majd kattintson a **Törlés** gombra.
