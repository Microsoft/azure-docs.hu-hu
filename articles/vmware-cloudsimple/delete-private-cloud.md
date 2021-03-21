---
title: Azure VMware-megoldás törlése a CloudSimple privát felhővel
description: Megtudhatja, hogyan törölhet CloudSimple saját felhőt. Ha töröl egy privát felhőt, az összes fürt törölve lesz.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896260"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő törlése

A CloudSimple rugalmasságot biztosít a privát felhő törléséhez.  A privát felhő egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Ha töröl egy privát felhőt, az összes fürt törölve lesz.

## <a name="before-you-begin"></a>Előkészületek

A privát felhő törlése törli a teljes privát felhőt.  A rendszer törli a privát felhő összes összetevőjét.  Ha meg szeretné őrizni az összes adatmennyiséget, győződjön meg róla, hogy biztonsági másolatot készített a helyszíni tárolóba vagy az Azure Storage szolgáltatásba.

A privát felhő összetevői a következők:

* CloudSimple-csomópontok
* Virtual machines (Virtuális gépek)
* Virtuális helyi hálózatok vagy alhálózatok
* A privát felhőben tárolt összes felhasználói érték
* Az összes tűzfalszabály melléklete egy VLAN/alhálózat számára

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-a-private-cloud"></a>Magánfelhő törlése

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot.

3. Kattintson a törölni kívánt privát felhőre

4. Az összefoglalás lapon kattintson a **Törlés** gombra.

    ![Privát felhő törlése](media/delete-private-cloud.png)

5. A jóváhagyás lapon adja meg a saját felhő nevét, majd kattintson a **Törlés** gombra. 

    ![Privát felhő törlése – megerősítés](media/delete-private-cloud-confirm.png)

A privát felhő törlésre van megjelölve.  A törlési folyamat három óra elteltével indul el, és törli a privát felhőt.

> [!CAUTION]
> A csomópontokat törölni kell a saját felhő törlése után.  A csomópontok mérése folytatódni fog, amíg a csomópontok törlődnek az előfizetésből.

## <a name="next-steps"></a>Következő lépések

* [Csomópontok törlése](delete-nodes.md)
