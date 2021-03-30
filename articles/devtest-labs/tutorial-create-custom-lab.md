---
title: Tesztkörnyezet létrehozása az Azure DevTest Labs szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban egy labort hoz létre Azure DevTest Labs a Azure Portal használatával. A tesztkörnyezet rendszergazdája beállítja a labort, virtuális gépeket hoz létre a laborban, és konfigurálja a házirendeket.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 4b59edd4ab1204d6683cf95a02070d10b1abe061
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324280"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Oktatóanyag: Tesztkörnyezet létrehozása az Azure DevTest Labs szolgáltatással
Ebben az oktatóanyagban létrehoz egy tesztkörnyezetet az Azure Portalon. A tesztkörnyezet rendszergazdája létrehozza a tesztkörnyezetet a vállalaton belül és a virtuális gépeket a tesztkörnyezetben, továbbá konfigurálja a szabályzatokat. A tesztkörnyezet felhasználói (például a fejlesztők és a tesztelők) lefoglalják a tesztkörnyezet virtuális gépeit, csatlakoznak azokhoz, és használják azokat. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Labor létrehozása
> * Virtuális gépek hozzáadása a tesztkörnyezethez
> * Felhasználó hozzáadása a tesztkörnyezet-felhasználó szerepkörhöz

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-lab"></a>Labor létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali főmenüben kattintson az **Erőforrás létrehozása** elemre (a lista elején), mutasson a **Fejlesztői eszközök** pontra, végül kattintson a **DevTest Labs** lehetőségre. 

    ![Új DevTest Labs-tesztkörnyezet menü](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. A **DevTest Labs-tesztkörnyezet létrehozása** ablakban tegye a következőket: 
    1. A **Tesztkörnyezet neve** mezőbe írja be a tesztkörnyezet nevét. 
    2. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyikben a tesztkörnyezetet létre szeretné hozni. 
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak. 
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetet. 
    5. Válassza a **Létrehozás** lehetőséget. 
    6. Válassza a **Rögzítés az irányítópulton** lehetőséget. A létrehozott tesztkörnyezet megjelenik az irányítópulton. 

        ![A DevTest Labs labor szakaszának létrehozása](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. Ellenőrizze, hogy a labor létrehozása sikeres volt-e az értesítések megtekintésével. Válassza az **Erőforrás megnyitása** lehetőséget.  

    ![Értesítés](./media/tutorial-create-custom-lab/creation-notification.png)
3. Ellenőrizze, hogy megjelenik-e a labor **DevTest-labor** lapja. 

    ![A labor kezdőlapja](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Virtuális gép hozzáadása a tesztkörnyezethez

1. A **DevTest Labs-tesztkörnyezet** lapon kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Hozzáadás gomb](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Az **Alap kiválasztása** lapon keressen egy kulcsszóra (például Windows vagy Ubuntu), majd válasszon ki egy alaprendszerképet a listából. 
1. A **Virtuális gép** oldalon tegye a következőket: 
    1. A **Virtuális gép neve** mezőbe írja be a virtuális gép nevét. 
    2. A **Felhasználónév** mezőbe írja be a felhasználó nevét, aki hozzáférhet a virtuális géphez. 
    3. A **Password** (Jelszó) mezőben adja meg a felhasználónévhez tartozó jelszót. 

        ![Képernyőfelvétel: a "labor erőforrás létrehozása" lap alapszintű beállításainak megjelenítése.](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Válassza a **Speciális beállítások** fület.
    1. **A gép legyen lefoglalható** beállításnál válassza az **Igen** lehetőséget.
    2. Győződjön meg arról, hogy a **példányszám** értéke **1**. Ha az érték **2**, két virtuális gép jön létre a következő nevekkel: `<base image name>00' and <base image name>01`. Például: `win10vm00` és `win10vm01`.     
    3. Válassza a **Küldés** lehetőséget. 

        ![Alap kiválasztása](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. A **Lefoglalható virtuális gépek** listában megtekintheti a virtuális gépek állapotát. A virtuális gépek létrehozása körülbelül 25 percet vesz igénybe. A virtuális gép létrehozása egy különálló Azure-erőforráscsoportban történik, amelynek neve az aktuális erőforráscsoport nevével kezdődik, amelyben a tesztkörnyezet található. Egy példa: ha a tesztkörnyezet a `labrg` erőforráscsoportban található, a virtuális gép a `labrg3988722144002` nevű erőforráscsoportban jön létre. 

        ![Virtuális gép létrehozásának állapota](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. A létrehozott virtuális gép megjelenik a **Lefoglalható virtuális gépek** listában. 

    > [!NOTE] 
    > A **Speciális beállítások** lapon konfigurálhatja a virtuális gép nyilvános, magánhálózati vagy megosztott IP-címét. Ha a **megosztott IP-cím** engedélyezve van, a Azure DevTest Labs automatikusan engedélyezi a Windows rendszerű virtuális gépek és az SSH használatát a Linux rendszerű virtuális gépekhez. Ha **nyilvános IP-** címmel rendelkező virtuális gépeket hoz létre, az RDP és az SSH a DevTest Labs módosítása nélkül is engedélyezve van.  

## <a name="add-a-user-to-the-lab-user-role"></a>Felhasználó hozzáadása a tesztkörnyezet-felhasználó szerepkörhöz

1. A bal oldali menüben válassza a **Konfigurálás és szabályzatok** elemet. 

    ![Konfigurálás és szabályzatok](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a menüben, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron. 

    ![Szerepkör-hozzárendelés hozzáadása – gomb](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Az **Engedélyek hozzáadása** oldalon tegye a következőket:
    1. A **Szerepkör** esetében válassza a **DevTest Labs-felhasználó** lehetőséget. 
    2. Válassza ki a hozzáadni kívánt **felhasználót**. 
    3. Kattintson a **Mentés** gombra.

        ![Felhasználó hozzáadása](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A következő oktatóanyag azt mutatja be, hogyan foglalhatják le a tesztkörnyezet-felhasználók a tesztkörnyezet virtuális gépeit, és hogyan csatlakozhatnak azokhoz. Ha nem szeretné végrehajtani ezt az oktatóanyagot, és törölni szeretné a jelen oktatóanyag során létrehozott erőforrásokat, tegye a következőket: 

1. Az Azure Portalon válassza az **Erőforráscsoportok** elemet. 

    ![Erőforráscsoportok](./media/tutorial-create-custom-lab/resource-groups.png)
1. Válassza ki azt az erőforráscsoportot, amelyben létrehozta a tesztkörnyezetet. 
1. Válassza az **Erőforráscsoport törlése** elemet az eszköztáron. Az erőforráscsoport törlése a csoportba tartozó összes erőforrást törli, beleértve a tesztkörnyezetet is. 

    ![Laboratóriumi erőforráscsoport](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Ismételje meg ezeket a lépéseket a `<your resource group name><random numbers>` nevű, az Ön számára létrehozott további erőforráscsoport esetében. Példa: `splab3988722144001`. A virtuális gépek ebben az erőforráscsoportban jönnek létre, és nem abban, amelyben a tesztkörnyezet található. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetet egy virtuális géppel, és egy felhasználónak hozzáférést biztosított a tesztkörnyezethez. Ha meg szeretné tudni, tesztkörnyezet-felhasználóként hogyan érheti el a tesztkörnyezetet, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés a tesztkörnyezethez](tutorial-use-custom-lab.md)

