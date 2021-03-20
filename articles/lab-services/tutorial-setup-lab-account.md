---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be labor-fiókot Azure Lab Serviceskal, hozzáadhat egy labor-létrehozót, és megadhatja a Labs által a labor-fiókban használandó Piactéri lemezképeket.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445066"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre labor-fiókot. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza ki a **DevOps** **kategóriából**. Ezután válassza a **labor Services** elemet. Ha a Star () lehetőséget választja a `*` **labor Services** mellett, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **Lab szolgáltatások** lehetőséget a **Kedvencek** alatt.

    ![Minden szolgáltatás – > labor Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **labor Services** lapon kattintson a **Hozzáadás** elemre az eszköztáron, vagy válassza a **labor-fiók létrehozása** gombot az oldalon. 

    ![Válassza a Hozzáadás lehetőséget a labor-fiókok oldalon](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **labor-fiók létrehozása** lap **alapok** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.
    4. A **hely** mezőben válasszon ki egy helyet vagy régiót, amelyben létre kívánja hozni a labor-fiókot. 

        ![Lab-fiók – alapismeretek lap](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Válassza az **Áttekintés + létrehozás** lehetőséget.
    6. Tekintse át az összegzést, és válassza a **Létrehozás** lehetőséget. 

        ![Felülvizsgálat + létrehozás – > létrehozás](./media/tutorial-setup-lab-account/create-button.png)    
5. Ha a telepítés befejeződött, bontsa ki a **következő lépések** csomópontot, és válassza az **Ugrás az erőforráshoz** lehetőséget. 

    ![Ugrás a labor-fiók oldalra](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Ellenőrizze, hogy megjelenik-e a **labor-fiók** lap. 

    ![Tesztkörnyezetfiók-oldal](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. Ahhoz, hogy a pedagógusok számára engedélyt adjon a Labs létrehozásához az osztályaik számára, vegye fel őket a **labor létrehozói** szerepkörbe:

> [!NOTE]
> A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha azt tervezi, hogy ugyanazt a felhasználói fiókot használja egy osztályterem labor létrehozásához ebben az oktatóanyagban, hagyja ki ezt a lépést. 

1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés** lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha szeretne többet megtudni arról, hogyan hozhat létre tantermi labort oktatóként, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

