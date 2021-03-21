---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356652"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Továbbra is telepíthetek több megerősített gazdagépet a kihelyezett virtuális hálózatok között?

Igen. Alapértelmezés szerint a felhasználó az ugyanabban a virtuális hálózaton üzembe helyezett megerősített gazdagépet látja, amelyben a virtuális gép található. A **kapcsolat** menüben azonban láthatja, hogy egy felhasználó több megerősített gazdagépet észlelt a több hálózat között. Kiválaszthatják azt a megerősített gazdagépet, amelyet szívesebben használnak a virtuális hálózaton üzembe helyezett virtuális GÉPHEZ való kapcsolódáshoz.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Ha a társas virtuális hálózatok különböző előfizetésekben vannak telepítve, akkor a a megerősített munkafolyamaton keresztül fog kapcsolódni?

Igen, a megerősített kapcsolaton keresztül folytatott kapcsolatok továbbra is működnek a különböző előfizetésekben lévő, egyetlen bérlőre kiterjedő virtuális hálózatok. A két különböző bérlőn belüli előfizetések nem támogatottak. Ha meg szeretné tekinteni a megerősített adatcseréket a **Kapcsolódás** legördülő menüben, a felhasználónak ki kell választania az **előfizetéshez > globális előfizetéshez** hozzáférő köv.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Globális előfizetések szűrője" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Hozzáférek a VNet, de nem látom az ott üzembe helyezett virtuális gépet.

Győződjön meg arról, hogy a felhasználó rendelkezik **olvasási** hozzáféréssel mind a virtuális géphez, mind a társ VNet. Továbbá győződjön meg arról, hogy a felhasználó rendelkezik **olvasási** hozzáféréssel a következő erőforrásokhoz:

* Olvasó szerepkör a virtuális gépen.
* Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.
* Olvasó szerepkör az Azure-beli megerősített erőforráson.
* A Virtual Network olvasó szerepköre (nem szükséges, ha nincs egyenrangú virtuális hálózat).

|Engedélyek|Description|Engedély típusa|
|---|---| ---|
|Microsoft. Network/bastionHosts/READ |Megerősített gazdagép beolvasása|Művelet|
|Microsoft. Network/virtualNetworks/BastionHosts/Action |Beolvas egy Virtual Network.|Művelet|
|Microsoft. Network/virtualNetworks/bastionHosts/default/művelet|Beolvas egy Virtual Network.|Művelet|
|Microsoft. Network/networkInterfaces/READ|Hálózati adapter definíciójának beolvasása.|Művelet|
|Microsoft. Network/networkInterfaces/ipconfigurations/READ|Hálózati adapter IP-konfigurációjának meghatározását kéri le.|Művelet|
|Microsoft. Network/virtualNetworks/READ|A virtuális hálózat definíciójának beolvasása|Művelet|
|Microsoft. Network/virtualNetworks/Subnets/virtualMachines/READ|A virtuális hálózati alhálózat összes virtuális gépre mutató hivatkozás beolvasása|Művelet|
|Microsoft. Network/virtualNetworks/virtualMachines/READ|A virtuális hálózatban lévő összes virtuális gépre mutató hivatkozásokat kap|Műveletek|