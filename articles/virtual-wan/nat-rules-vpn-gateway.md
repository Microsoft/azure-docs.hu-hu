---
title: VPN NAT-szabályok konfigurálása az átjáróhoz
titleSuffix: Azure Virtual WAN
description: Ismerje meg, hogyan konfigurálhatja a VWAN VPN-átjáró NAT-szabályait
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431216"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>NAT-szabályok konfigurálása a virtuális WAN VPN-átjáróhoz – előzetes verzió

> [!IMPORTANT]
> A NAT-szabályok jelenleg nyilvános előzetes verzióban érhetők el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A virtuális WAN VPN-átjárót konfigurálhatja statikus egy-az-egyhez NAT-szabályokkal. A NAT-szabályok egy mechanizmust biztosítanak az IP-címek egy-az-egyhez fordításának beállításához. A NAT segítségével két olyan IP-hálózatot lehet összekapcsolni, amely nem kompatibilis vagy átfedésben lévő IP-címekkel rendelkezik. Egy tipikus forgatókönyv olyan átfedésben lévő IP-címekkel rendelkező ágak, amelyek az Azure VNet erőforrásaihoz szeretnének hozzáférni.

Ez a konfiguráció egy flow-táblázatot használ a külső (gazdagép) IP-címről érkező forgalom átirányítására egy virtuális hálózaton (virtuális gépen, számítógépen, tárolón stb.) belüli végponthoz társított belső IP-címekre.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Az architektúrát ábrázoló diagram.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT-szabályok konfigurálása

A VPN-átjáró beállításain bármikor konfigurálhatja és megtekintheti a NAT-szabályokat.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="A szerkesztési szabályokat bemutató képernyőkép.":::

1. Navigáljon a virtuális hubhoz.
1. Válassza **a VPN (hely helye)** lehetőséget.
1. Válassza a **NAT-szabályok (Szerkesztés)** lehetőséget.
1. A **NAT-szabály szerkesztése** lapon a következő értékek használatával **hozzáadhat/SZERKESZTHET vagy törölhet NAT-** szabályokat:

   * **Név:** A NAT-szabály egyedi neve.
   * **Írja be a következőt:** Statikus. A statikus egy-az-egyhez típusú NAT egy-az-egyhez kapcsolatot hoz létre egy belső és egy külső címe között.
   * **Mód:** IngressSnat vagy EgressSnat.  
      * A IngressSnat mód (más néven bejövő forrás NAT) az Azure hub helyek közötti VPN-átjáróra belépő forgalmára vonatkozik.
      * A EgressSnat mód (más néven kimenő forrás NAT) olyan forgalomra vonatkozik, amely elhagyja az Azure hub helyek közötti VPN-átjáróját.
   * **InternalMapping:** A belső hálózaton lévő forrás IP-címek tartománya, amely külső IP-címekre lesz leképezve. Más szóval a NAT előtagja a címfordítás előtti tartománya.
   * **ExternalMapping:** A külső hálózatban lévő cél IP-címek tartománya, amely a forrás IP-címeket fogja leképezni. Más szóval a NAT-beli címtartomány-előtag tartománya.
   * **Kapcsolat csatolása:** A kapcsolati erőforrás, amely gyakorlatilag összekapcsolja a VPN-helyet az Azure hub helyek közötti VPN-átjáróval.

### <a name="configuration-considerations"></a><a name="considerations"></a>Konfigurációs megfontolások

* A belső és külső hozzárendelések alhálózat-méretének azonosnak kell lennie a statikus egy-az-egyhez típusú NAT esetében.
* Ügyeljen arra, hogy a Azure Portal található VPN-helyet **ExternalMapping** adja hozzá a "privát címtartomány" mezőben. Jelenleg a BGP-t használó helyeknek biztosítaniuk kell, hogy a helyszíni BGP bemondója (eszköz BGP-beállításai) tartalmazzon egy bejegyzést a külső leképezés előtagjaihoz.

## <a name="examples-and-verification"></a><a name="examples"></a>Példák és ellenőrzés

### <a name="ingress-mode-nat"></a>Bejövő forgalom mód NAT

A bejövő módú NAT-szabályok az Azure-t a virtuális WAN-helyek közötti VPN-átjárón keresztül belépő csomagokra vonatkoznak. Ebben az esetben két helyek közötti VPN-ágat szeretne csatlakozni az Azure-hoz. Az 1. VPN-hely a Link1-on keresztül csatlakozik, és a 2. hivatkozáson keresztül csatlakozik a VPN-hely. Minden helyhez a 192.169.1.0/24 címtartomány tartozik.

A következő ábrán a tervezett végeredmény látható:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="A bejövő forgalom módú NAT-t bemutató ábra.":::

1. NAT-szabályt kell megadnia.

   Egy NAT-szabályt kell megadnia, amely biztosítja, hogy a helyek közötti VPN-átjáró képes legyen megkülönböztetni a két, átfedésben lévő címtartományt (például 192.168.1.0/24). Ebben a példában az 1. VPN-hely Link1 koncentrálunk.

   A következő NAT-szabály beállítható és társítható az egyik ág 1. hivatkozásához. Mivel ez egy statikus NAT-szabály, a InternalMapping és a ExternalMapping címtartomány azonos számú IP-címet tartalmaz.

   * **Név:** IngressRule01
   * **Írja be a következőt:** Statikus
   * **Mód:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Kapcsolat csatolása:** 1. hivatkozás

1. A megfelelő ExternalMapping reklámozása.

   Ebben a lépésben ellenőrizze, hogy a helyek közötti VPN-átjáró a megfelelő ExternalMapping-címtartományt hirdeti-e a többi Azure-erőforráshoz. Különböző utasítások vannak, attól függően, hogy a BGP engedélyezve van-e, vagy sem.

   **1. példa: a BGP engedélyezve van**

   * Győződjön meg arról, hogy az 1. VPN-helyen található helyszíni BGP-hangszóró úgy van konfigurálva, hogy hirdesse a 10.1.1.0/24 címtartományt.
   * Ebben az előzetes verzióban a BGP-t használó helyeknek biztosítaniuk kell, hogy a helyszíni BGP bejelentése (eszköz BGP-beállításai) tartalmazzon egy bejegyzést a külső leképezés előtagjaihoz.

   **2. példa: a BGP nincs engedélyezve**

   * Navigáljon a két hálózat közötti pont-pont típusú VPN-átjárót tartalmazó virtuális hub-erőforráshoz. A virtuális központ lap **kapcsolat** területén válassza a **VPN (helyek közötti)** lehetőséget.
   * Válassza ki a virtuális WAN-hubhoz csatlakozó VPN-helyet az 1. hivatkozáson keresztül. Válassza a **hely szerkesztése** és a 10.1.1.0/24 lehetőséget a VPN-hely magánhálózati címeként.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="A VPN-hely szerkesztése lapot ábrázoló képernyőfelvétel.":::

### <a name="packet-flow"></a>Csomag folyamata

Ebben a példában egy helyszíni eszköz egy küllős virtuális hálózatot szeretne elérni. A csomagok folyamata a következő, a NAT-fordítások félkövérrel.

1. A helyszíni forgalom kezdeményezve.
   * Forrás IP-címe: **192.168.1.1**
   * Cél IP-címe: 30.0.0.1
1. A forgalom helyek közötti átjáróba kerül, és a NAT-szabály használatával van lefordítva, majd a Küllőre lesz küldve.
   * Forrás IP-címe: **10.1.1.1**
   * Cél IP-címe: 30.0.0.1
1. A küllő válasza kezdeményezve.
   * Forrás IP-címe: 30.0.0.1
   * Cél IP-címe: **10.1.1.1**
1. A forgalom a két hálózat közötti pont-pont típusú VPN-átjáróba kerül, a fordítás pedig fordított, és a helyszínen történik.
   * Forrás IP-címe: 30.0.0.1
   * Cél IP-címe: **192.168.1.1**

### <a name="verification-checks"></a>Ellenőrzési ellenőrzések

Ez a szakasz ellenőrzi, hogy a konfiguráció megfelelően van-e beállítva.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable, szabályok és útvonalak ellenőrzése

A virtuális WAN-beli ágak a **DefaultRouteTable**, ami az összes ág kapcsolatát a DefaultRouteTable belül kitöltött útvonalakon tanulja meg. A NAT-szabályt a DefaultRouteTable érvényes útvonalakon láthatja a külső leképezési előtaggal.

Példa:

* **Előtag:** 10.1.1.0/24  
* **Következő ugrás típusa:** VPN_S2S_Gateway
* **Következő ugrás:** Erőforrás VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>A címek előtagjainak ellenőrzése

Ez a példa a DefaultRouteTable társított virtuális hálózatok erőforrásaira vonatkozik.

A virtuális WAN-hubhoz csatlakoztatott, küllős virtuális hálózatban található virtuális gépek hálózati adapterei (NIC) **érvényes útvonalait** a NAT-szabályok **ExternalMapping** is tartalmaznia kell.

#### <a name="validate-bgp-advertisements"></a>BGP-hirdetmények ellenőrzése

Ha van BGP konfigurálva a VPN-hely kapcsolaton, ellenőrizze a helyszíni BGP-hangszórót, és győződjön meg róla, hogy reklámban szerepel a külső leképezés előtagjainak bejegyzése.

## <a name="next-steps"></a>Következő lépések

További információ a helyek közötti konfigurációkról: [virtuális WAN-helyek közötti kapcsolat konfigurálása](virtual-wan-site-to-site-portal.md).
