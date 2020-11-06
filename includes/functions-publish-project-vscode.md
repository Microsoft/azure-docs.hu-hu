---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a525d1e14e642a64235c263ba29bf7a181bf9e30
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420988"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.


1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Adja meg a következő információkat a kérdésekben:

    - **Mappa kiválasztása** : válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ez nem jelenik meg, ha már nyitva van egy érvényes Function alkalmazás.

    - **Előfizetés kiválasztása** : válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    - **Függvényalkalmazás kiválasztása az Azure-ban** : válassza a lehetőséget `- Create new Function App` . (Ne válassza a `Advanced` lehetőséget, amely nem szerepel ebben a cikkben.)
      
    - **Adja meg a Function alkalmazás globálisan egyedi nevét** : írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen.
    
    - **Válasszon egy helyet az új erőforrásokhoz** : a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . 
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján:
    
    - Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
    - Szabványos Azure Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
    - Egy használati terv, amely a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét határozza meg. 
    - Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az azonos üzemeltetési csomagban található erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.
    - A Function alkalmazáshoz csatlakoztatott Application Insights-példány, amely a kiszolgáló nélküli függvény használatát követi nyomon.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 
    
1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](media/functions-publish-project-vscode/function-create-notifications.png)
