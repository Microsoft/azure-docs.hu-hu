---
title: Portok megnyitása virtuális géphez a Azure Portal használatával
description: Megtudhatja, hogyan nyithat meg egy portot/hozzon létre egy végpontot a virtuális géphez a Azure Portal használatával
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550450"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Portok megnyitása virtuális géphez a Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Keresse meg és válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Hozzáadás** lehetőséget, majd keresse meg és válassza ki a **hálózati biztonsági csoportot**.

1. Válassza a **Létrehozás** lehetőséget.

    Megnyílik a **hálózati biztonsági csoport létrehozása** ablak.

    ![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

1. Adja meg a hálózati biztonsági csoport nevét. 

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot, majd válasszon ki egy helyet.

1. Válassza a **Létrehozás** lehetőséget a hálózati biztonsági csoport létrehozásához.

## <a name="create-an-inbound-security-rule"></a>Bejövő biztonsági szabály létrehozása

1. Válassza ki az új hálózati biztonsági csoportot. 

1. A bal oldali menüben válassza a **bejövő biztonsági szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

    ![Váltás a speciális lapra](./media/nsg-quickstart-portal/advanced.png)

1. A **bejövő biztonsági szabály hozzáadása** lapon váltson át a **speciális** elemre az **alapszintű** oldalon a lap tetején. 

1. Válasszon egy közös **szolgáltatást** a legördülő menüből, például **http**. A **Custom (egyéni** ) lehetőséget is választhatja, ha a használni kívánt portot szeretné megadni. 

1. Szükség esetén módosíthatja a **prioritást** vagy a **nevet**. A prioritás befolyásolja a szabályok alkalmazásának sorrendjét: minél kisebb a numerikus érték, annál korábbi a szabály alkalmazása.

1. A szabály létrehozásához válassza a **Hozzáadás** lehetőséget.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Hálózati biztonsági csoport hozzárendelése alhálózattal

Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ebben a példában a hálózati biztonsági csoportot egy alhálózattal társítjuk. 

1. Válassza ki az **alhálózatok** elemet a bal oldali menüben, majd válassza a **hozzárendelés** lehetőséget.

1. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.

    ![Hálózati biztonsági csoport társítása virtuális hálózattal](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Ha elkészült, kattintson az **OK** gombra.

## <a name="additional-information"></a>További információ

[A cikkben ismertetett lépéseket a Azure PowerShell használatával is végrehajthatja](nsg-quickstart-powershell.md).

A cikkben ismertetett parancsok segítségével gyorsan lekérheti a virtuális gépre irányuló forgalmat. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. További információ: [hálózati forgalom szűrése hálózati biztonsági csoporttal](../../virtual-network/tutorial-filter-network-traffic.md).

A magasan elérhető webalkalmazások esetében érdemes lehet egy Azure Load Balancer mögött elhelyezni a virtuális gépeket. A terheléselosztó elosztja a forgalmat a virtuális gépekre egy olyan hálózati biztonsági csoporttal, amely forgalmi szűrést biztosít. További információkért lásd: [Windows rendszerű virtuális gépek terheléselosztása az Azure-ban egy magasan elérhető alkalmazás létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, létrehozott egy bejövő szabályt, amely engedélyezi a HTTP-forgalmat az 80-as porton, majd hozzárendeli a szabályt egy alhálózathoz. 

A részletesebb környezetek létrehozásáról a következő cikkekben talál információt:
- [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
- [Biztonsági csoportok](../../virtual-network/network-security-groups-overview.md)
