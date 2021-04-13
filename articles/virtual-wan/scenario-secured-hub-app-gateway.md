---
title: Application Gateway-és háttér-készletek közötti adatforgalom védelme
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztáshoz – biztonságos forgalom, amely egy biztonságos virtuális WAN-hubhoz csatlakoztatott, küllős VNet üzemelő Application gatewayen keresztül halad át.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315576"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Forgatókönyv: Application Gateway és háttérbeli készletek közötti adatforgalom védelme

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben az esetben a felhasználó forgalma az Azure-ba egy olyan, küllős VNet üzembe helyezett Application Gateway használatával történik, amely egy biztonságos virtuális WAN-hubhoz (Azure Firewall) csatlakozó virtuális WAN-hubhoz csatlakozik. A cél a biztonságos virtuális központ Azure Firewall használata az Application Gateway és a háttérbeli készletek közötti forgalom vizsgálatához.

Ebben a forgatókönyvben két konkrét kialakítási minta van, attól függően, hogy az Application Gateway és a háttér-készletek ugyanabban a VNet, vagy eltérő virtuális hálózatok találhatók-e.

* **1. forgatókönyv:** Az Application Gateway és a háttér-készletek ugyanabban a virtuális hálózatban találhatók, mint a virtuális WAN-központ (külön alhálózatok).
* **2. forgatókönyv:** Az Application Gateway és a háttér-készletek különböző virtuális hálózatokban találhatók, amelyek egy virtuális WAN-központhoz vannak csatlakoztatva.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>1. forgatókönyv – azonos VNet

Ebben az esetben az Application Gateway és a háttér-készletek ugyanabban a virtuális hálózatban vannak, mint a virtuális WAN-központ (külön alhálózatok).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Az 1. forgatókönyv ábrája" lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Munkafolyamat

Jelenleg a virtuális WAN-útválasztási táblázatból a küllő virtuális hálózatokra hirdetett útvonalak a teljes virtuális hálózatra lesznek alkalmazva, és nem a küllős VNet alhálózatán. Ennek eredményeképpen a felhasználó által megadott útvonalak szükségesek ennek a forgatókönyvnek az engedélyezéséhez. A felhasználó által megadott útvonalakkal (UDR) kapcsolatos információkért lásd: [Virtual Network egyéni útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. A Azure Firewall Managerben az Application Gateway és a háttér-készleteket tartalmazó küllő virtuális hálózaton válassza a **biztonságos internetes forgalom engedélyezése** és a **biztonságos magánhálózati forgalom engedélyezése** lehetőséget.
1. Konfigurálja a felhasználó által megadott útvonalakat (UDR) az Application Gateway alhálózaton.

   * A következő UDR megadásával biztosíthatja, hogy az Application Gateway képes legyen közvetlenül az internetre küldeni a forgalmat:

     * **Címzési előtag:** 0.0.0.0.0/0
     * **Következő ugrás:** Internet

   * Annak biztosítása érdekében, hogy az Application Gateway képes legyen a háttér-készlet felé irányuló adatforgalom küldésére a virtuális WAN-központban Azure Firewall keresztül, a következő UDR kell megadnia:

      * **Címzési előtag:** Háttérbeli készlet alhálózata (10.2.0.0/24)
      * **Következő ugrás:** Azure Firewall magánhálózati IP-cím

1. Konfiguráljon egy felhasználó által megadott útvonalat (UDR) a háttérbeli készlet alhálózatán.

   * **Címzési előtag:** Alhálózat Application Gateway
   * **Következő ugrás:** Azure Firewall magánhálózati IP-cím

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>2. forgatókönyv – különböző virtuális hálózatok

Ebben az esetben az Application Gateway és a háttér-készletek különböző virtuális hálózatokban találhatók, amelyek egy virtuális WAN-központhoz vannak csatlakoztatva.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="A 2. forgatókönyv diagramja" lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Munkafolyamat

Jelenleg a virtuális WAN-útválasztási táblázatból a küllő virtuális hálózatokra hirdetett útvonalak a teljes virtuális hálózatra lesznek alkalmazva, és nem a küllős VNet alhálózatán. Ennek eredményeképpen a felhasználó által megadott útvonalak szükségesek ennek a forgatókönyvnek az engedélyezéséhez. A felhasználó által megadott útvonalakkal (UDR) kapcsolatos információkért lásd: [Virtual Network egyéni útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. **Azure Firewall Managerben** válassza a **biztonságos internetes forgalom engedélyezése** lehetőséget, és **engedélyezze a biztonságos magánhálózati forgalmat** mindkét küllős virtuális hálózaton.

1. Konfigurálja a felhasználó által megadott útvonalakat (UDR) az Application Gateway alhálózaton. A következő UDR megadásával biztosíthatja, hogy az Application Gateway képes legyen közvetlenül az internetre küldeni a forgalmat:

   * **Címzési előtag:** 0.0.0.0.0/0
   * **Következő ugrás:** Internet

## <a name="next-steps"></a>Következő lépések

* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
* A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [Virtual Network egyéni útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* További információ a virtuális WAN által védett virtuális hubokról: [biztonságos virtuális hubok](../firewall-manager/secured-virtual-hub.md).
