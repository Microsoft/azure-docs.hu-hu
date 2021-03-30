---
title: 'Virtuális központ tényleges útvonalának megtekintése: Azure Virtual WAN | Microsoft Docs'
description: Virtuális központ tényleges útvonalának megtekintése az Azure Virtual WAN-ban
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983653"
---
# <a name="view-virtual-hub-effective-routes"></a>Virtuális központ érvényes útvonalainak megtekintése

A Azure Portalban megtekintheti a virtuális WAN-központ összes útvonalát. Ez a cikk végigvezeti a hatályos útvonalak megtekintésének lépésein. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Kapcsolatok vagy útválasztási táblák kiválasztása

1. Navigáljon a virtuális hubhoz, majd válassza az **Útválasztás** elemet. Az Útválasztás lapon válassza az **érvényes útvonalak** lehetőséget.
1. A legördülő listából kiválaszthatja az **útválasztási táblázatot**. Ha nem látja az útválasztási táblázat lehetőséget, ez azt jelenti, hogy nincs beállítva egyéni vagy alapértelmezett útválasztási tábla ebben a virtuális központban.

## <a name="view-output"></a><a name="output"></a>Kimenet megtekintése

Az oldal kimenete a következő mezőket jeleníti meg:

* **Előtag**: az aktuális entitáshoz (a virtuális központ útválasztóján) ismert címzési előtag
* **Következő ugrás típusa**: lehet Virtual Network kapcsolat, a VPN_S2S_Gateway, a ExpressRouteGateway, a távoli központ vagy a Azure Firewall.
* **Következő ugrás**: Ez a következő ugrás erőforrás-azonosítójára mutató hivatkozás, vagy egyszerűen azt mutatja, hogy a hivatkozás az aktuális hubhoz utal.
* **Forrás: az** útválasztási forrás erőforrás-azonosítója.
* **Elérési út**: a BGP attribútum as (autonóm rendszer) elérési útja felsorolja az összes olyan számot, amelyet át kell adni ahhoz, hogy elérje azt a helyet, ahol az elérési utat csatolták, a meghirdetve.

### <a name="example"></a><a name="example"></a>Például

A következő példában szereplő értékek azt jelzik, hogy a virtuális hub-kapcsolatok vagy az útválasztási táblázat megtanulta a 10.2.0.0/24 (ág-előtag) útvonalát. Megtanulta az útvonalat a **VPN következő ugrásának típusa** VPN_S2S_Gateway a **következő ugrás** VPN Gateway erőforrás-azonosítóval. Az **útvonal kezdőpontja** a kezdeményező VPN-átjáró/útválasztási tábla/kapcsolat erőforrás-azonosítójára mutat. Az **elérési** út a ág as elérési útját jelöli.

A táblázat alján található görgetősáv használatával megtekintheti az "AS Path" kifejezést.

| **Előtag** |  **A következő ugrás típusa** | **Következő ugrás** |  **Útvonal forrása** |**Elérési út** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Szempontok**

* Ha a a 0.0.0.0/0 értéket látja a **hatékony útvonalak lekérése** kimenetben, az azt jelenti, hogy az útvonal létezik az egyik útválasztási táblában. Ha azonban ez az útvonal az internetre lett beállítva, egy további **"enableInternetSecurity"** jelzőt kell megadnia a kapcsolatban. A virtuálisgép-hálózati adapteren érvényes útvonal nem jeleníti meg az útvonalat, ha a kapcsolat "enableInternetSecurity" jelzője "false" (hamis).

* A virtuális hálózati kapcsolat, a VPN-kapcsolat vagy egy ExpressRoute-kapcsolat szerkesztése során az **alapértelmezett propagálási útvonal** mező látható az Azure Virtual WAN portálon. Ebben a mezőben a **enableInternetSecurity** jelző látható, amely a ExpressRoute és a VPN-kapcsolatok esetében mindig "false", de a virtuális hálózati kapcsolatok esetében "igaz".

* Ha a virtuális gép hálózati adapterén tekinti meg a tényleges útvonalakat, ha a következő ugrást "Virtual Network átjáróként" látja, akkor ez azt jelenti, hogy a virtuális központ útválasztója, ha a virtuális gép egy virtuális WAN-hubhoz van csatlakoztatva.

* Egy virtuális központ útválasztási táblájának érvényes útvonalait csak akkor kell megtekinteni, ha a virtuális hubhoz legalább egy kapcsolat (VPN/ER/VNET) csatlakozik.

## <a name="next-steps"></a>Következő lépések

* A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
