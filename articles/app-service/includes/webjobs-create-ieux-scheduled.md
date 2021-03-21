---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745672"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Ütemezett Webjobs létrehozása


1. A [Azure Portal](https://portal.azure.com).
1. Lépjen a **app Service** <abbr title="Az alkalmazás-erőforrás lehet egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás.">Alkalmazás-erőforrás</abbr>.
1. Válassza a **Webjobs** elemet.

   ![Webjobs-feladatok kiválasztása](../media/web-sites-create-web-jobs/select-webjobs.png)

1. A **webjobs** lapon válassza a **Hozzáadás** lehetőséget.

    ![Webjobs lap](../media/web-sites-create-web-jobs/wjblade.png)

1. Használja a táblázatban megadott Webjobs-beállítások **hozzáadása** beállítást.

    ![Webjobs hozzáadása lap](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Beállítás      | Mintaérték   |
    | ------------ | ----------------- | 
    | <abbr title="Egy App Service alkalmazáson belül egyedi név. Betűvel vagy számmal kell kezdődnie, és a és a nem tartalmazhat különleges `-` karaktereket `_` .">Name</a> | myScheduledWebJob |  |
    | <abbr title="A végrehajtható fájlt vagy parancsfájlt tartalmazó *. zip* fájl, valamint a program vagy a parancsfájl futtatásához szükséges összes támogató fájl.">Fájlfeltöltés</abbr> | ConsoleApp.zip |
    | <abbr title="A típusok között folyamatos, aktivált.">Típus</abbr> | Kiváltott |
    | <abbr title="Ahhoz, hogy az ütemezés megbízhatóan működjön, engedélyezze az Always On funkciót. Az Always on csak az alapszintű, a standard és a prémium szintű díjszabásban érhető el.">Triggerek</a> | Ütemezett |
    | CRON-kifejezés</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>További információ a CRON-kifejezésekről</summary>
     <a name="#ncrontab-expressions"></a>
    
     Megadhat egy [NCRONTAB kifejezést](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) a portálon, vagy belefoglalhat egy `settings.job` fájlt a webjobs *. zip* fájl gyökerébe, ahogy az alábbi példában is látható:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     További információ: [aktivált Webjobs ütemezése](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Kattintson az **OK** gombra.

    Az új Webjobs megjelenik a **webjobs** oldalon.
    
    ![Webjobs-feladatok listája](../media/web-sites-create-web-jobs/listallwebjobs.png)
