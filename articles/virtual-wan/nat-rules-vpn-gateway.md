---
title: VPN NAT-szabályok konfigurálása az átjáróhoz
titleSuffix: Azure Virtual WAN
description: Ismerje meg, hogyan konfigurálhatja a VWAN VPN-átjáró NAT-szabályait
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745865"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>NAT-szabályok konfigurálása a virtuális WAN VPN-átjáróhoz – előzetes verzió

> [!IMPORTANT]
> A NAT-szabályok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A virtuális WAN VPN-átjárót konfigurálhatja statikus egy-az-egyhez NAT-szabályokkal. A NAT-szabályok egy mechanizmust biztosítanak az IP-címek egy-az-egyhez fordításának beállításához. A NAT segítségével két olyan IP-hálózatot lehet összekapcsolni, amely nem kompatibilis vagy átfedésben lévő IP-címekkel rendelkezik. Egy tipikus forgatókönyv olyan átfedésben lévő IP-címekkel rendelkező ágak, amelyek az Azure VNet erőforrásaihoz szeretnének hozzáférni.

Ez a konfiguráció egy flow-táblázatot használ a külső (gazdagép) IP-címről érkező forgalom átirányítására egy virtuális hálózaton (virtuális gépen, számítógépen, tárolón stb.) belüli végponthoz társított belső IP-címekre.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Az architektúrát ábrázoló diagram.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Szabályok konfigurálása és megtekintése

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

### <a name="configuration-considerations"></a>Konfigurációs megfontolások

* A belső és külső hozzárendelések alhálózat-méretének azonosnak kell lennie a statikus egy-az-egyhez típusú NAT esetében.
* Ügyeljen arra, hogy a Azure Portal található VPN-helyet **ExternalMapping** adja hozzá a "privát címtartomány" mezőben. Jelenleg a BGP-t használó helyeknek biztosítaniuk kell, hogy a helyszíni BGP bemondója (eszköz BGP-beállításai) tartalmazzon egy bejegyzést a külső leképezés előtagjaihoz.

## <a name="next-steps"></a>Következő lépések

További információ a helyek közötti konfigurációkról: [virtuális WAN-helyek közötti kapcsolat konfigurálása](virtual-wan-site-to-site-portal.md).
