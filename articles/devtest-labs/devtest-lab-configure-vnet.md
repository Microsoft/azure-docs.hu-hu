---
title: Virtuális hálózat konfigurálása Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy meglévő virtuális hálózatot és alhálózatot, és hogyan használhatja azokat egy virtuális gépen Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283554"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Virtuális hálózat konfigurálása Azure DevTest Labsban
Ahogy azt a cikk azt ismerteti, hogyan adhat hozzá egy virtuális gépet a [laborhoz](devtest-lab-add-vm.md), amikor a virtuális gépet egy laborban hozza létre, megadhat egy konfigurált virtuális hálózatot. Előfordulhat például, hogy el kell érnie a Corpnet-erőforrásait a virtuális gépekről a ExpressRoute vagy helyek közötti VPN-kapcsolattal konfigurált virtuális hálózat használatával.

Ez a cikk azt ismerteti, hogyan adható hozzá meglévő virtuális hálózata egy Lab Virtual Network-beállításokhoz, hogy elérhető legyen a virtuális gépek létrehozásakor.

> [!NOTE]
> Az Azure Virtual Network szolgáltatással kapcsolatos költségek megismeréséhez tekintse meg az [azure Virtual Network díjszabását](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Virtuális hálózat konfigurálása laborhoz a Azure Portal használatával
A következő lépések végigvezetik a meglévő virtuális hálózatok (és alhálózatok) egy laborhoz való hozzáadásának lépésein, így a virtuális gép ugyanabban a laborban való létrehozásakor használható. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A laborok listájából válassza ki a kívánt labort. 
1. A labor fő ablaktábláján válassza a **konfiguráció és szabályzatok** lehetőséget.

    ![A labor konfigurációjának és házirendjeinek elérése](./media/devtest-lab-configure-vnet/policies-menu.png)
1. A **külső erőforrások** szakaszban válassza a **virtuális hálózatok** elemet. Megjelenik az aktuális laborhoz konfigurált virtuális hálózatok listája, valamint a laborhoz létrehozott alapértelmezett virtuális hálózat. 
1. Válassza a **+ Hozzáadás** lehetőséget.
   
    ![Meglévő virtuális hálózat hozzáadása a laborhoz](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. A **virtuális hálózat** ablaktáblán válassza a **[virtuális hálózat kiválasztása]** lehetőséget.
   
    ![Meglévő virtuális hálózat kiválasztása](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. A **virtuális hálózat kiválasztása** panelen válassza ki a kívánt virtuális hálózatot. Megjelenik egy lista, amely az előfizetésben a laborban azonos régióban található összes virtuális hálózatot megjeleníti.
1. A virtuális hálózat kiválasztása után a rendszer visszaadja a **virtuális hálózat** panelt. Válassza ki az alhálózatot a lenti listában.

    ![Alhálózati lista](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Megjelenik a tesztkörnyezet alhálózata ablaktábla.

    ![Lab alhálózat panel](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Adja meg a **labor alhálózat nevét**.
   - Ha engedélyezni szeretné az alhálózat használatát a labor virtuális gépek létrehozásában, válassza **a használat a virtuális gép létrehozásakor** lehetőséget.
   - [Megosztott nyilvános IP-cím](devtest-lab-shared-ip.md)engedélyezéséhez válassza a **megosztott nyilvános IP-címek engedélyezése** lehetőséget.
   - Ha engedélyezni szeretné a nyilvános IP-címeket egy alhálózatban, válassza a **nyilvános IP-címek létrehozásának engedélyezése** lehetőséget.
   - A **virtuális gépek maximális száma** felhasználónként mezőben határozza meg az egyes alhálózatok felhasználónként maximális számát. Ha nem korlátozott számú virtuális gépet szeretne használni, hagyja üresen ezt a mezőt.
1. Kattintson az **OK** gombra a labor alhálózat panel bezárásához.
1. A virtuális hálózat ablaktábla bezárásához válassza a **Mentés** lehetőséget.

Most, hogy a virtuális hálózat be van állítva, akkor kiválasztható virtuális gép létrehozásakor. Ha szeretné megtekinteni, hogyan hozható létre virtuális gép, és hogyan adhat meg virtuális hálózatot, tekintse meg a cikket, [adjon hozzá egy virtuális gépet egy laborhoz](devtest-lab-add-vm.md). 

Az Azure [Virtual Network dokumentációja](../virtual-network/index.yml) további információkat nyújt a virtuális hálózatok használatáról, beleértve a VNet beállítását és kezelését, valamint a helyszíni hálózathoz való kapcsolódást.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
Miután hozzáadta a kívánt virtuális hálózatot a laborhoz, a következő lépés [egy virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md).
