---
title: 'Gyors útmutató: profil létrehozása az alkalmazások számára – Azure Portal – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt, hogy a Azure Portal használatával egy magasan elérhető webalkalmazást építsen ki.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 45489d3500a4a744f2aeb34dc21122d180797133
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101318"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Gyors útmutató: Traffic Manager profil létrehozása a Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre olyan Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát olvashatja. Mindegyik egy másik Azure-régióban fut. A Traffic Manager-profilt a [végpont prioritása](traffic-manager-routing-methods.md#priority-traffic-routing-method)alapján hozza létre. A profil a webes alkalmazást futtató elsődleges helyre irányítja a felhasználói forgalmat. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helyhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban két különböző Azure-régióban (az *USA keleti* régiójában és *Nyugat-Európában*) üzembe helyezett webalkalmazás két példánya szükséges. Mindegyik a Traffic Manager elsődleges és feladatátvételi végpontjának fogja szolgálni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** lehetőséget. Keresse meg a **webalkalmazást** , és válassza a **Létrehozás** lehetőséget.

1. A **Webalkalmazás létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

    | Beállítás                 | Érték |
    | ---                     | --- |
    | Előfizetés            | Válassza ki előfizetését. |    
    | Erőforráscsoport          | Válassza az **új létrehozása** elemet, és írja be a *myResourceGroupTM1* szöveget a szövegmezőbe.|
    | Name                    | Adjon egyedi **nevet** a webalkalmazásnak. Ez a példa az *myWebAppEastUS*-t használja. |
    | Közzététel                 | Válassza a **Kód** lehetőséget. |
    | Futtatókörnyezet verme           | Válassza a **ASP.net v 4.7** elemet. |
    | Operációs rendszer        | Válassza a **Windows** lehetőséget. |
    | Region                  | Válassza az **USA keleti régiója** lehetőséget. |
    | Windows-csomag            | Válassza az **új létrehozása** elemet, és írja be a *myAppServicePlanEastUS* szöveget a szövegmezőbe. |
    | Termékváltozat és méret            | Válassza **a standard S1 100 Total ACU, 1,75-GB memóriát**. |
   
1. Válassza a **figyelés** fület, vagy válassza a **Tovább: figyelés** lehetőséget.  A **figyelés** területen állítsa be **Application Insights**  >  az **Application Insights engedélyezése** a **nem** értékre.

1. Válassza **a felülvizsgálat és létrehozás** lehetőséget.

1. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.  A webalkalmazás sikeres üzembe helyezése után létrejön egy alapértelmezett webhely.

1. Az 1-6-es lépéseket követve hozzon létre egy *myWebAppWestEurope* nevű második webalkalmazást. Az **erőforráscsoport** neve *MyResourceGroupTM2*, a *Nyugat-európai* **régióval** és **app Service** **myAppServicePlanWestEurope**. Az összes többi beállítás ugyanaz, mint a *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt, amely a végponti prioritás alapján irányítja a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** lehetőséget. Ezután keressen rá **Traffic Manager profilra** , és válassza a **Létrehozás** lehetőséget.
1. A **Traffic Manager profil létrehozása** lapon adja meg a következő beállításokat, vagy válassza ki ezeket:

    | Beállítás | Érték |
    | --------| ----- |
    | Név | Adjon meg egy egyedi nevet a Traffic Manager profilhoz.|
    | Útválasztási metódus | Válassza a **Priority (prioritás**) lehetőséget.|
    | Előfizetés | Válassza ki azt az előfizetést, amelyre a Traffic Manager-profilt alkalmazni kívánja. |
    | Erőforráscsoport | Válassza a *myResourceGroupTM1* lehetőséget.|
    | Hely |Ez a beállítás az erőforráscsoport helyét jelöli. Nincs hatása a globálisan telepítendő Traffic Manager-profilra.|

1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az *USA keleti régiójában* lévő webhelyt elsődleges végpontként, amelyre az összes felhasználói forgalom át lesz irányítva. Adja hozzá a webhelyet a *Nyugat-Európában* feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan átirányítja a feladatátvételi végpontot.

1. A portál keresősáv mezőjébe írja be az előző szakaszban létrehozott Traffic Manager profil nevét.
1. Válassza ki a profilt a keresési eredmények közül.
1. **Traffic Manager profilban** a **Beállítások** szakaszban válassza a **végpontok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="A Traffic Manager profil végpont-beállításai":::

1. Adja meg vagy válassza ki a következő beállításokat:

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza ki az **Azure-végpontot**. |
    | Name | Adja meg a *myPrimaryEndpoint*. |
    | Célerőforrás típusa | Válassza a **app Service** lehetőséget. |
    | Célerőforrás | Válassza **az App Service az**  >  **USA keleti** régiója lehetőséget. |
    | Prioritás | Válassza az **1** lehetőséget. Az összes forgalom állapota erre a végpontra kerül, amikor kifogástalan állapotú. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Képernyőkép arról, hogy hol adhat hozzá végpontot a Traffic Manager profiljához":::
    
1. Válassza az **OK** lehetőséget.
1. Ha feladatátvételi végpontot szeretne létrehozni a második Azure-régióhoz, ismételje meg a 3. és a 4. lépést a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza ki az **Azure-végpontot**. |
    | Name | Adja meg a *myFailoverEndpoint*. |
    | Célerőforrás típusa | Válassza a **app Service** lehetőséget. |
    | Célerőforrás | Válassza **az App Service**  >  **Nyugat-Európa** kiválasztása lehetőséget. |
    | Prioritás | Válassza a **2** lehetőséget. Az összes forgalom erre a feladatátvételi végpontra kerül, ha az elsődleges végpont állapota nem megfelelő. |

1. Válassza az **OK** lehetőséget.

Ha elkészült a két végpont hozzáadásával, azok **Traffic Manager profilban** jelennek meg. Figyelje meg, hogy a figyelési állapota most **online** állapotú.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a Traffic Manager profil tartománynevét fogja megtekinteni. Azt is beállíthatja, hogy az elsődleges végpont ne legyen elérhető. Végezetül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy Traffic Manager továbbítja a forgalmat a feladatátvételi végpontnak.

### <a name="check-the-dns-name"></a>DNS-név keresése

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére.
1. Válassza ki a Traffic Manager-profilt. Megjelenik az **Áttekintés** .
1. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Képernyőkép a Traffic Manager DNS-név helyéről":::

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

1. A webböngészőben adja meg Traffic Manager profiljának DNS-nevét a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányítja át. Az **1-es prioritásra** van beállítva.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Képernyőfelvétel a weboldalról Traffic Manager profil rendelkezésre állásának megerősítéséhez":::

1. Ha Traffic Manager feladatátvételt szeretné megtekinteni a működés közben, tiltsa le az elsődleges helyet:
    1. A Traffic Manager profil lapon az **Áttekintés** szakaszban válassza a **myPrimaryEndpoint** lehetőséget.
    1. A *myPrimaryEndpoint* területen válassza a **letiltott**  >  **Mentés** lehetőséget.
    1. **MyPrimaryEndpoint** lezárása. Figyelje meg, hogy az állapot jelenleg **le van tiltva** .
1. Másolja a Traffic Manager profiljának DNS-nevét az előző lépésből, hogy megtekintse a webhelyet egy új böngésző-munkamenetben.
1. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

Az elsődleges végpont nem érhető el, ezért a rendszer átirányítja a feladatátvételi végpontra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportot, a webalkalmazásokat és az összes kapcsolódó erőforrást. Ehhez jelölje ki az egyes elemeket az irányítópulton, és válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt. Lehetővé teszi a felhasználói forgalom közvetlen használatát a magas rendelkezésre állású webalkalmazásokhoz. Ha többet szeretne megtudni az útválasztási forgalomról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
