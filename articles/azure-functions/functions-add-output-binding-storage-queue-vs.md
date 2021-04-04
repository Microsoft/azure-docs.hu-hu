---
title: Függvények összekötése az Azure Storage-ba a Visual Studióval
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést a C# Class Library-függvények Azure Storage-várólistákhoz való összekapcsolásához a Visual Studio használatával.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 635392212027c73e5aa954eb671be31228796a0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185130"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Függvények összekötése az Azure Storage-ba a Visual Studióval

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használhatja a Visual studiót az [előző] rövid útmutatóban létrehozott funkció Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű alkalmazás-beállításban van tárolva `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Előfeltételek

A cikk elindítása előtt a következőket kell tennie: 

 - Fejezze be [a Visual Studio](./functions-create-your-first-function-visual-studio.md)rövid útmutatójának 1. részét. 

- Jelentkezzen be az Azure-előfizetésbe a Visual studióból.

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](./create-first-function-vs-code-csharp.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az alkalmazás beállításait a fájl *local.settings.js* . 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. 

1. A **műveletek** területen válassza a **Azure app Service beállítások szerkesztése** lehetőséget. 

    ![Az Alkalmazásbeállítások szerkesztése](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. A **AzureWebJobsStorage** alatt másolja a **távoli** karakterlánc értéket a **helyi** értékre, majd kattintson **az OK gombra**. 

A kapcsolat beállítását használó tárolási kötések `AzureWebJobsStorage` mostantól helyileg is csatlakozhatnak a várólista-tárolóhoz.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. 

1. Az **eszközök** menüben válassza a **NuGet Package** Manager  >  **csomagkezelő konzol** lehetőséget. 

1. A-konzolon futtassa a következő [Install-Package](/nuget/tools/ps-ref-install-package) parancsot a tárolási bővítmények telepítéséhez:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a `name` kötést használhatja a függvény aláírása attribútumként való eléréséhez. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

A rendszer létrehoz egy nevű új várólistát a `outqueue` Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Cloud Explorer használatával ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

## <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studióban a **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

1. A **Cloud Explorerben** bontsa ki az Azure-előfizetések és a **Storage-fiókok** csomópontot, majd bontsa ki a függvény által használt Storage-fiókot. Ha nem emlékszik a Storage-fiók nevére, tekintse `AzureWebJobsStorage` *meg alocal.settings.js* fájljának kapcsolatok karakterlánc-beállítását.  

1. Bontsa ki a **várólisták** csomópontot, majd kattintson duplán a " **Dequeue** " nevű várólistára a várólista tartalmának megtekintéséhez a Visual Studióban. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Közzététel** lehetőséget, majd válassza a **Közzététel** lehetőséget a projekt újbóli közzétételéhez az Azure-ban.

1. Az üzembe helyezés befejezése után újra használhatja a böngészőt az újratelepített függvény teszteléséhez. Ahogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez.

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. További információ a függvények fejlesztéséről: [Azure functions fejlesztése a Visual Studióval](functions-develop-vs.md).

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[előző rövid útmutató – cikk]: functions-create-your-first-function-visual-studio.md