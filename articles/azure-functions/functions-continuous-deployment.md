---
title: Azure Functions – folyamatos üzembe helyezés
description: A függvények közzétételéhez használja a Azure App Service folyamatos üzembe helyezési funkcióit.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83123673"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés

A Azure Functions segítségével folyamatosan üzembe helyezheti a kódot a [verziókövetés integrációjának](functions-deployment-technologies.md#source-control)használatával. A verziókövetés integrációja lehetővé teszi egy olyan munkafolyamat használatát, amelyben a kód frissítése elindítja az Azure-ba történő üzembe helyezést. Ha még nem Azure Functions, az első lépésekhez tekintse át az [Azure functions áttekintését](functions-overview.md).

A folyamatos üzembe helyezés jó választás olyan projektekhez, ahol több és gyakori hozzájárulásokat is integrálhat. Ha folyamatos üzembe helyezést használ, a kód egyetlen forrását őrzi meg, amely lehetővé teszi a csapatok számára, hogy egyszerűen működjenek együtt. A folyamatos üzembe helyezést Azure Functions a következő forráskód-helyekről állíthatja be:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Az Azure-beli függvények üzembe helyezési egysége a Function alkalmazás. Egy Function alkalmazás összes funkciója egyszerre van üzembe helyezve. Miután engedélyezte a folyamatos üzembe helyezést, a Azure Portalhoz való hozzáférés *csak olvashatóként* van konfigurálva, mert az igazság forrása máshol van beállítva.

## <a name="requirements-for-continuous-deployment"></a>A folyamatos üzembe helyezésre vonatkozó követelmények

A sikeres üzembe helyezéshez a címtár struktúrájának kompatibilisnek kell lennie a Azure Functions várt alapszintű mappastruktúrát.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A folyamatos üzembe helyezés még nem támogatott a használati terveken futó Linux-alkalmazások esetében. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Folyamatos üzembe helyezés beállítása

Egy meglévő Function alkalmazás folyamatos üzembe helyezésének konfigurálásához végezze el a következő lépéseket. A lépések bemutatják a GitHub-adattárral való integrációt, de a hasonló lépések érvényesek az Azure Repos vagy más forráskód-Tárházak esetében.

1. A [Azure Portal](https://portal.azure.com)a Function alkalmazásban válassza ki a **központi telepítési központ** elemet, válassza a **GitHub** lehetőséget, majd válassza az **Engedélyezés** lehetőséget. Ha már engedélyezte a GitHubot, válassza a **Folytatás** lehetőséget, és hagyja ki a következő lépést. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service központi telepítési központ":::

3. A GitHubon válassza a **AzureAppService engedélyezése** lehetőséget.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Engedélyezés Azure App Service":::

    Adja meg a GitHub-jelszavát, majd kattintson a **Continue (folytatás**) gombra.

4. Válasszon egyet a következő Build-szolgáltatók közül:

    * **App Service Build szolgáltatás**: a legjobb, ha nincs szüksége buildre, vagy ha általános buildre van szüksége.
    * **Azure-folyamatok (előzetes verzió)**: a legjobb, ha nagyobb mértékű vezérlésre van szüksége a builden. Ez a szolgáltató jelenleg előzetes verzióban érhető el.

    Válassza a **Folytatás** lehetőséget.

5. Konfigurálja a megadott verziókövetés-beállításra vonatkozó adatokat. A GitHub esetében meg kell adnia vagy ki kell választania a **szervezet**, a **tárház** és az **ág** értékeit. Az értékek a kód helyétől függenek. Ezután válassza a **Folytatás** lehetőséget.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="A GitHub konfigurálása":::

6. Tekintse át az összes adatot, majd a **Befejezés** gombra kattintva fejezze be a telepítési konfigurációt.

A folyamat befejezésekor a rendszer a megadott forrásból származó összes kódot telepíti az alkalmazásba. Ezen a ponton a központi telepítési forrás változásai elindítják ezeket a módosításokat az Azure-beli Function alkalmazásban.

> [!NOTE]
> A folyamatos integráció konfigurálása után már nem szerkesztheti a forrásfájlokat a functions portálon.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
