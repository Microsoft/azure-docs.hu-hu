---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban egy tesztkörnyezetfiókot állíthat be az Azure Lab Services szolgáltatással.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: 704ad915616e4f860204783462269ec68a6e4e28
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730638"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Az Azure Lab Services tesztkörnyezetfiók beállítása
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebben az oktatóanyagban megismerheti, hogyan hozhat létre tesztkörnyezetfiókot tesztkörnyezet-rendszergazdaként. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
> * A tesztkörnyezet-tulajdonosok számára elérhető rendszerképek megadása a Marketplace-en

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben. Válassza ki **Lab Services** a a **fejlesztési és üzemeltetési** szakaszban. Ha bejelöli a csillag (`*`) melletti **Lab Services**, megjelenik a **Kedvencek** szakaszban a bal oldali menüben. És újabb verziók esetében a következő időpont választja **Lab Services** alatt **Kedvencek**.

    ![A Lab Services összes szolgáltatások ->](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Az a **Lab Services** lapon jelölje be **Hozzáadás** az eszköztáron. 

    ![A Lab-fiókokban oldalon válassza a Hozzáadás](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Az a **labor fiók** lapon, tegye a következőket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Válasszon egy meglévő **megosztott lemezkép-katalógusában** vagy hozzon létre egyet. A Virtuálisgép-sablon mentheti a megosztott rendszerkép-katalógusában ahhoz, hogy a mások által felhasználható. Megosztott kép katalógusok részletes információkért lásd: [használata az Azure Lab Services egy megosztott képgyűjtemény](how-to-use-shared-image-gallery.md). 
    6. A **társ virtuális hálózatnak**, válassza ki a társ virtuális hálózatnak (VNet), a tesztlabor-hálózat. Ehhez a fiókhoz a létrehozott Labs a kiválasztott virtuális hálózat csatlakozik, és rendelkezik az erőforrásokhoz való hozzáférést a kiválasztott virtuális hálózat. 
    7. Az a **engedélyezése tesztkörnyezet létrehozója, labor helyre** mezőben adja meg, hogy tesztlabor kiindulópontként szolgálhat ki egy helyet a tesztkörnyezethez. Alapértelmezés szerint a beállítás le van tiltva. Le van tiltva, ha a labor alkotói nem adható meg egy helyet a tesztkörnyezethez hoznak létre. A labs labor-fiókba az Önhöz legközelebbi földrajzi helyet jönnek létre. Ha engedélyezve van, egy tesztkörnyezet létrehozója lab létrehozása idején válasszon egy helyet is. 
    8. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Válassza ki a **harang ikonra** eszköztár (**értesítések**), győződjön meg arról, hogy az üzembe helyezés sikeres volt-e, és válassza ki **erőforrás megnyitása**. 

    Jelölje ki **frissítése** a a **Tesztkörnyezetfiókok** lapon, és válassza ki a létrehozott tesztkörnyezet-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. Az a **labor fiók** lapon jelölje be **hozzáférés-vezérlés (IAM)**, jelölje be **+ Hozzáadás** elemre az eszköztárban, majd válassza ki **+ szerepkör-hozzárendelés hozzáadása** a a eszköztár. 

    ![Hozzáférés-vezérlés -> szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Az a **szerepkör-hozzárendelés hozzáadása** lapon jelölje be **tesztkörnyezet létrehozója** a **szerepkör**, válassza ki a felhasználót, adja hozzá a labor létrehozó szerepkörhöz, és válassza ki a kívánt **mentése**. 

    ![Tesztkörnyezet létrehozója hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Adja meg a Marketplace-rendszerképek labor létrehozói számára elérhető
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

1. Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 
    
    ![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
        
    - Egyetlen virtuális gépet hoznak létre.
    - A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
    - Nincs szükség külön licenccsomag vásárlására.
2. Egy engedélyezett Marketplace-beli rendszerkép **letiltásának** a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép letiltása** lehetőségre. 

        ![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek letiltása** lehetőségre. 

        ![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. A letiltott Marketplace-beli rendszerképek **engedélyezésének** a fentiekhez hasonlóan a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép engedélyezése** lehetőségre. 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek engedélyezése** lehetőségre. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha meg szeretné tudni, tanárként hogyan hozhat létre osztályterem-tesztkörnyezetet, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

