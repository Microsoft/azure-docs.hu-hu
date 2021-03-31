---
title: Azure-beli virtuális gép vész-helyreállításának beállítása másodlagos régióba Azure Site Recovery
description: A Azure Site Recovery szolgáltatás használatával gyorsan állíthatja be a vész-helyreállítást egy másik Azure-régióba egy Azure-beli virtuális gépen.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135707"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Rövid útmutató: vész-helyreállítás beállítása egy másodlagos Azure-régióba Azure-beli virtuális gépen

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás az üzletmenet-folytonossági és a vész-helyreállítási (BCDR-) stratégiához járul hozzá az üzleti alkalmazások tervezett és nem tervezett leállások esetén történő online megőrzéséhez. Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek (VM) vész-helyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez a rövid útmutató azt ismerteti, hogyan állítható be a vész-helyreállítás egy Azure-beli virtuális gépen egy másodlagos Azure-régióba való replikálással. Az alapértelmezett beállítások általában a replikálás engedélyezésére szolgálnak.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre és egy virtuális gépre.

- Ha nem rendelkezik aktív előfizetéssel rendelkező Azure-fiókkal, akkor [ingyenesen létrehozhat egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Legalább 1 GB RAM memóriával rendelkező virtuális gép használata javasolt. [További](../virtual-machines/windows/quick-create-portal.md) információ a virtuális gépek létrehozásáról.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Azure-beli virtuális gép replikálásának engedélyezése

A következő lépésekkel engedélyezheti a virtuális gépek replikálását egy másodlagos helyre.

1. A Azure Portal a **Kezdőlap**  >  **virtuális gépek** menüjében válasszon ki egy virtuális gépet a replikáláshoz.
1. A **műveletek** területen válassza a vész- **helyreállítás** lehetőséget.
1. Az **alapvető**  >  **célkitűzések** területen válassza ki a kívánt régiót.
1. A replikálási beállítások megtekintéséhez válassza a **felülvizsgálat + replikáció indítása** lehetőséget. Ha módosítania kell az alapértelmezett beállításokat, válassza a **Speciális beállítások** lehetőséget.
1. A virtuális gépek replikálását engedélyező feladatok elindításához válassza a **replikáció indítása** lehetőséget.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Replikáció engedélyezése.":::

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikációs feladatok befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikálási beállításokat, és tesztelheti a telepítést.

1. A Azure Portal menüben válassza a **virtuális gépek** lehetőséget, és válassza ki a replikált virtuális gépet.
1. A **műveletek** területen válassza a vész- **helyreállítás** lehetőséget.
1. A replikálás részleteit az **Áttekintés** Select Essentials ( **alapismeretek**) lapján tekintheti meg. További részletek az állapot és az **állapot**, a **feladatátvételi készültség** és az infrastruktúra- **nézet** térképe látható.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replikálás állapota.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha le szeretné állítani a virtuális gép replikálását az elsődleges régióban, le kell tiltania a replikációt:

- A forrás replikálási beállításai automatikusan törlődnek.
- A virtuális gépen a replikálás során telepített Site Recovery bővítmény nem törlődik.
- A virtuális gép Site Recovery számlázása leáll.

A replikáció letiltásához hajtsa végre a következő lépéseket:

1. A Azure Portal menüben válassza a **virtuális gépek** lehetőséget, és válassza ki a replikált virtuális gépet.
1. A **műveletek** területen válassza a vész- **helyreállítás** lehetőséget.
1. Az **Áttekintés** területen válassza a **replikáció letiltása** lehetőséget.
1. A site Recovery bővítmény eltávolításához nyissa meg a virtuális gép **Beállítások**  >  **bővítményeit**.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Tiltsa le a replikációt.":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy virtuális gépet replikált egy másodlagos régióba. Ezután állítson be több Azure-beli virtuális gép replikálását.

> [!div class="nextstepaction"]
> [Vész-helyreállítás beállítása Azure-beli virtuális gépekhez](azure-to-azure-tutorial-enable-replication.md)
