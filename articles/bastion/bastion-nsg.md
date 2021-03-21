---
title: Virtuális gépek és NSG használata az Azure Bastion-ben
description: A hálózati biztonsági csoportokat az Azure Bastion használatával is használhatja. Az ehhez a konfigurációhoz szükséges alhálózatok ismertetése.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700218"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>A NSG-hozzáférés és az Azure Bastion használata

Az Azure Bastion használatakor hálózati biztonsági csoportokat (NSG) is használhat. További információ: [biztonsági csoportok](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Ebben a diagramban:

* A megerősített gazdagép üzembe helyezése a virtuális hálózaton történik.
* A felhasználó bármely HTML5 böngésző használatával csatlakozik a Azure Portalhoz.
* A felhasználó az Azure-beli virtuális gépre RDP/SSH-ra navigál.
* Integrációs csatlakozás – egyetlen kattintással RDP/SSH-munkamenet a böngészőben
* Nem szükséges nyilvános IP-cím az Azure-beli virtuális gépen.

## <a name="network-security-groups"></a><a name="nsg"></a>Hálózati biztonsági csoportok

Ez a szakasz a felhasználó és az Azure-alapú megerősített hálózat közötti hálózati forgalmat, valamint a virtuális hálózatban megcélzott virtuális gépeket mutatja be:

> [!IMPORTANT]
> Ha úgy dönt, hogy NSG használ az Azure Bastion-erőforráshoz, akkor az alábbi bejövő és kimenő forgalmi szabályok mindegyikét létre **kell** hoznia. Ha kihagyja a következő szabályok valamelyikét a NSG, azzal letiltja az Azure Bastion-erőforrást, hogy a jövőben is fogadja a szükséges frissítéseket, ezért az erőforrást a jövőbeli biztonsági rések számára nyithatja meg.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Az Azure Bastion üzembe helyezése kifejezetten a ***AzureBastionSubnet***.

* **Bejövő forgalom:**

   * **Bejövő forgalom a nyilvános internetről:** Az Azure Bastion létrehoz egy nyilvános IP-címet, amelyhez a 443-es port szükséges a nyilvános IP-címen a bejövő forgalom számára. A AzureBastionSubnet nem szükséges megnyitni a 3389/22-es portot.
   * Forgalom átadása **Az Azure Bastion Control Plan síkja:** A vezérlési sík kapcsolata esetében engedélyezze a 443-es portot a **GatewayManager** szolgáltatás címkéjén. Ez lehetővé teszi, hogy az átjáró-kezelő képes legyen kommunikálni az Azure Bastion-vel.
   * **Forgalom beáramlása az Azure Bastion-adatsíkon:** Az Azure Bastion mögöttes összetevői közötti adatsík kommunikációhoz engedélyezze a 8080-es, 5701-es portokat a **VirtualNetwork** szolgáltatás címkéjén a **VirtualNetwork** szolgáltatás címkéjén. Ez lehetővé teszi, hogy az Azure Bastion összetevői kommunikálhassanak egymással.
   * Bejövő **forgalom a Azure Load Balancerról:** Az állapot-mintavételek esetében engedélyezze a 443-es portot a **AzureLoadBalancer** szolgáltatás címkéjén. Ez lehetővé teszi a Azure Load Balancer számára a kapcsolatok észlelését


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Képernyőfelvétel: a bejövő biztonsági szabályok az Azure Bastion-kapcsolathoz.":::

* **Kimenő forgalom:**

   * **Kimenő forgalom a cél virtuális gépek felé:** Az Azure Bastion a cél virtuális gépeket magánhálózati IP-címekre fogja elérni. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokhoz a 3389-es és 22-es porton.
   * **Kimenő forgalom az Azure Bastion-adatsíkon:** Az Azure Bastion mögöttes összetevői közötti adatsík kommunikációhoz engedélyezze a 8080-es, 5701-as portot a **VirtualNetwork** szolgáltatás címkéjéről a **VirtualNetwork** szolgáltatás címkéjére. Ez lehetővé teszi, hogy az Azure Bastion összetevői kommunikálhassanak egymással.
   * **Kimenő forgalom az Azure más nyilvános végpontjai felé:** Az Azure Bastion-nek képesnek kell lennie az Azure-on belüli különböző nyilvános végpontokhoz való kapcsolódásra (például diagnosztikai naplók és mérési naplók tárolására). Ezért az Azure Bastion-nek 443-re kell **AzureCloud** a szolgáltatási címkéhez.
   * **Kimenő forgalom az internetre:** Az Azure Bastion-nek képesnek kell lennie kommunikálni az internettel a munkamenet és a tanúsítvány érvényesítéséhez. Ezért javasoljuk, hogy engedélyezze az 80-es portot az **internetre.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="A képernyőképen az Azure Bastion-kapcsolat kimenő biztonsági szabályai láthatók.":::

### <a name="target-vm-subnet"></a>Cél virtuálisgép-alhálózat
Ez az az alhálózat, amely az RDP/SSH-t tartalmazó cél virtuális gépet tartalmazza.

   * **Bejövő forgalom az Azure Bastion-ből:** Az Azure Bastion privát IP-címen éri el a cél virtuális gépet. Az RDP-/SSH-portokat (3389/22-as portokat) meg kell nyitni a cél virtuális gépen a privát IP-címeken. Az ajánlott eljárás az, ha az Azure megerősített alhálózati IP-címtartományt ebben a szabályban adja hozzá, hogy csak a Bastion tudja megnyitni ezeket a portokat a célként megadott virtuálisgép-alhálózatban lévő virtuális gépeken.


## <a name="next-steps"></a>Következő lépések

További információ az Azure Bastion-ről: [Gyakori kérdések](bastion-faq.md).
