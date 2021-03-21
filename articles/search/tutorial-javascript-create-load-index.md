---
title: 'JavaScript-oktatóanyag: keresés hozzáadása a Web Apps szolgáltatáshoz'
titleSuffix: Azure Cognitive Search
description: Hozzon létre indexet, és importálja a CSV-fájlokat a keresési indexbe a JavaScript használatával a NPM SDK-val @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726914"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 – keresési index létrehozása és betöltése a JavaScript-sel

Folytassa a keresésre alkalmas webhely létrehozásával:
* Keresési erőforrás létrehozása a VS Code bővítménnyel
* Új index létrehozása és az adatimportálás JavaScript használatával a minta parancsfájllal és az Azure SDK-val [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Azure Search erőforrás létrehozása 

Hozzon létre egy új keresési erőforrást a Visual Studio Code-hoz készült [Azure Cognitive Search](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) bővítménnyel.

1. A Visual Studio Code-ban nyissa meg a [tevékenység sávot](https://code.visualstudio.com/docs/getstarted/userinterface), és válassza az Azure ikont. 

1. Az oldalsó sávban kattintson a **jobb gombbal az Azure-előfizetésre** a `Azure: Cognitive Search` területen, és válassza az **új keresési szolgáltatás létrehozása** lehetőséget.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Az oldalsó sávban kattintson a jobb gombbal az Azure-előfizetésre az * * Azure: Cognitive Search * * területen, és válassza a * * új keresési szolgáltatás * * létrehozása lehetőséget.":::

1. A következő információk megadásához kövesse az utasításokat:

    |Adatkérés|Enter|
    |--|--|
    |Adja meg az új Search Service globálisan egyedi nevét.|**Jegyezze fel ezt a nevet**. Ez az erőforrás neve az erőforrás-végpont részévé válik.|
    |Válasszon ki egy erőforráscsoportot az új erőforrásokhoz|Használja az oktatóanyaghoz létrehozott erőforráscsoportot.|
    |Válassza ki a keresési szolgáltatáshoz tartozó SKU-t.|Válassza az **ingyenes** lehetőséget ehhez az oktatóanyaghoz. A szolgáltatás létrehozása után nem módosítható az SKU díjszabási szintje.|
    |Válasszon egy helyet az új erőforrásokhoz.|Válasszon ki egy Önhöz közeli régiót.|

1. Az utasítások elvégzése után létrejön az új keresési erőforrás. 

## <a name="get-your-search-resource-admin-key"></a>A keresési erőforrás rendszergazdai kulcsának beolvasása

Szerezze be a keresési erőforrás-adminisztrátori kulcsot a Visual Studio Code bővítménnyel. 

1. A Visual Studio Code-ban az oldalsó sávban kattintson a jobb gombbal a keresési erőforrásra, majd válassza a **felügyeleti kulcs másolása** lehetőséget.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Az oldalsó sávban kattintson a jobb gombbal a keresési erőforrásra, és válassza a * * rendszergazdai kulcs másolása * * elemet.":::

1. Tartsa meg ezt az adminisztrátori kulcsot, majd [egy későbbi szakaszban](#prepare-the-bulk-import-script-for-search)kell használnia. 

## <a name="prepare-the-bulk-import-script-for-search"></a>A tömeges importálási parancsfájl előkészítése a kereséshez

A szkript a Cognitive Search készült Azure SDK-t használja:

* [NPM-csomag @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Referenciadokumentáció](/javascript/api/overview/azure/search-documents-readme)

1. A Visual Studio Code-ban nyissa meg a `bulk_insert_books.js` fájlt az alkönyvtárban, majd  `search-web/bulk-insert` cserélje le a következő változókat a saját értékeire a Azure Search SDK-val való hitelesítéshez:

    * SAJÁT KERESÉSI ERŐFORRÁS NEVE
    * A-SEARCH-ADMIN-KEY

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Nyisson meg egy integrált terminált a Visual Studióban a projekt könyvtára alkönyvtárában, `search-web/bulk-insert` és futtassa a következő parancsot a függőségek telepítéséhez. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>A tömeges importálási parancsfájl futtatása a kereséshez

1. Folytassa az integrált terminál használatát a Visual Studióban a projekt könyvtára alkönyvtárában, és `search-web/bulk-insert` futtassa a következő bash-parancsot a szkript futtatásához `bulk_insert_books.js` :

    ```javascript
    npm start
    ```

1. A kód futtatásakor a konzol megjeleníti a folyamatot. 
1. A feltöltés befejezésekor a konzolra kinyomtatott utolsó utasítás "kész".

## <a name="review-the-new-search-index"></a>Az új keresési index áttekintése

A feltöltés befejeződése után a keresési index készen áll a használatra. Tekintse át az új indexet.

1. A Visual Studio Code-ban nyissa meg az Azure Cognitive Search bővítményt, és válassza ki a keresési erőforrást.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="A Visual Studio Code-ban nyissa meg az Azure Cognitive Search bővítményt, és nyissa meg a keresési erőforrást.":::

1. Bontsa ki az indexek, majd a dokumentumok, majd `good-books` a doc elemet az összes dokumentum-specifikus információ megtekintéséhez.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Bontsa ki az indexek, majd a jó könyvek elemet, majd válasszon ki egy dokumentumot.":::

## <a name="copy-your-search-resource-name"></a>A keresési erőforrás nevének másolása

Jegyezze fel a **keresési erőforrás nevét**. Erre szüksége lesz ahhoz, hogy az Azure Function alkalmazást a keresési erőforráshoz kapcsolódjon. 

> [!CAUTION]
> Előfordulhat, hogy a keresési rendszergazdai kulcs az Azure-függvényben való használatának megkísértése mellett nem a legalacsonyabb jogosultsági szint elvét követik. Az Azure-függvény a lekérdezési kulcsot fogja használni a legalacsonyabb jogosultsági szintű megfeleléshez. 

## <a name="next-steps"></a>Következő lépések

[A statikus webalkalmazás üzembe helyezése](tutorial-javascript-deploy-static-web-app.md)