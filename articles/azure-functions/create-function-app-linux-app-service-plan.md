---
title: Function-alkalmazás létrehozása Linux rendszeren a Azure Portal
description: Ismerje meg, hogyan hozhatja létre első Azure-függvényét Linux rendszeren a Azure Portal használatával.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d6787e1dd8312e368964a1f555025beff9a06a23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732258"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Function-alkalmazás létrehozása Linux rendszeren egy Azure App Service tervben

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy alapértelmezett Azure App Service-tárolóban üzemeltesse. Ez a cikk bemutatja, hogyan használható a [Azure Portal](https://portal.azure.com) egy olyan Linux-alapú Function-alkalmazás létrehozásához, amely egy [app Service](dedicated-plan.md)-csomagban fut. [Használhatja saját egyéni tárolóját](functions-create-function-linux-custom-image.md) is.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében. Ebben a cikkben egy App Service tervet hoz létre a Function-alkalmazás létrehozásakor.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Függvényalkalmazás létrehozása az Azure Portalon":::

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban megadott Function App-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| **Kód** (alapértelmezett) | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.net Core** lehetőséget a C# és az F # függvények számára. |
    |**Verzió**| Verziószám | Válassza ki a telepített futtatókörnyezet verzióját.  |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Alapismeretek lap":::

1. Válassza a **Tovább: Üzemeltetés** lehetőségre. Az **üzemeltetés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| **Linux** | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Felkészülés](../azure-functions/functions-scale.md)** | **Felhasználás (kiszolgáló nélküli)** | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények követelményeinek megfelelően. Ebben a [kiszolgáló](https://azure.microsoft.com/overview/serverless-computing/) nélküli üzemeltetésben csak a függvények futtatásának idejére kell fizetnie. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Üzemeltetési oldal":::

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Igen** (alapértelmezett) | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával vagy az **új létrehozása** lehetőség kiválasztásával módosíthatja a Application Insights nevét, vagy választhat egy másik régiót egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Figyelési oldal":::

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

    ![Üzembe helyezési értesítés](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Még a Function app elérhetővé tétele után is eltarthat néhány percig, hogy teljesen inicializálható legyen.

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP-trigger függvény létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre függvényt az új Function alkalmazásban a portálon.

> [!NOTE]
> A portál fejlesztői felülete hasznos lehet a Azure Functions kipróbálásához. A legtöbb esetben érdemes lehet helyileg fejleszteni a függvényeket, és a [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) vagy a [Azure functions Core Tools](functions-run-local.md#create-a-local-functions-project)használatával közzétenni a projektet a Function alkalmazásban.  

1. A **függvények** ablak bal oldali menüjében válassza a **függvények** lehetőséget, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 
 
1. Az **új függvény** ablakban válassza a **http-trigger** lehetőséget.

    ![HTTP-trigger függvény kiválasztása](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Az **új függvény** ablakban fogadja el az **új függvény** alapértelmezett nevét, vagy adjon meg egy új nevet. 

1. Válassza a **Névtelen** lehetőséget az **engedélyezési szint** legördülő listából, majd kattintson a **függvény létrehozása** elemre.

    Az Azure létrehozza a HTTP trigger függvényt. Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új HTTP-trigger függvényben válassza a **kód + tesztelés** elemet a bal oldali menüben, majd a felső menüben válassza a **függvény URL-címének beolvasása** elemet.

    ![Válassza a függvény URL-címének beolvasása](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. A **függvény URL-címének beolvasása** párbeszédpanelen válassza az **alapértelmezett** lehetőséget a legördülő listából, majd válassza a **Másolás a vágólapra** ikont. 

    ![A függvény URL-címének másolása az Azure portálról](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a lekérdezési karakterlánc értékét `?name=<your_name>` az URL végéhez, majd nyomja le az ENTER billentyűt a kérelem futtatásához. 

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. A nyomkövetés kimenetének megtekintéséhez térjen vissza a **kód + teszt** lapra a portálon, és bontsa ki a lap alján található **naplók** nyilat.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozta a Function alkalmazást egy egyszerű HTTP-trigger függvénnyel.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információt az [Azure Functions HTTP-kötéseit](functions-bindings-http-webhook.md) ismertető cikkben talál.
