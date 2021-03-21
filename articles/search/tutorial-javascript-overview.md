---
title: 'JavaScript-oktatóanyag: a Search Integration áttekintése'
titleSuffix: Azure Cognitive Search
description: Technikai áttekintés és beállítás a webhelyek keresésének hozzáadásához és az Azure-beli statikus webalkalmazások üzembe helyezéséhez.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726881"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 – a keresésnek a webhelyhez való hozzáadásának áttekintése

Ez az oktatóanyag egy webhelyet hoz létre a könyvek katalógusában való kereséshez, majd üzembe helyezi a webhelyet egy Azure-beli statikus webalkalmazásban. 

Az alkalmazás elérhető: 
* [Sample](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Bemutató webhely – aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Mit tesz a minta? 

Ez a minta webhely hozzáférést biztosít a 10 000-es könyvek katalógusához. A felhasználók a keresési sávban lévő szöveg beírásával kereshetik meg a katalógusban. Amíg a felhasználó szöveget ír be, a webhely a keresési index javaslata funkció használatával fejezi be a szöveget. A lekérdezés befejeződése után a könyvek listája a részletek egy részével jelenik meg. A felhasználók kiválaszthatnak egy könyvet a könyv keresési indexében tárolt összes adat megtekintéséhez. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Ez a minta webhely hozzáférést biztosít a 10 000-es könyvek katalógusához. A felhasználók a keresési sávban lévő szöveg beírásával kereshetik meg a katalógusban. Amíg a felhasználó szöveget ír be, a webhely a keresési index javaslata funkció használatával fejezi be a szöveget. A keresés befejeződése után a könyvek listája a részletek egy részével jelenik meg. A felhasználók kiválaszthatnak egy könyvet a könyv keresési indexében tárolt összes adat megtekintéséhez.":::

A keresési élmény az alábbiakat tartalmazza: 

* Search – keresési funkciókat biztosít az alkalmazáshoz.
* Javaslat – javaslatokat nyújt, mivel a felhasználó begépeli a keresősáv kifejezést.
* Dokumentum-keresés – megkeres egy dokumentumot azonosító alapján, hogy beolvassa az összes tartalmát a Részletek lapra.

## <a name="how-is-the-sample-organized"></a>Hogyan szerveződik a minta?

A [minta](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) a következőket tartalmazza:

|Alkalmazás|Cél|GitHub<br>Adattár<br>Hely|
|--|--|--|
|Ügyfél|Alkalmazás (bemutató réteg) használata a könyvek megjelenítéséhez a kereséssel. Meghívja az Azure Function alkalmazást. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Kiszolgáló|Azure Function app (üzleti réteg) – meghívja az Azure Cognitive Search API-t a JavaScript SDK-val |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Tömeges beszúrás|JavaScript-fájl az index létrehozásához és a dokumentumok hozzáadásához.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Telepítse a következőt a helyi fejlesztési környezetbe. 

- [Node.js 12 vagy 14](https://nodejs.org/en/download)
    - Ha a helyi számítógépen a Node.js különböző verziója van telepítve, érdemes lehet használni a [Node Version Managert](https://github.com/nvm-sh/nvm) (NVM) vagy egy Docker-tárolót.  
- [Git](https://git-scm.com/downloads)
- A [Visual Studio Code](https://code.visualstudio.com/) és a következő bővítmények
    - [Azure Resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups) (Azure-erőforrások)
    - [Azure Cognitive Search 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure-alapú statikus webalkalmazás](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Nem kötelező:
    - Ez az oktatóanyag nem futtatja helyileg az Azure Function API-t, de ha helyileg szeretné futtatni, az [Azure-functions-Core-Tools-](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) t globálisan kell telepítenie a következő bash-paranccsal: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>A keresési minta elágazása és klónozása a git-vel

A minta tárházának elágazása kritikus fontosságú a statikus webes alkalmazás üzembe helyezéséhez. A webalkalmazások a saját GitHub-elágazási helye alapján határozzák meg a fordítási műveleteket és a központi telepítési tartalmat. A statikus webalkalmazásban a kód végrehajtása távoli, és az Azure statikus Web Apps beolvassa a kódot az elágazó mintában.

1. A GitHubon Villa a [minta tárházat](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Töltse ki az elágazási folyamatot a böngészőjében a GitHub-fiókjával. Ez az oktatóanyag egy Azure-beli statikus webalkalmazás üzembe helyezésének részeként használja az elágazást. 

1. A bash-terminálon töltse le a minta alkalmazást a helyi számítógépre. 

    Cserélje le a- `YOUR-GITHUB-ALIAS` t a GitHub-aliasra. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. A Visual Studio Code-ban nyissa meg a klónozott adattár helyi mappáját. A fennmaradó feladatok a Visual Studio Code-ból érhetők el, kivéve, ha meg van adva.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Erőforráscsoport létrehozása Azure-erőforrásokhoz

1. A Visual Studio Code-ban nyissa meg a [tevékenység sávot](https://code.visualstudio.com/docs/getstarted/userinterface), és válassza az Azure ikont. 
1. Az oldalsó sávban kattintson a **jobb gombbal az Azure-előfizetésre** a `Resource Groups` területen, majd válassza az **erőforráscsoport létrehozása** lehetőséget.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Az oldalsó sávban * * kattintson a jobb gombbal az Azure-előfizetésre * * az erőforráscsoportok területen, és válassza az * * erőforráscsoport létrehozása * * elemet.":::
1. Adjon meg egy erőforráscsoport-nevet, például: `cognitive-search-website-tutorial` . 
1. Válasszon ki egy helyet a közelben.
1. A Cognitive Search és a statikus webalkalmazás-erőforrások létrehozásakor az oktatóanyag későbbi részében használja ezt az erőforráscsoportot. 

    Az erőforráscsoportok létrehozásával logikai egység hozható létre az erőforrások kezeléséhez, beleértve a használatuk befejezését is.

## <a name="next-steps"></a>Következő lépések

* [Keresési index létrehozása és betöltés dokumentumokkal](tutorial-javascript-create-load-index.md)
* [A statikus webalkalmazás üzembe helyezése](tutorial-javascript-deploy-static-web-app.md)
