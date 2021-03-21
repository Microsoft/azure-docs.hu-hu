---
title: Az Azure Route Server (előzetes verzió) támogatása a ExpressRoute és az Azure VPN-hez
description: Ismerje meg, hogy az Azure Route Server hogyan kommunikál a ExpressRoute és az Azure VPN Gateway átjárókkal.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679972"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Az Azure Route Server (előzetes verzió) támogatása a ExpressRoute és az Azure VPN-hez

Az Azure Route Server nem csak az Azure-on futó, külső gyártótól származó hálózati virtuális berendezések (NVA-EK) használatát támogatja, azonban zökkenőmentesen integrálható a ExpressRoute és az Azure VPN Gateway átjárókkal. Nem kell konfigurálnia vagy kezelnie a BGP-társat az átjáró és az Azure-útválasztó kiszolgálója között. Az átjáró és az Azure Route Server közötti útvonal-csere egyszerű [konfigurációs módosítással](quickstart-configure-route-server-powershell.md#route-exchange)engedélyezhető.

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Hogyan működik?

Ha egy Azure-útválasztó kiszolgálót és egy ExpressRoute-átjárót, valamint egy virtuális hálózatban lévő NVA telepít az alapértelmezett Azure Route Server, nem terjeszti a NVA és a ExpressRoute átjárótól kapott útvonalakat egymás között. Miután engedélyezte az útválasztási Exchange-t, a ExpressRoute és a NVA megtudhatja egymás útvonalait.

Például a következő ábrán:

* A SDWAN készülék az Azure Route Servertől kapja az útvonalat az "on-Prem 2" útvonalról, amely a ExpressRoute-hez csatlakozik, valamint a virtuális hálózati útvonalat is.

* A ExpressRoute-átjáró az "on-Prem 1" útvonalról kapja meg az útvonalat, amely a SDWAN-berendezéshez csatlakozik, valamint az Azure Route Server virtuális hálózati útvonalát is.

    ![A Route Serverrel konfigurált ExpressRoute mutató diagram.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Az SDWAN-berendezést az Azure VPN Gateway használatával is lecserélheti. Mivel az Azure VPN Gateway és a ExpressRoute teljes körűen felügyelt, csak akkor kell engedélyeznie a két helyszíni hálózat útválasztási cseréjét, hogy kommunikáljon egymással.

> [!IMPORTANT] 
> Az Azure VPN-átjárót [**aktív-aktív**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) módban kell konfigurálni.
>

![Az útválasztási kiszolgálóval konfigurált ExpressRoute és VPN-átjárót bemutató ábra.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Route Serverről](route-server-faq.md).
- Ismerje meg, hogyan [konfigurálhatja az Azure Route Servert](quickstart-configure-route-server-powershell.md).
- További információ az [Azure ExpressRoute és az Azure VPN együttes létezéséről](../expressroute/expressroute-howto-coexist-resource-manager.md).
