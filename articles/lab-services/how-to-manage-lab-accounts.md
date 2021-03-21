---
title: Labor-fiókok kezelése a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes laboratóriumi fiókot, vagy törölhet egy Lab-fiókot egy Azure-előfizetésben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434090"
---
# <a name="create-and-manage-lab-accounts"></a>Tesztkörnyezeti fiókok létrehozása és felügyelete
Azure Lab Services a labor-fiók a felügyelt labor típusok, például a laborok tárolója. A rendszergazda létrehoz egy Azure Lab Services, és hozzáférést biztosít a labor tulajdonosai számára, akik a fiókban létrehozhatnak laborokat. Ez a cikk bemutatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes tesztkörnyezet fiókját, vagy törölhet egy labor-fiókot.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. A **DevOps** szakaszban válassza a **labor-fiókok** lehetőséget. Ha a csillag () lehetőséget választja a `*` **labor-fiókok** mellett, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **labor-fiókok** lehetőséget a **Kedvencek** alatt.

    ![Minden szolgáltatás – > Lab-fiókok](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **labor-fiókok** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, vagy **hozzon létre egy Lab-fiókot** az oldalon. 

    ![Válassza a Hozzáadás lehetőséget a labor-fiókok oldalon](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **labor-fiók létrehozása** lap **alapok** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot.
    5. Ahhoz, hogy a labor létrehozója kiválassza a **labor helye** mezőt, adja meg, hogy szeretné-e, ha a labor készítői ki tudják választani a tesztkörnyezet helyét. Alapértelmezés szerint a beállítás le van tiltva. Ha le van tiltva, a labor létrehozói nem tudnak megadni egy helyet a létrehozandó labornak. A laborok a legközelebbi földrajzi helyen a labor-fiókhoz jönnek létre. Ha engedélyezve van, a tesztkörnyezet létrehozója kiválaszthatja a kívánt helyet a labor létrehozásakor. További információ: [a tesztkörnyezet létrehozójának engedélyezése a tesztkörnyezet helyének kiválasztásához](allow-lab-creator-pick-lab-location.md). 

        ![Lab-fiók létrehozása – > alapjai](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Válassza a **Tovább: speciális** lehetőséget az oldal alján a **speciális** lapra való ugráshoz, majd hajtsa végre a következő lépéseket: 
    1. Válasszon ki egy meglévő **megosztott képtárat** , vagy hozzon létre egyet. A sablon virtuális gépet a megosztott rendszerkép-katalógusba mentheti, amelyet mások is felhasználhatnak. A megosztott képtárakkal kapcsolatos részletes információkért lásd: [közös rendszerkép-katalógus használata Azure Lab Servicesban](how-to-use-shared-image-gallery.md).
    2. Itt adhatja meg, hogy a rendszer **automatikusan leállítsa-e a Windows virtuális gépeket** , amikor a felhasználók lecsatlakoznak róluk. Annak megadása, hogy a virtuális gépek meddig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt. 
    3. A **társ virtuális hálózat** területen válasszon ki egy társ virtuális hálózatot (VNet) a tesztkörnyezet hálózatához. A fiókban létrehozott laborok kapcsolódnak a kiválasztott VNet, és hozzáférnek a kiválasztott VNet lévő erőforrásokhoz. További információ: [a tesztkörnyezet virtuális hálózatának összekötése egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md).    
    8. Válasszon **címtartományt** a virtuális gépek számára a laborban. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva. További információ: [címtartomány megadása virtuális gépekhez a tesztkörnyezetben](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > A **címtartomány** tulajdonság csak akkor érvényes, ha a tesztkörnyezetben engedélyezve van egy **társ virtuális hálózat** .

        ![Labor-fiók létrehozása – > Advanced](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Kattintson a **Next (tovább** ) gombra a lap alján a **címkék** lapra való váltáshoz. Vegyen fel minden olyan címkét, amelyet hozzá szeretne rendelni a labor-fiókhoz. A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).

    ![Képernyőkép, amely a "Lab-fiók létrehozása" lapot mutatja a Címkék lapon kiemelve.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Kattintson a lap alján található **felülvizsgálat + létrehozás** gombra a **felülvizsgálat + létrehozás** lapra való váltáshoz. 
4. Tekintse át az ezen a lapon található összegző információkat, majd kattintson a **Létrehozás** gombra. 

    ![Lab-fiók létrehozása – > Címkék](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Várjon, amíg befejeződik az üzembe helyezés, bontsa ki a **következő lépések** elemet, majd válassza az **Ugrás az erőforráshoz** lehetőséget az alábbi képen látható módon: 

    A **harang ikont** is kiválaszthatja az eszköztáron (**értesítések**), ellenőrizheti, hogy az üzembe helyezés sikeres volt-e, majd válassza az **erőforrás keresése** lehetőséget. 

    Másik lehetőségként válassza a **labor-fiókok** lapon a **frissítés** lehetőséget, majd válassza ki a létrehozott labor-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Tesztkörnyezet-fiókok megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki az **összes erőforrás** elemet a menüből. 
3. Válassza a **típushoz** tartozó **labor-fiókok** lehetőséget. 
    Az előfizetés, az erőforráscsoport, a helyszínek és a címkék alapján is szűrhető. 

    ![Minden erőforrás – > Lab-fiókok](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Labor-fiók törlése
Kövesse az előző szakasz utasításait, amely a listában szereplő Lab-fiókokat jeleníti meg. A labor-fiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a törölni kívánt **labor-fiókot** . 
2. Válassza a **Törlés** lehetőséget az eszköztáron. 

    ![Labor-fiókok – > törlés gomb](./media/how-to-manage-lab-accounts/delete-button.png)
1. Erősítse meg az **Igen** értéket.
1. Válassza a **Törlés** elemet. 

    ![Labor-fiók törlése – megerősítés](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> A Lab-fiókok kezeléséhez az az. LabServices PowerShell-modult (előzetes verzió) is használhatja. További információ: az az [. LabServices Kezdőlap a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikkeket **: útmutató a**(z) tartalomjegyzék  ->  (**Lab-fiók tulajdonosa) szakaszának létrehozásához és konfigurálásához** . 