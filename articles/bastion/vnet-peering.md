---
title: VNet peering és Azure Bastion architektúra
description: Ebből a cikkből megtudhatja, hogyan használható a VNet-társítás és az Azure Bastion együtt a virtuális gépekhez való kapcsolódáshoz.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710583"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet-peering és Azure Bastion (előzetes verzió)

Az Azure Bastion és a VNet peering együttes használata is lehetséges. Ha a VNet-társítás konfigurálva van, nem kell telepítenie az Azure Bastion-t minden egyes VNet. Ez azt jelenti, hogy ha egy virtuális hálózatban (VNet) van konfigurálva egy Azure Bastion-gazdagép, akkor a rendszer egy további megerősített állomás üzembe helyezése nélkül használható a társ VNet üzembe helyezett virtuális gépekhez való kapcsolódáshoz. További információ a VNet-kezelésről: [Tudnivalók a virtuális hálózatok](../virtual-network/virtual-network-peering-overview.md)összevonásáról.

Az Azure Bastion a következő típusú partnerekkel működik:

* **Virtuális hálózat** társítása: Ugyanazon az Azure-régióban lévő virtuális hálózatok összekapcsolhatók.
* **Globális virtuális hálózati társítás:** Virtuális hálózatok összekapcsolása az Azure-régiók között.

## <a name="architecture"></a>Architektúra

Ha a VNet-társítás konfigurálva van, az Azure Bastion központi és küllős vagy teljes hálós topológiákban is üzembe helyezhető. Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében.

Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózatban, az RDP/SSH-élmény az összes virtuális gép számára elérhető az azonos VNet, valamint a virtuális hálózatok. Ez azt jelenti, hogy konszolidálhatja a megerősített üzembe helyezést egyetlen VNet, és továbbra is elérheti a VNet üzembe helyezett virtuális gépeket, központosítva a teljes üzembe helyezést.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Tervezési és architektúra diagram":::

Ez az ábra egy Azure-alapú megerősített telepítés architektúráját mutatja egy sugaras modellben. Ebben a diagramban a következő konfiguráció látható:

* A megerősített gazdagép üzembe helyezése a központi hub virtuális hálózatban történik.
* Központi hálózati biztonsági csoport (NSG) üzembe helyezése.
* A nyilvános IP-cím nem szükséges az Azure-beli virtuális gépen.

**Lépéseket**

1. Kapcsolódjon a Azure Portal bármely HTML5 böngésző használatával.
2. Győződjön meg arról, hogy **olvasási** hozzáférése van a célként megadott virtuális géphez és a társ VNet. Emellett az alábbi erőforrásokra vonatkozó olvasási jogosultsággal keresse meg a következőt:
   * Olvasó szerepkör a virtuális gépen.
   * Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.
   * Olvasó szerepkör az Azure-beli megerősített erőforráson.
   * A Virtual Network olvasó szerepköre (nem szükséges, ha nincs egyenrangú virtuális hálózat).
3. Ha szeretné megtekinteni a megerősített adatpontot a **Kapcsolódás** legördülő menüben, ki kell választania az **előfizetéshez > globális előfizetéshez** hozzáférő köv-ket.
4. Válassza ki a virtuális gépet, amelyhez csatlakozni szeretne.
5. Az Azure Bastion zökkenőmentesen észlelhető a különböző VNet.
6. Egyetlen kattintással megnyílik az RDP/SSH-munkamenet a böngészőben. Az RDP és az SSH egyidejű munkamenet-korlátaival kapcsolatban lásd: [RDP-és SSH-munkamenetek](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Kapcsolódás":::

   További információ a virtuális gépekhez az Azure Bastion használatával történő csatlakozásról:

   * [Kapcsolódjon egy VM-RDP-](bastion-connect-vm-rdp.md)hez.
   * [Kapcsolódás virtuális géphez – SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>GYIK

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
