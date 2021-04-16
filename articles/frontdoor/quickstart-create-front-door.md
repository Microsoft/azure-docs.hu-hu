---
title: 'Rövid útmutató: Magas rendelkezésre állás beállítása Azure Front Door Service – Azure Portal'
description: Ez a rövid útmutató bemutatja, hogyan használhatja a Azure Front Door Service a magas rendelkezésre álló és nagy teljesítményű globális webalkalmazáshoz a Azure Portal.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 2cf52d30c5658e73c55944bdfb7d424425fa4507
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538952"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre állású globális webalkalmazásokhoz

A Azure Front Door használatának első Azure Portal a webalkalmazások magas rendelkezésre állásának beállításához.

Ebben a rövid útmutatóban Azure Front Door webalkalmazás két példányát, amelyek különböző Azure-régiókban futnak. Hozzon létre egy Front Door, amely azonos súlyozású és prioritású háttérkészletek alapján van létrehozva. Ez a konfiguráció az alkalmazást futtató legközelebbi webhelyre irányítja a forgalmat. Azure Front Door folyamatosan figyeli a webalkalmazást. A szolgáltatás automatikus feladatátvételt biztosít a következő elérhető webhelyre, ha a legközelebbi hely nem érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

Ehhez a rövid útmutatóhoz két példányra van szükség egy webalkalmazásból, amelyek különböző Azure-régiókban futnak. Mindkét webalkalmazáspéldány *Aktív/Aktív* módban fut, így bármelyik képes forgalmat venni. Ez a konfiguráció eltér az *aktív/készenléti* konfigurációtól, ahol az egyik feladatátvételként működik.

Ha még nem rendelkezik webalkalmazással, a következő lépésekkel állíthat be példa webalkalmazásokat.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

1. A képernyő bal felső részén válassza az **Erőforrás** létrehozása  >   **WebApp lehetőséget.**

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Webalkalmazás létrehozása az Azure Portalon":::

1. A **Webalkalmazás létrehozása** lap Alapvető beállítások **lapján** adja meg vagy válassza ki a következő adatokat.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Előfizetés**               | Válassza ki előfizetését. |    
    | **Erőforráscsoport**       | Válassza **az Új létrehozása lehetőséget,** *FrontDoorQS_rg1* a szövegmezőbe.|
    | **Név**                   | Adjon meg egy egyedi **nevet** a webalkalmazásnak. Ez a példa a *WebAppContoso-1-et használja.* |
    | **Közzététel** | Válassza a **Kód** lehetőséget. |
    | **Futtatókörnyezet verme**         | Válassza **a .NET Core 2.1 (LTS) lehetőséget.** |
    | **Operációs rendszer**          | Válassza a **Windows lehetőséget.** |
    | **Régió**           | Válassza az **USA középső államoka lehetőséget.** |
    | **Windows-csomag** | Válassza **az Új létrehozása lehetőséget,** és írja be a *myAppServicePlanCentralUS* szöveget a szövegmezőbe. |
    | **Termékváltozat és méret** | Válassza **a Standard S1 100 teljes ACU- és 1,75 GB memóriát.** |

1. Válassza **az Áttekintés + létrehozás** lehetőséget, tekintse át az **Összegzést,** majd válassza a Létrehozás **lehetőséget.** Az üzembe helyezés eltarthat néhány percig.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="A webalkalmazás összegzésének áttekintése":::

Az üzembe helyezés befejezése után hozzon létre egy második webalkalmazást. Ugyanezeket az eljárásokat használja ugyanazokkal az értékekkel, kivéve a következő értékeket:

| Beállítás          | Érték     |
| ---              | ---  |
| **Erőforráscsoport**   | Válassza az **Új létrehozása lehetőséget,** és írja be a *FrontDoorQS_rg2* |
| **Név**             | Adjon egyedi nevet a webalkalmazásnak, ebben a *példában: WebAppContoso-2*  |
| **Régió**           | Ebben a példában egy másik régió, az *USA déli középső régiója* |
| **App Service csomag**  >  **Windows-csomag**         | Válassza **az Új lehetőséget,** írja be *a myAppServicePlanSouthCentralUS* gombra, majd kattintson az **OK gombra.** |

## <a name="create-a-front-door-for-your-application"></a>Front Door létrehozása az alkalmazáshoz

Konfigurálja Azure Front Door, hogy a két webalkalmazás-kiszolgáló közötti legkisebb késés alapján irányítsa a felhasználói forgalmat. Először adjon hozzá egy előtere gazdagépet a Azure Front Door.

1. A kezdőlapon vagy az Azure menüjében válassza az **Erőforrás létrehozása lehetőséget.** Válassza **a Hálózat**  >  **lehetőséget, és tekintse meg az összes**  >  **Front Door.**

1. A **Create** a **Front Door lap** Alapvető beállítások lapján adja meg vagy válassza ki a következő adatokat, majd válassza a **Tovább: Konfiguráció lehetőséget.**

    | Beállítás | Érték |
    | --- | --- |
    | **Előfizetés** | Válassza ki előfizetését. |    
    | **Erőforráscsoport** | Válassza **az Új létrehozása lehetőséget,** *FrontDoorQS_rg0* a szövegmezőbe.|
    | **Erőforráscsoport helye** | Válassza az **USA középső államoka lehetőséget.** |

1. Az **Előterek/tartományok területen válassza** a lehetőséget az Előtere **+** **gazdagép hozzáadása megnyitásához.**

1. A **Gazdagépnév mezőben** adjon meg egy globálisan egyedi állomásnevet. Ez a példa a *contoso-frontend előtűt használja.* Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Előtere gazdagép hozzáadása a Azure Front Door":::

Ezután hozzon létre egy háttérkészletet, amely a két webalkalmazást tartalmazza.

1. Továbbra is **a Create a Front Door (Új** háttérkészlet létrehozása) gombra, majd a **Backend pools**(Háttérkészletek) mezőben válassza a Lehetőséget a **+** **Háttérkészlet hozzáadása megnyitásához.**

1. A **Név mezőben** adja meg a *myBackendPool nevet,* majd válassza **a Háttér hozzáadása lehetőséget.**

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Háttérkészlet hozzáadása":::

1. A Háttér **hozzáadása panelen** válassza ki a következő adatokat, majd válassza a **Hozzáadás lehetőséget.**

    | Beállítás | Érték |
    | --- | --- |
    | **Háttér gazdagép típusa** | Válassza az **App Service lehetőséget.** |   
    | **Előfizetés** | Válassza ki előfizetését. |    
    | **Háttér gazdagép neve** | Válassza ki az első létrehozott webalkalmazást. Ebben a példában a webalkalmazás *a WebAppContoso-1 volt.* |

    **Hagyja meg az összes többi mező alapértelmezett beállítását.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Háttérgazda hozzáadása a Front Door":::

1. Válassza **ismét a Háttér hozzáadása** lehetőséget. válassza ki az alábbi adatokat, majd válassza a **Hozzáadás lehetőséget.**

    | Beállítás | Érték |
    | --- | --- |
    | **Háttér gazdagép típusa** | Válassza az **App Service lehetőséget.** |   
    | **Előfizetés** | Válassza ki előfizetését. |    
    | **Háttér gazdagép neve** | Válassza ki a második létrehozott webalkalmazást. Ebben a példában a webalkalmazás *a WebAppContoso-2 volt.* |

    **Hagyja meg az összes többi mező alapértelmezett beállítását.*

1. A **háttérkészlet** konfigurálásának befejezéséhez válassza **a** Háttérkészlet hozzáadása panel Hozzáadás lehetőséget.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Háttérkészlet hozzáadása a Azure Front Door":::

Végül adjon hozzá egy útválasztási szabályt. Az útválasztási szabály leképezi az előtere gazdagépet a háttérkészletre. A szabály továbbít egy kérést a `contoso-frontend.azurefd.net` **myBackendPool számára.**

1. Továbbra **is a Create a Front Door**(Új fiók létrehozása) gombra, majd az **Útválasztási szabályok alatt** válassza ki az **+** útválasztási szabály konfigurálni kívánt beállítását.

1. A **Szabály hozzáadása alatt a** Név **alatt** adja meg a *LocationRule nevet.* Fogadja el az összes alapértelmezett értéket, majd válassza a **Hozzáadás lehetőséget** az útválasztási szabály hozzáadásához.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Szabály hozzáadása a Front Door":::

   >[!WARNING]
   > Meg **kell** győződnie arról, hogy a Front Door minden egyes előtere gazdagéphez tartozik egy útválasztási szabály, amelyhez egy alapértelmezett elérési út `\*` () van társítva. Ez azt jelenti, hogy az összes útválasztási szabályban legalább egy útválasztási szabálynak kell lennie minden egyes előtere gazdagéphez, amely az alapértelmezett elérési úton ( ) van `\*` meghatározva. Ennek hiányában előfordulhat, hogy a végfelhasználói forgalom nem lesz megfelelően irányítva.

1. Válassza **az Áttekintés + létrehozás,** majd a Létrehozás **lehetőséget.**

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Konfigurált Azure Front Door":::

## <a name="view-azure-front-door-in-action"></a>A Azure Front Door megtekintése

Miután létrehozott egy Front Door, a konfiguráció globális üzembe helyezése eltarthat néhány percig. Ha elkészült, hozzáférhet a létrehozott előtere gazdagéphez. Egy böngészőben ugrás a következőre: `contoso-frontend.azurefd.net` . A kérés automatikusan a legközelebbi kiszolgálóhoz lesz irányítva a háttérkészlet megadott kiszolgálóiról.

Ha ezeket az alkalmazásokat ebben a rövid útmutatóban hozta létre, egy információs oldal fog látni.

Az azonnali globális feladatátvételi művelet tesztelése a következő lépésekkel:

1. Nyisson meg egy böngészőt a fent leírtak szerint, és nyissa meg az előtere címét: `contoso-frontend.azurefd.net` .

1. A Azure Portal keresse meg és válassza az *App Services lehetőséget.* Ebben a példában görgessen le az egyik webalkalmazáshoz, a **WebAppContoso-1-hez.**

1. Válassza ki a webalkalmazást, majd válassza **a Leállítás,** majd **az Igen** lehetőséget az ellenőrzéshez.

1. Frissítse a böngészőjét. Ugyaneznek az információs lapnak kell jelen lennie.

   >[!TIP]
   >Ezek a műveletek kissé késnek. Előfordulhat, hogy újra frissítenie kell.

1. Keresse meg és állítsa le a másik webalkalmazást is.

1. Frissítse a böngészőjét. Ezúttal hibaüzenetet kell látnia.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="A webalkalmazás mindkét példánya leállt":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, az összes létrehozott elemet eltávolíthatja. Az erőforráscsoport törlésével a tartalma is törölve lesz. Ha nem kívánja használni ezt a Front Door, távolítsa el az erőforrásokat a szükségtelen költségek elkerülése érdekében.

1. A Azure Portal keresse meg és válassza az Erőforráscsoportok **lehetőséget,** vagy válassza az **Erőforráscsoportok** lehetőséget a Azure Portal menüből.

1. Szűrje vagy görgessen le egy erőforráscsoport, például a **FrontDoorQS_rg0.**

1. Válassza ki az erőforráscsoportot, majd válassza **az Erőforráscsoport törlése lehetőséget.**

   >[!WARNING]
   >Ez a művelet nem visszafordítható.

1. Írja be az ellenőrizni kívánt erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

Ismételje meg az eljárást a másik két csoport esetében is.

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan adhat hozzá egyéni tartományt a Front Door.
> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)
