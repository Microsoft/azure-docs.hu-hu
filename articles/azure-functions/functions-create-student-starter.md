---
title: Függvény létrehozása az Azure diákoknak kezdőknek
description: Ismerje meg, hogyan hozhat létre Azure-függvényt egy Azure-beli Student Starter-előfizetés keretében
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182529"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Függvény létrehozása az Azure diákoknak kezdőknek

Ebben az oktatóanyagban létrehozunk egy "Hello World" HTTP-függvényt egy Azure diákoknak kezdő előfizetéshez. Az előfizetéshez tartozó Azure Functions elérhetővé tételeit is megismerheti.

A Microsoft *Azure diákoknak kezdő* csomaggal a felhőben való fejlesztéshez szükséges Azure-termékeket ingyenesen elsajátíthatja. [Ebben az ajánlatban itt találhat további információt.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. [További információ a functions szolgáltatásról.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Függvény létrehozása

 Ebből a cikkből megtudhatja, hogyan használhatja a Azure Functions a "Hello World" HTTP-trigger függvény létrehozásához a Azure Portalban.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP-trigger függvény létrehozása

1. A **függvények** ablak bal oldali menüjében válassza a **függvények** lehetőséget, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 
 
1. Az **új függvény** ablakban válassza a **http-trigger** lehetőséget.

    ![HTTP-trigger függvény kiválasztása](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Az **új függvény** ablakban fogadja el az **új függvény** alapértelmezett nevét, vagy adjon meg egy új nevet. 

1. Válassza a **Névtelen** lehetőséget az **engedélyezési szint** legördülő listából, majd kattintson a **függvény létrehozása** elemre.

    Az Azure létrehozza a HTTP trigger függvényt. Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új HTTP-trigger függvényben válassza a **kód + tesztelés** elemet a bal oldali menüben, majd a felső menüben válassza a **függvény URL-címének beolvasása** elemet.

    ![Válassza a függvény URL-címének beolvasása](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. A **függvény URL-címének beolvasása** párbeszédpanelen válassza az **alapértelmezett** lehetőséget a legördülő listából, majd válassza a **Másolás a vágólapra** ikont. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a lekérdezési karakterlánc értékét `?name=<your_name>` az URL végéhez, majd nyomja le az ENTER billentyűt a kérelem futtatásához. 

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-student-starter/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. A nyomkövetés kimenetének megtekintéséhez térjen vissza a **kód + teszt** lapra a portálon, és bontsa ki a lap alján található **naplók** nyilat.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Az Azure diákoknak kezdőknek támogatott szolgáltatásai

Az Azure diákoknak kezdők számára a Azure Functions futtatókörnyezet legtöbb funkcióját elérheti, és számos, az alábbiakban felsorolt kulcsfontosságú korlátozással rendelkezik:

* A HTTP-trigger az egyetlen támogatott trigger-típus.
    * Minden bemenet és minden kimeneti kötés támogatott! [Itt tekintheti meg a teljes listát.](functions-triggers-bindings.md)
* Támogatott nyelvek: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [A magasabb csomagokban támogatott nyelveket itt tekintheti meg](supported-languages.md)
* A Windows az egyetlen támogatott operációs rendszer.
* A skála egy ingyenes, legfeljebb 60 percet futtató, napi szinten üzemelő [példányra](https://azure.microsoft.com/pricing/details/app-service/windows/) korlátozódik. A HTTP-forgalom fogadása után a 0 – 1 példányra is automatikusan serverlessly.
* Csak a functions futtatókörnyezet [2. x vagy újabb verziója](functions-versions.md) támogatott.
* Az összes fejlesztői szerszámozás támogatott szerkesztési és közzétételi függvények esetén. Ide tartozik a VS Code, a Visual Studio, az Azure CLI és a Azure Portal. Ha a portálon kívül bármilyen más eszközt szeretne használni, először létre kell hoznia egy alkalmazást a portálon, majd az alkalmazást központi telepítési célként kell kiválasztania az előnyben részesített eszközön.

## <a name="next-steps"></a>Következő lépések

Ezzel befejezte a Function app létrehozását egy egyszerű HTTP-trigger függvénnyel. Ezután megismerheti a helyi eszközöket, a több nyelvet, a figyelést és az integrációt.

 * [Az első függvény létrehozása a Visual Studio használatával](./functions-create-your-first-function-visual-studio.md)
 * [Az első függvény létrehozása a Visual Studio Code használatával](./create-first-function-vs-code-csharp.md)
 * [Azure Functions JavaScript fejlesztői útmutató](./functions-reference-node.md)
 * [Azure Functions használata Azure SQL Databasehoz való kapcsolódáshoz](./functions-scenario-database-table-cleanup.md)
 * [További információ a Azure FUNCTIONS http-kötésekről](./functions-bindings-http-webhook.md).
 * [A Azure Functions figyelése](./functions-monitoring.md)