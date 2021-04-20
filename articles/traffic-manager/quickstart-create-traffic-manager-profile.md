---
title: 'Rövid útmutató: Profil létrehozása a nagy elérésű alkalmazásokhoz – Azure Portal – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magas rendelkezésre álló webalkalmazás létrehozásához a Azure Portal.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 13b5925310c615461424f78d90ba9849c9bf58c5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727977"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Rövid útmutató: Traffic Manager profil létrehozása a Azure Portal

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányáról olvashat. Mindegyik másik Azure-régióban fut. A végpont prioritása Traffic Manager [létre.](traffic-manager-routing-methods.md#priority-traffic-routing-method) A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges webhelyre irányítja. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Az üzembe helyezési Traffic Manager ábrája." border="false":::

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban egy webalkalmazás két példányára lesz szüksége, amelyek két különböző Azure-régióban (az *USA* keleti régiójában és *Nyugat-Európában) üzembe helyezhetők.* Mindegyik elsődleges és feladatátvételi végpontként szolgál majd a Traffic Manager.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása lehetőséget.** Keressen rá a **Webalkalmazás kifejezésre, és** válassza a **Létrehozás lehetőséget.**

1. A **Create a Web App (Webalkalmazás létrehozása) lapon** írja be vagy válassza ki a következő értékeket az Alapvető **beállítások** lapon:

    | Beállítás                 | Érték |
    | ---                     | --- |
    | Előfizetés            | Válassza ki előfizetését. |    
    | Erőforráscsoport          | Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupTM1* szöveget a szövegmezőbe.|
    | Name                    | Adjon meg egy egyedi **nevet** a webalkalmazásnak. Ebben a példában a *myWebAppEastUS van használatban.* |
    | Közzététel                 | Válassza a **Kód** lehetőséget. |
    | Futtatókörnyezet verme           | Válassza **ASP.NET v4.7 lehetőséget.** |
    | Operációs rendszer        | Válassza a **Windows lehetőséget.** |
    | Region                  | Válassza az **USA keleti régiója** lehetőséget. |
    | Windows-csomag            | Válassza **az Új létrehozása lehetőséget,** és írja be a *myAppServicePlanEastUS* szöveget a szövegmezőbe. |
    | Termékváltozat és méret            | Válassza **a Standard S1 100 teljes ACU, 1,75 GB memória lehetőséget.** |
   
1. Válassza a **Figyelés** lapot, vagy válassza a **Tovább: Figyelés lehetőséget.**  A **Figyelés alatt** állítsa **a Application Insights** Enable  >  **Application Insights (Nem)** **beállításra.**

1. Válassza az Áttekintés lehetőséget, és hozza létre a **et.**

1. Tekintse át a beállításokat, majd válassza a **Létrehozás lehetőséget.**  A webalkalmazás sikeres üzembe helyezésekor létrehoz egy alapértelmezett webhelyet.

1. Kövesse az 1–6. lépést egy második, *myWebAppWestEurope nevű webalkalmazás létrehozásához.* Az **erőforráscsoport neve** *myResourceGroupTM2,* nyugat-európai régióval, és **App Service** **myAppServicePlanWestEurope nevű csomagnév.**   A többi beállítás megegyezik a *myWebAppEastUS beállításokkal.*

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt, amely a végpont prioritása alapján irányítja a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az Erőforrás **létrehozása lehetőséget.** Ezután keressen rá a **Traffic Manager, és** válassza a **Létrehozás lehetőséget.**
1. A Create Traffic Manager profile (Új profil **létrehozása) lapon adja** meg vagy válassza ki a következő beállításokat:

    | Beállítás | Érték |
    | --------| ----- |
    | Név | Adjon egyedi nevet a Traffic Manager profilnak.|
    | Útválasztási metódus | Válassza a **Prioritás lehetőséget.**|
    | Előfizetés | Válassza ki azt az előfizetést, amelyre alkalmazni szeretné a Traffic Manager-profilt. |
    | Erőforráscsoport | Válassza *a myResourceGroupTM1 lehetőséget.*|
    | Hely |Ez a beállítás az erőforráscsoport helyére vonatkozik. Ez nincs hatással a Traffic Manager központilag telepített profilra.|

1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az *USA keleti régiójában* lévő webhelyt elsődleges végpontként, amelyre az összes felhasználói forgalom át lesz irányítva. Adja hozzá a *nyugat-európai webhelyet* feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra kerül.

1. A portál keresősávjában adja meg Traffic Manager előző szakaszban létrehozott profilnevet.
1. Válassza ki a profilt a keresési eredmények közül.
1. A **Traffic Manager beállítások** szakaszában  válassza a **Végpontok** lehetőséget, majd a Hozzáadás **lehetőséget.**

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Képernyőkép a végpontbeállításokról Traffic Manager profilban.":::

1. Adja meg vagy válassza ki a következő beállításokat:

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza az **Azure-végpont lehetőséget.** |
    | Name | Írja be *a myPrimaryEndpoint ált.* |
    | Célerőforrás típusa | Válassza a **App Service** lehetőséget. |
    | Célerőforrás | Válassza **az Alkalmazásszolgáltatás kiválasztása az USA** keleti  >  **régiója lehetőséget.** |
    | Prioritás | Válassza **az 1 lehetőséget.** Minden forgalom erre a végpontra kerül, ha kifogástalan állapotú. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Képernyőkép arról, hogy hol adhat végpontot a Traffic Manager profiljához.":::
    
1. Válassza az **OK** lehetőséget.
1. A második Azure-régió feladatátvételi végpontjának létrehozásához ismételje meg a 3. és a 4. lépést az alábbi beállításokkal:

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza az **Azure-végpont lehetőséget.** |
    | Name | Írja be a *myFailoverEndpoint ált.* |
    | Célerőforrás típusa | Válassza a **App Service** lehetőséget. |
    | Célerőforrás | Válassza **a Select an app service** West Europe  >  **(Alkalmazásszolgáltatás kiválasztása Nyugat-Európa) lehetőséget.** |
    | Prioritás | Válassza **a 2 lehetőséget.** Ha az elsődleges végpont nem megfelelő, minden forgalom erre a feladatátvételi végpontra kerül. |

1. Válassza az **OK** lehetőséget.

Ha végzett a két végpont hozzáadásával, azok a következő **profilban jelennek meg Traffic Manager profilban.** Figyelje meg, hogy a figyelési **állapotuk most Online.**

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a saját profilja tartománynevét Traffic Manager ellenőrizni. Az elsődleges végpontot is úgy kell konfigurálnia, hogy az ne legyen elérhető. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek az az oka, Traffic Manager a rendszer elküldi a forgalmat a feladatátvételi végpontra.

### <a name="check-the-dns-name"></a>A DNS-név ellenőrzése

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére.
1. Válassza ki a Traffic Manager-profilt. Megjelenik **az** Áttekintés.
1. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Képernyőkép a dns-Traffic Manager helyéről.":::

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

1. A webalkalmazás alapértelmezett webhelyének megtekintéséhez egy webböngészőben adja Traffic Manager profil DNS-nevét.

    > [!NOTE]
    > Ebben a rövid útmutatóban minden kérés az elsődleges végpontra lesz irányítva. A prioritása **1.**

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Képernyőkép a webhelyről a profil elérhetőségének Traffic Manager megerősítéséhez.":::

1. A feladatátvételi Traffic Manager megtekintéséhez tiltsa le az elsődleges helyet:
    1. A Traffic Manager Profil lapon, az **Áttekintés szakaszban** válassza a **myPrimaryEndpoint lehetőséget.**
    1. A *myPrimaryEndpoint mezőben válassza* a **Letiltva mentés**  >  **lehetőséget.**
    1. Zárja **be a myPrimaryEndpoint et.** Figyelje meg, hogy az állapot **most Letiltva.**
1. Másolja ki a Traffic Manager profil DNS-nevét az előző lépésből a webhely új webböngésző-munkamenetben való megtekintéséhez.
1. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

Az elsődleges végpont nem érhető el, ezért a rendszer a feladatátvételi végpontra irányítva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást. Válassza ki az egyes elemet az irányítópulton, majd válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt. Lehetővé teszi a felhasználói forgalom magas rendelkezésre állású webalkalmazások számára történő közvetlen beállítását. Ha többet szeretne megtudni a forgalom útválasztásról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
