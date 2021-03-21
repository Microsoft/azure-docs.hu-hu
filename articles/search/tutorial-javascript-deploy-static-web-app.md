---
title: 'JavaScript-oktatóanyag: Search-kompatibilis webhely üzembe helyezése'
titleSuffix: Azure Cognitive Search
description: Search-kompatibilis webhely üzembe helyezése az Azure statikus webalkalmazásban.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726893"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 – a Search-kompatibilis webhely üzembe helyezése

A keresésre alkalmas webhely üzembe helyezése Azure-beli statikus webalkalmazásként. Ez az üzembe helyezés magában foglalja az alkalmazás és a Function alkalmazást is.  

A statikus webalkalmazás lekéri az információkat és a fájlokat a GitHubról történő üzembe helyezéshez a minták tárházának elágazásával.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Statikus webes alkalmazás létrehozása a Visual Studio Code-ban

1. Válassza az **Azure** lehetőséget a tevékenység sávjában, majd válassza a **statikus Web Apps** lehetőséget az oldal sávján. 
1. Kattintson a jobb gombbal az előfizetés nevére, majd válassza a **statikus webes alkalmazás létrehozása (speciális)** lehetőséget.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Kattintson a jobb gombbal az előfizetés nevére, majd válassza a * * statikus webes alkalmazás létrehozása (speciális) * * elemet.":::

1. A következő információk megadásához kövesse az utasításokat:

    |Adatkérés|Enter|
    |--|--|
    |Hogyan kíván létrehozni statikus webalkalmazást?|Meglévő GitHub-adattár használata|
    |Szervezet kiválasztása|Válassza ki a _saját_ GitHub-aliasát szervezetként.|
    |Adattár kiválasztása|Válassza az **Azure-Search-JavaScript-Samples** elemet a listából. |
    |Tárház-ág kiválasztása|Válassza ki a **főkiszolgálót** a listából. |
    |Adja meg az új statikus webalkalmazás nevét.|Hozzon létre egy egyedi nevet az erőforrás számára. Például megadhatja a nevét az adattár neveként, például: `joansmith-azure-search-javascript-samples` . |
    |Válasszon ki egy erőforráscsoportot az új erőforrásokhoz.|Használja az oktatóanyaghoz létrehozott erőforráscsoportot.|
    |Az alapértelmezett projekt struktúrájának konfigurálásához válassza a Build beállításkészlet lehetőséget.|**Egyéni** kijelölése|
    |Adja meg az alkalmazás kódjának helyét|`search-website`|
    |Adja meg az Azure-függvény kódjának helyét|`search-website/api`|
    |Adja meg a Build kimenetének elérési útját...|építeni|
    |Válasszon egy helyet az új erőforrásokhoz.|Válasszon ki egy Önhöz közeli régiót.|

1. Az erőforrás létrejött, válassza a **műveletek megnyitása a githubban** lehetőséget az értesítések közül. Ekkor megnyílik egy böngészőablak, amely az elágazó tárházra mutat. 

    A műveletek listája azt jelzi, hogy a webalkalmazást, az ügyfelet és a függvényeket is sikeresen leküldte az Azure-beli statikus webalkalmazásba. 

    A folytatás előtt várjon, amíg a build és az üzembe helyezés befejeződik. Ez egy-két percet is igénybe vehet.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Cognitive Search lekérdezési kulcs beolvasása a Visual Studio Code-ban

1. A Visual Studio Code-ban nyissa meg a [tevékenység sávot](https://code.visualstudio.com/docs/getstarted/userinterface), és válassza az Azure ikont. 

1. Az oldalsó sávban válassza ki az Azure-előfizetését az **Azure: Cognitive Search** területen, majd kattintson a jobb gombbal a keresési erőforrásra, majd válassza a **lekérdezési kulcs másolása** menüpontot. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Az oldalsó sávban válassza ki az Azure-előfizetését az * * Azure: Cognitive Search * * területen, majd kattintson a jobb gombbal a keresési erőforrásra, és válassza a * * másolás lekérdezési kulcs * * elemet.":::

1. Tartsa meg ezt a lekérdezési kulcsot, majd a következő szakaszban kell használnia. A lekérdezési kulcs le tudja kérdezni az indexet. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Konfigurációs beállítások hozzáadása a Visual Studio Code-ban

Az Azure Function alkalmazás nem ad vissza keresési adatokat, amíg a keresési titkok nincsenek a beállításokban. 

1. Válassza az **Azure** lehetőséget a tevékenység sávjában, majd válassza a **statikus Web Apps** lehetőséget az oldal sávján. 
1. Bontsa ki az új statikus webalkalmazást, amíg az **Alkalmazásbeállítások** meg nem jelennek.
1. Kattintson a jobb gombbal az **Alkalmazásbeállítások** elemre, majd válassza az **új beállítás hozzáadása** lehetőséget.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Kattintson a jobb gombbal a * * Alkalmazásbeállítások * * elemre, majd válassza az * * új beállítás hozzáadása * * elemet.":::

1. Adja hozzá a következő beállításokat:

    |Beállítás|A keresési erőforrás értéke|
    |--|--|
    |SearchApiKey|Keresési lekérdezési kulcs|
    |SearchServiceName|A keresési erőforrás neve|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>A keresés használata a statikus webalkalmazásban

1. A Visual Studio Code-ban nyissa meg a [tevékenység sávot](https://code.visualstudio.com/docs/getstarted/userinterface), és válassza az Azure ikont.
1. Az oldalsó sávban kattintson a **jobb gombbal az Azure-előfizetésre** a `Static web apps` területen, és keresse meg az oktatóanyaghoz létrehozott statikus webalkalmazást.
1. Kattintson a jobb gombbal a statikus webalkalmazás nevére, és válassza a **Tallózás webhely** lehetőséget.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Kattintson a jobb gombbal a statikus webalkalmazás nevére, és válassza a * * Tallózás webhely * * elemet.":::    

1. Kattintson a **Megnyitás** lehetőségre az előugró ablakban.
1. A webhely keresési sávjában adjon meg egy keresési lekérdezést, például: `code` , _lassan_ , így a javasolt funkció a könyv címeit javasolja. Válasszon ki egy javaslatot, vagy folytassa a saját lekérdezés beírásával. Nyomja le az ENTER billentyűt a keresési lekérdezés befejezését követően. 
1. Tekintse át az eredményeket, majd válassza ki az egyik könyvet a további részletek megtekintéséhez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott erőforrások törléséhez törölje az erőforráscsoportot.

1. A Visual Studio Code-ban nyissa meg a [tevékenység sávot](https://code.visualstudio.com/docs/getstarted/userinterface), és válassza az Azure ikont. 

1. Az oldalsó sávban kattintson a **jobb gombbal az Azure-előfizetésre** a `Resource Groups` területen, és keresse meg az oktatóanyaghoz létrehozott erőforráscsoportot.
1. Kattintson a jobb gombbal az erőforráscsoport nevére, majd válassza a **Törlés** lehetőséget.
    Ezzel törli a keresési és a statikus webalkalmazás-erőforrásokat is.
1. Ha már nem szeretné a minta GitHub-elágazását használni, ne felejtse el törölni a GitHubon. Nyissa meg az elágazás **beállításait** , majd törölje az elágazást. 


## <a name="next-steps"></a>Következő lépések

* [A Search-kompatibilis webhely keresési integrációjának megismerése](tutorial-javascript-search-query-integration.md)
