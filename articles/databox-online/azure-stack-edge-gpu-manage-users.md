---
title: Azure Stack Edge Pro GPU felhasználók kezelése | Microsoft Docs
description: Ismerteti, hogyan kezelheti a felhasználókat az Azure Stack Edge Pro GPU-val a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 5aeb4f2d7152120ea92a8df76f980eb3baed6e50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102638548"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>A Azure Portal használatával kezelheti a felhasználókat a Azure Stack Edge Pro-ban

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan kezelheti a felhasználókat a Azure Stack Edge Pro-ban. A Azure Stack Edge Pro-t a Azure Portal vagy a helyi webes felületen keresztül kezelheti. Az Azure Portalon felhasználókat vehet fel, módosíthat vagy törölhet.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználó hozzáadása
> * Felhasználó módosítása
> * Felhasználó törlése

## <a name="about-users"></a>Információk a felhasználókról

A felhasználók csak olvasási vagy teljes körű jogosultsággal rendelkezhetnek. Csak olvasási jogosultsággal rendelkező felhasználók tekinthetik meg a megosztási adatvédelmet. A teljes jogosultsággal rendelkező felhasználók megoszthatják az adatmegosztási és a megosztási írásokat, és módosíthatják vagy törölhetik a megosztási adatait.

 - **Teljes körű jogosultsággal rendelkező felhasználó** – Teljes körű hozzáféréssel rendelkező helyi felhasználó.
 - **Csak olvasási jogosultsággal rendelkező felhasználó** – Csak olvasási hozzáféréssel rendelkező helyi felhasználó. Ezek a felhasználók csak olvasási műveleteket megengedő megosztásokhoz vannak társítva.

A felhasználói engedélyek első alkalommal a megosztás létrehozása során, a felhasználó létrehozásakor lesznek meghatározva. A fájlkezelő használatával módosíthatók.


## <a name="add-a-user"></a>Felhasználó hozzáadása

Felhasználó hozzáadásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza a **felhasználók** lehetőséget. Válassza a **+ felhasználó hozzáadása** elemet a parancssorban.

    ![Válassza a felhasználó hozzáadása elemet.](media/azure-stack-edge-gpu-manage-users/add-user-1.png)

2. Adja meg a hozzáadni kívánt felhasználó felhasználónevét és jelszavát. Erősítse meg a jelszót, és válassza a **Hozzáadás** lehetőséget.

    ![Felhasználónév és jelszó megadása](media/azure-stack-edge-gpu-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > A következő rendszer által lefoglalt felhasználók nem használhatóak: Rendszergazda, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Vendég.  

3. Egy értesítés jelenik meg, amikor a felhasználó létrehozása megkezdődik és befejeződött. A felhasználó létrehozása után a parancssorból válassza a **frissítés** lehetőséget a felhasználók frissített listájának megtekintéséhez.


## <a name="modify-user"></a>Felhasználó módosítása

A felhasználó létrehozását követően lehetőség van a hozzá tartozó jelszó módosítására. Válasszon a felhasználók listájából. Adja meg és erősítse meg az új jelszót. Mentse a módosításokat.

![Felhasználó módosítása](media/azure-stack-edge-gpu-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törléséhez hajtsa végre a következő lépéseket az Azure Portalon.


1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza a **felhasználók** lehetőséget.

    ![Válassza ki a törölni kívánt felhasználót](media/azure-stack-edge-gpu-manage-users/delete-user-1.png)

2. Válasszon ki egy felhasználót a felhasználók listájából, majd válassza a **Törlés** lehetőséget. A rendszer kérésére erősítse meg a törlést.

    ![Válassza ki a törölni kívánt felhasználót 2](media/azure-stack-edge-gpu-manage-users/delete-user-2.png)

A törlés végrehajtása után annak megfelelően frissül a felhasználók listája.

![A felhasználók frissített listája](media/azure-stack-edge-gpu-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Következő lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](azure-stack-edge-gpu-manage-bandwidth-schedules.md) talál.
