---
title: Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer-Azure Portal
description: Megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer a Azure Portal használatával.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91439525"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Egy meglévő standard SKU-terheléselosztó abban az előfizetésben, amelyben a virtuálisgép-méretezési csoport telepítve lesz.
- Egy Azure-Virtual Network a virtuálisgép-méretezési csoport számára.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési csoport üzembe helyezése meglévő Load balancerrel

Ebben a szakaszban egy virtuálisgép-méretezési készletet hoz létre a Azure Portal egy meglévő Azure Load balancerrel.

> [!NOTE]
> Az alábbi lépések feltételezik, hogy egy **myVNet** nevű virtuális hálózat és egy **myLoadBalancer** nevű Azure Load Balancer korábban telepítve lett.

1. A képernyő bal felső részén kattintson az **erőforrás létrehozása**  >  **számítási**  >  **virtuálisgép-méretezési csoport** vagy a **virtuálisgép-méretezési csoport** keresése lehetőségre a piactéren.

2. Válassza a **Létrehozás** lehetőséget.

3. A **virtuálisgép-méretezési csoport létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás                        | Érték                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Projekt részletei**            |                                                                                                       |
    | Előfizetés                   | Válassza ki az Azure-előfizetését                                                                        |
    | Erőforráscsoport                 | Válassza az új létrehozása elemet, írja be a **myResourceGroup**, majd kattintson az OK gombra, vagy válasszon ki egy meglévő erőforráscsoportot. |
    | **Méretezési csoport részletei**          |                                                                                                       |
    | A virtuálisgép-méretezési csoport neve | **MyVMSS** megadása                                                                                      |
    | Region                         | Válassza az **USA 2. keleti** régióját                                                                                    |
    | A rendelkezésre állási zóna              | Válassza a **nincs** lehetőséget                                                                                       |
    | **Példány adatai**           |                                                                                                       |
    | Kép                          | Az **Ubuntu Server 18,04 LTS** kiválasztása                                                                    |
    | Azure Spot-példány            | Válassza a **nem** lehetőséget                                                                                         |
    | Méret                           | Alapértelmezésben hagyja                                                                                      |
    | **Rendszergazdai fiók**      |                                                                                                       |
    | Hitelesítéstípus            | **Jelszó** kiválasztása                                                                                   |
    | Felhasználónév                       | Adja meg a rendszergazdai felhasználónevét        |
    | Jelszó                       | Adja meg a rendszergazdai jelszavát    |
    | Jelszó megerősítése               | Adja meg újra a rendszergazdai jelszót |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="A képernyőfelvételen a virtuálisgép-méretezési csoport alapjai lap jelenik meg." border="true":::

4. Válassza a **Hálózatkezelés** lapot.

5. Adja meg vagy válassza ki ezt az információt a **hálózatkezelés** lapon:

     Beállítás                           | Érték                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtuális hálózati konfiguráció** |                                                          |
    | Virtuális hálózat                   | Válassza a **myVNet** vagy a meglévő virtuális hálózat lehetőséget.      |
    | **Terheléselosztás**                |                                                          |
    | Terheléselosztó használata               | Válassza az **Igen** lehetőséget.                                           |
    | **Terheléselosztási beállítások**       |                                                          |
    | Terheléselosztási beállítások            | **Azure Load Balancer** kiválasztása                           |
    | Terheléselosztó kiválasztása            | Válassza ki a **myLoadBalancer** vagy a meglévő Load balancert |
    | Háttérbeli készlet kiválasztása             | Válassza ki a **myBackendPool** vagy a meglévő háttér-készletet.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="A képernyőfelvételen a virtuálisgép-méretezési csoport létrehozása lap látható." border="true":::

6. Válassza a **felügyelet** fület.

7. A **felügyelet** lapon állítsa be a **rendszerindítási diagnosztika** beállítást **off** értékre.

8. Kattintson a kék **Áttekintés + létrehozás** gombra.

9. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuálisgép-méretezési csoport üzembe helyezését egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési csoportokról és a Load balancerről további információt a következő témakörben talál:

- [Mi az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
