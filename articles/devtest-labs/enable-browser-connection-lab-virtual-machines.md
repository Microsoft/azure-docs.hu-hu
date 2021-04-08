---
title: Böngészőalapú kapcsolatok engedélyezése Azure DevTest Labs virtuális gépeken
description: A DevTest Labs mostantól integrálva van az Azure Bastion szolgáltatással, a labor tulajdonosaként pedig engedélyezheti a tesztkörnyezet összes virtuális gépe elérését egy böngészőben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d9d631c79c22f1f713cfc4ee7cdd766a4ad8f06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341172"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Böngészőalapú kapcsolatok engedélyezése Azure DevTest Labs virtuális gépeken 
A DevTest Labs integrálható az [Azure Bastion](../bastion/index.yml)szolgáltatással, amely lehetővé teszi a virtuális gépekhez való kapcsolódást egy böngészőben. Először engedélyeznie kell a böngészőalapú kapcsolódást a labor virtuális gépeken.

A labor tulajdonosaként engedélyezheti az összes Tesztkörnyezet virtuális gép elérését egy böngészőben. Nincs szüksége további ügyfélre, ügynökre vagy szoftverre. Az Azure Bastion biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül a TLS-en keresztül Azure Portal. Amikor az Azure Bastion-n keresztül kapcsolódik, a virtuális gépeknek nincs szükségük nyilvános IP-címekre. További információ: [Mi az az Azure Bastion?](../bastion/bastion-overview.md)


Ebből a cikkből megtudhatja, hogyan engedélyezheti a böngészőalapú kapcsolatokat a labor virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek 
- Helyezzen üzembe egy megerősített gazdagépet a meglévő labor virtuális hálózatában **(vagy)** a labort egy megerősített konfigurált virtuális hálózattal.
Ha meg szeretné tudni, hogyan helyezhet üzembe egy megerősített gazdagépet egy virtuális hálózaton, tekintse meg  [Az Azure Bastion-gazdagép létrehozása](../bastion/tutorial-create-host-portal.md)című témakört. A megerősített gazdagép létrehozásakor válassza ki a labor virtuális hálózatát. 
- A tesztkörnyezet felhasználójának **olvasó** szerepkörrel kell rendelkeznie a megerősített gazdagépen és a megerősített virtuális hálózaton. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Második alhálózat létrehozása a megerősített virtuális hálózaton
Először létre kell hoznia egy második alhálózatot a megerősített virtuális hálózatban, mivel a AzureBastionSubnet nem engedélyezi a nem megerősített erőforrások létrehozását. Hozzon létre egy másik alhálózatot a megerősített virtuális hálózaton belül az alábbi ábrán látható módon:

![Második alhálózat az Azure Bastion Virtual Networkben](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>VIRTUÁLIS gépek létrehozásának engedélyezése az alhálózatban
Most engedélyezze a virtuális gépek létrehozását ebben az alhálózatban a következő lépések végrehajtásával: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget. 
1. Válassza a **DevTest Labs** elemet a listából. 
1. A Labs listából válassza ki *a labort*. 

    > [!NOTE]
    > Az Azure Bastion mostantól általánosan elérhető a következő régiókban: USA nyugati régiója, USA keleti régiója, Nyugat-Európa, az USA déli középső régiója, Kelet-Ausztrália és Kelet-Japán. Ezért hozzon létre egy labort ezekben a régiókban, ha a labor egyike sincs bennük. 
    
1. A bal oldali menüben a **Beállítások** szakaszban válassza a **konfiguráció és szabályzatok** lehetőséget. 
1. Válassza a **virtuális hálózatok** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget az eszköztáron. 
1. Válassza ki azt a **virtuális hálózatot** , amelyen telepítve van a megerősített gazdagép. 
1. Válassza ki a virtuális gépek alhálózatát, ne pedig a **AzureBastionSubnet**, a másikat, amelyet korábban hozott létre. Zárjuk be a lapot, és nyissa meg újra, ha nem látja az alhálózatot a listában alul. 

    ![VIRTUÁLIS gépek létrehozásának engedélyezése az alhálózatban](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Válassza **a használat a virtuális gép létrehozásakor** lehetőséget. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 
1. Ha van egy régi virtuális hálózata a laborhoz, távolítsa el a **...*  elemre kattintva.  és **távolítsa el**. 

## <a name="enable-browser-connection"></a>Böngészőalapú kapcsolatok engedélyezése 

Miután megtörtént egy megerősített virtuális hálózat létrehozása a laborban, a tesztkörnyezet tulajdonosaként engedélyezheti a böngésző csatlakozási szolgáltatását a labor virtuális gépeken.

Az alábbi lépéseket követve engedélyezheti a böngésző kapcsolódását a labor virtuális gépeken:

1. A Azure Portal navigáljon a *laborhoz*.
1. Válassza **a konfiguráció és szabályzatok** lehetőséget.
1. A **Beállítások** területen válassza a **böngészőbeli kapcsolat** lehetőséget. Ha nem látja ezt a beállítást, akkor a **konfigurációs házirendek** lapon nyissa meg újra. 

    ![Böngészőalapú kapcsolatok engedélyezése](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Következő lépések
A következő cikkből megtudhatja, hogyan csatlakozhat a virtuális gépekhez egy böngésző használatával: [Kapcsolódás a virtuális gépekhez egy böngészőben](connect-virtual-machine-through-browser.md)