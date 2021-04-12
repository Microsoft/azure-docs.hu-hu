---
title: A Windows rendszerű virtuális asztali portál alkalmazás-csoportjainak kezelése – Azure
description: Windows rendszerű virtuális asztali alkalmazás-csoportok kezelése a Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ca13cba07230a7bf606a398e28761aced14857
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446009"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Oktatóanyag: alkalmazás-csoportok kezelése a Azure Portal

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Az új Windows rendszerű virtuális asztali készlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes Start menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

Ha már létrehozott egy gazdagép-készletet és egy munkamenet-gazdagépet a Azure Portal vagy a PowerShell használatával, az alábbi eljárással adhat hozzá alkalmazás-csoportokat a Azure Portalból:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
   
    >[!NOTE]
    > Ha bejelentkezik a US Gov portálra, ugorjon a [https://portal.azure.us/](https://portal.azure.us/) helyet.

2.  Keresse meg és válassza ki a **Windows virtuális asztal** elemet.

3. Hozzáadhat közvetlenül egy alkalmazás-csoportot, vagy hozzáadhatja egy meglévő gazdagép-készletből is. Válasszon az alábbi lehetőségek közül:

    - Az oldal bal oldalán található menüben válassza ki az **alkalmazáscsoport** elemet, majd válassza a **+ Hozzáadás** lehetőséget.

    - Válassza a **gazdagépek** lehetőséget a képernyő bal oldalán, válassza ki a gazdagép nevét, válassza ki az **alkalmazáscsoport** elemet a bal oldali menüben, majd válassza a **+ Hozzáadás** lehetőséget. Ebben az esetben a gazdagép-készlet már ki lesz választva az alapok lapon.

4. Az **alapvető beállítások** lapon válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyhez létre kívánja hozni az alkalmazás-csoportot. Azt is megteheti, hogy új erőforráscsoportot hoz létre, és nem választ ki egy meglévőt.

5. Válassza ki azt a **gazdagépet** , amely a legördülő menüből az alkalmazáscsoport társítva lesz.

    >[!NOTE]
    >Ki kell választania az alkalmazáscsoport társított készletét. Az alkalmazáscsoport olyan alkalmazásokkal vagy asztali számítógépekkel rendelkezik, amelyek egy munkamenet-gazdagépről származnak, és a munkamenet-gazdagépek a gazdagépek részét képezik. A létrehozás során az alkalmazás csoportjának társítania kell egy gazdagép-készletet.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Azure Portal alapjai lapról.](media/basics-tab.png)

6. Válassza ki a **RemoteApp** elemet az **alkalmazáscsoport típusa** területen, majd adja meg a RemoteApp nevét.

      > [!div class="mx-imgBorder"]
      > ![Az alkalmazáscsoport típusú mezők képernyőképe A "RemoteApp" ki van emelve.](media/remoteapp-button.png)

7.  Válassza a **Tovább: hozzárendelések >** lapot.

8.  Ha egyéni felhasználókat vagy felhasználói csoportokat szeretne hozzárendelni az alkalmazás csoporthoz, válassza az **+ Azure ad-felhasználók vagy felhasználói csoportok hozzáadása** elemet.

9.  Válassza ki azokat a felhasználókat, akik számára hozzáférést szeretne használni az alkalmazásokhoz. Kiválaszthat egy vagy több felhasználót és felhasználói csoportot is.

     > [!div class="mx-imgBorder"]
     > ![A felhasználó kiválasztási menüjének képernyőképe.](media/select-users.png)

10.  Válassza a **Kiválasztás** lehetőséget.

11.  Válassza a **Tovább: alkalmazások >**, majd az **+ Alkalmazások hozzáadása** lehetőséget.

12.  Alkalmazás hozzáadása a Start menüből:

      - Az **alkalmazás forrása** területen válassza a **Start menü** lehetőséget a legördülő menüből. Ezután az **alkalmazás** alatt válassza ki az alkalmazást a legördülő menüből.

     > [!div class="mx-imgBorder"]
     > ![Képernyőkép az alkalmazás hozzáadása képernyőről a Start menü kiválasztásával.](media/add-app-start.png)

      - A **megjelenítendő név** mezőbe írja be annak az alkalmazásnak a nevét, amely megjelenik a felhasználó számára az ügyfélen.

      - Hagyja meg a többi beállítást, és válassza a **Mentés** lehetőséget.

13.  Alkalmazás hozzáadása egy adott fájl elérési útjáról:

      - Az **alkalmazás forrása** területen válassza a **fájl elérési útja** elemet a legördülő menüből.

      - Az **alkalmazás elérési útja** mezőben adja meg az alkalmazás elérési útját a társított gazdagép-készletben regisztrált munkamenet-gazdagépen.

      - Adja meg az alkalmazás adatait az **alkalmazás neve**, a **megjelenítendő név**, az **ikon elérési útja** és az **ikon index** mezőiben.

      - Kattintson a **Mentés** gombra.

     > [!div class="mx-imgBorder"]
     > ![Az alkalmazás hozzáadása oldal képernyőképe a kijelölt fájl elérési útjával.](media/add-app-file.png)

14.  Ismételje meg ezt a folyamatot minden olyan alkalmazás esetében, amelyet hozzá szeretne adni az alkalmazás csoportjához.

15.  Ezután válassza a **Tovább: munkaterület >** elemet.

16.  Ha egy munkaterületre szeretné regisztrálni az erőforráscsoportot, válassza az **Igen** lehetőséget az **alkalmazáscsoport regisztrálása** lehetőségnél. Ha inkább egy későbbi időpontban regisztrálja az alkalmazást, válassza a **nem** lehetőséget.

17.  Ha az **Igen** lehetőséget választja, kiválaszthat egy meglévő munkaterületet, amellyel regisztrálhatja az alkalmazás csoportját.

       >[!NOTE]
       >Az alkalmazáscsoport csak abban a helyen létrehozott munkaterületekre regisztrálható, ahol a gazdagép található. Is. Ha korábban már regisztrált egy másik alkalmazást ugyanabból a készletből, mint az új alkalmazáscsoport egy munkaterületre, akkor azt a rendszer kijelöli, és nem szerkesztheti. Egy adott alkalmazáskészletben lévő összes alkalmazás-csoportot regisztrálni kell ugyanahhoz a munkaterülethez.

     > [!div class="mx-imgBorder"]
     > ![Egy már meglévő munkaterület alkalmazás-csoportjának regisztrálása lapjának képernyőképe. Az alkalmazáskészletet a rendszer előjelöli.](media/register-existing.png)

18.  Ha címkéket szeretne létrehozni a munkaterület rendszerezéséhez, válassza a **tovább lehetőséget: címkék >** és adja meg a címke nevét.

19.  Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

20.  Várjon egy kicsit, hogy az érvényesítési folyamat befejeződjön. Ha elkészült, válassza a **Létrehozás** lehetőséget az alkalmazás csoportjának telepítéséhez.

Az üzembe helyezési folyamat a következő műveleteket végzi el:

- Hozza létre a RemoteApp-alkalmazás csoportot.
- Adja hozzá a kiválasztott alkalmazásokat az alkalmazás csoportjához.
- Tegye közzé az alkalmazáscsoport közzétételét a kiválasztott felhasználók és felhasználói csoportok számára.
- Ha ezt a lehetőséget választja, regisztrálja az alkalmazás csoportját.
- Hozzon létre egy Azure Resource Manager-sablonra mutató hivatkozást, amely a később letölthető és menthető konfiguráció alapján érhető el.

>[!IMPORTANT]
>Az egyes Azure Active Directory bérlők számára csak 200-es alkalmazáscsoport hozható létre. Ezt a korlátot a felhasználók számára a hírcsatornák beolvasásához szükséges szolgáltatási korlátozások miatt vesszük fel. Ez a korlát nem vonatkozik a Windows Virtual Desktopban (klasszikus) létrehozott alkalmazás-csoportokra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoport, hogyan tölthető be a RemoteApp-programokkal, és hogyan rendelhet hozzá felhasználókat az alkalmazás-csoporthoz. Az érvényesítési gazdagépek létrehozásáról a következő oktatóanyagban olvashat bővebben. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása a szolgáltatásfrissítések érvényesítéséhez](./create-validation-host-pool.md)
