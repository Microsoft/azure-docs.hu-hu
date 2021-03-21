---
title: Oktatóanyag – NSX-T hálózati szegmens hozzáadása az Azure VMware-megoldásban
description: Megtudhatja, hogyan hozhat létre NSX-T hálózati szegmenst a virtuális gépekhez a vCenter-ben.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462123"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Oktatóanyag: hálózati szegmens hozzáadása az Azure VMware megoldásban 

A vCenter-ben létrehozott virtuális gépek (VM-EK) az NSX-T-ben létrehozott hálózati szegmensekre kerülnek, és a vCenter láthatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati szegmensek hozzáadásához navigáljon a NSX-T Managerben
> * Új hálózati szegmens hozzáadása
> * Figyelje meg az új hálózati szegmenst a vCenter-ben

## <a name="prerequisites"></a>Előfeltételek

Egy Azure VMware-megoldás saját felhője, amely hozzáféréssel rendelkezik a vCenter és a NSX-T Manager felületekhez. További információt a [hálózatkezelés konfigurálása](tutorial-configure-networking.md) oktatóanyagban talál.

## <a name="add-a-network-segment"></a>Hálózati szegmens hozzáadása

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy NSX-T hálózati szegmenst, amelyet a vCenter-ben lévő virtuális gépekhez használhat. 

A következő ismereteket sajátította el: 

- [DHCP létrehozása és kezelése az Azure VMware-megoldáshoz](manage-dhcp.md)
- [Tartalom-függvénytár létrehozása virtuális gépek üzembe helyezéséhez az Azure VMware-megoldásban](deploy-vm-content-library.md) 
- [Helyszíni helyi környezetek saját felhőhöz](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
