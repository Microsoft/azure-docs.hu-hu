---
title: Tesztkörnyezet elérése az Azure DevTest Labs szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban megtanul hozzáférni az Azure DevTest Labs szolgáltatással létrehozott tesztkörnyezethez, továbbá megtudhatja, hogyan foglalhatja le és használhatja a virtuális gépeket, illetve hogyan oldhatja fel a lefoglalást.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: b4477e0b98ef534b8170ee297edf88ac6fa62dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476444"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Oktatóanyag: Tesztkörnyezet elérése az Azure DevTest Labs szolgáltatásban
Ebben az oktatóanyagban az [Oktatóanyag: Tesztkörnyezet létrehozása az Azure DevTest Labs szolgáltatásban](tutorial-create-custom-lab.md) című szakaszban létrehozott tesztkörnyezetet használatát ismerheti meg.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Virtuális gép (VM) lefoglalása a tesztkörnyezetben
> * Kapcsolódás a virtuális géphez
> * A virtuális gép lefoglalásának feloldása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="access-the-lab"></a>Hozzáférés a tesztkörnyezethez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali menü **Összes erőforrás** elemét. 
3. Válassza ki a **DevTest Labs** erőforrástípust. 
4. Válassza ki a tesztkörnyezetet. 

    ![A tesztkörnyezet kiválasztása](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Virtuális gép lefoglalása

1. A **Lefoglalható virtuális gépek** listában válassza a következőket: **...** (három pont), majd **Gép lefoglalása**.

    ![Virtuális gép lefoglalása](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Ellenőrizze, hogy a virtuális gép szerepel-e a **Saját virtuális gépek** listájában.

    ![Saját virtuális gép](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. Válassza ki a virtuális gépét a listából. Megjelenik a virtuális géphez tartozó **Virtuális gép oldal**. Válassza az eszköztár **Csatlakozás** elemét.

    ![Csatlakozás virtuális géphez](./media/tutorial-use-custom-lab/connect-button.png)
2. Mentse a letöltött **RDP**-fájlt a merevlemezre, majd a használatával csatlakozzon a virtuális géphez. Adja meg a felhasználónevet és jelszót, amelyeket a virtuális gép létrehozásakor határozott meg. 

    Linux rendszerű virtuális géphez való csatlakozáshoz a virtuális gép esetében engedélyezni kell az SSH- vagy az RDP-hozzáférést. A Linux rendszerű virtuális gépekhez történő RDP-csatlakozással kapcsolatban lásd: [Távoli asztal telepítése és konfigurálása Azure-beli Linux virtuális géphez való csatlakozáshoz](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > A virtuális gép virtuálisgép-lapjára más módokon is eljuthat. Íme néhány közülük: 
    > 
    > 1. Keresse meg az előfizetésben található összes virtuális gépet. Válassza ki a **virtuális gépet a virtuális gépek** listájából, hogy a virtuális gép oldalra kerüljön.
    > 2. Navigáljon az **erőforráscsoport oldalához** . Ezután válassza ki a virtuális gépet az erőforráscsoport erőforrásainak listájából, hogy beolvassa a **virtuális gép** lapját. 
    >
    > Ne használja a **Kapcsolódás** gombot azon az eszköztáron, amelyet a **virtuális gép** oldalán érhet el, és használja ezeket a beállításokat. Ehelyett keresse meg a **virtuális gép** lapot az **DevTest Labs** lapon a jelen cikkben látható módon, majd használja a **Kapcsolódás** gombot az eszköztáron.


## <a name="unclaim-the-vm"></a>A virtuális gép lefoglalásának feloldása
Ha már nem használja a virtuális gépet, az alábbi lépéseket követve oldja fel azt: 

1. A virtuális gép oldalán válassza ki az eszköztár **Feloldás** elemét. 

    ![A virtuális gép lefoglalásának feloldása](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A virtuális gép a lefoglalás feloldása előtt leáll. A művelet állapota az értesítésekben látható.  
3. Váltson vissza a DevTest Lab oldalára úgy, hogy a felső navigációs menüben a labor nevére kattint. 
    
    ![Navigáljon vissza a laborba](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Ellenőrizze, hogy a virtuális gép megjelenik-e a **jogcímek** listájának alján.

    
## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megismerkedett az Azure DevTest Labs használatával létrehozott tesztkörnyezet elérésének és használatának módjával. A tesztkörnyezetben lévő virtuális gépekhez való hozzáféréssel és azok használatával kapcsolatos további információkért tekintse meg a következőt: 

> [!div class="nextstepaction"]
> [Útmutató: Virtuális gépek használata tesztkörnyezetben](devtest-lab-add-vm.md)

