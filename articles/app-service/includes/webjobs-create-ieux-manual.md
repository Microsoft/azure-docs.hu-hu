---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109024"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Manuálisan aktivált Webjobs létrehozása

1. A [Azure Portal](https://portal.azure.com).
1. Lépjen a **app Service** <abbr title="Az alkalmazás-erőforrás lehet egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás.">Alkalmazás-erőforrás</abbr>.
1. Válassza a **Webjobs** elemet.

    ![Webjobs-feladatok kiválasztása](../media/web-sites-create-web-jobs/select-webjobs.png)

2. A **webjobs** lapon válassza a **Hozzáadás** lehetőséget.

   ![Webjobs lap](../media/web-sites-create-web-jobs/wjblade.png)

3. Használja a táblázatban megadott Webjobs-beállítások **hozzáadása** beállítást.

    ![Képernyőkép, amely a manuálisan aktivált Webjobs létrehozásához szükséges beállításokat jeleníti meg.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Beállítás      | Mintaérték   | 
    | ------------ | ----------------- | 
   | <abbr title="Egy App Service alkalmazáson belül egyedi név. Betűvel vagy számmal kell kezdődnie, és a és a nem tartalmazhat különleges `-` karaktereket `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="A végrehajtható fájlt vagy parancsfájlt tartalmazó *. zip* fájl, valamint a program vagy a parancsfájl futtatásához szükséges összes támogató fájl.">Fájlfeltöltés</abbr> | ConsoleApp.zip |
    | <abbr title="A típusok között folyamatos, aktivált.">Típus</abbr> | Kiváltott | 
    | <abbr title="A típusok a következők: ütemezett vagy manuális">Triggerek</a> | Kézi | |

4. Kattintson az **OK** gombra. 

   Az új Webjobs megjelenik a **webjobs** oldalon.

   ![Webjobs-feladatok listája](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Manuális webjobs futtatásához** kattintson a jobb gombbal a nevére a listában, majd kattintson a **Futtatás** parancsra.
   
    ![Webjobs futtatása](../media/web-sites-create-web-jobs/runondemand.png)

