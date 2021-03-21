---
title: 'JavaScript-oktatóanyag: az integrációs lehetőségek keresése'
titleSuffix: Azure Cognitive Search
description: A Search-kompatibilis webhelyen használt JavaScript SDK keresési lekérdezések ismertetése
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726875"
---
# <a name="4---search-integration-highlights"></a>4 – a keresés integrációjának Kiemelt jellemzői

Az előző leckében a keresés egy statikus webalkalmazásba lett hozzáadva. Ez a lecke kiemeli az integrációt kiépítő alapvető lépéseket. Ez a cikk azt ismerteti, hogy miként integrálható a keresés a JavaScript-alkalmazásba, ez a cikk leírja, hogy mit kell tudnia.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

A Function alkalmazás a Cognitive Search Azure SDK-t használja:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Hivatkozási dokumentáció: [ügyféloldali kódtár](/javascript/api/overview/azure/search-documents-readme)

A Function alkalmazás az SDK-n keresztül hitelesíti a felhőalapú Cognitive Search API-t az erőforrás neve, az erőforrás kulcsa és az index neve alapján. A titkos kulcsokat a rendszer a statikus webalkalmazás beállításaiban tárolja, és környezeti változókként behúzta a függvénybe. 

## <a name="configure-secrets-in-a-configuration-file"></a>Titkos kódok konfigurálása konfigurációs fájlban

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure-függvény: Keresés a katalógusban

Az `Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) keresési kifejezést használ, és a keresési indexben lévő dokumentumokon keres, és a találatok listáját adja vissza. 

A keresési API-hoz tartozó útválasztás a kötések [function.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) található.

Az Azure-függvény lekéri a keresési konfigurációs adatokat, és teljesíti a lekérdezést.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Ügyfél: Keresés a katalógusból

Hívja meg az Azure-függvényt az reagáló ügyfélen a következő kóddal. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure-függvény: a katalógus javaslatai

Az `Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) keresési kifejezést használ a felhasználó begépelése közben, és javaslatokat tesz a keresési kifejezéseket, például a könyv címeit és a szerzőket a keresési indexben lévő dokumentumok között, és egy kis listát ad vissza a találatok közül. 

A keresési javaslat a `sg` , amelyet a rendszer a tömeges feltöltés során használt [Schema fájlban](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) definiál.

Az ajánlott API-hoz tartozó útválasztás a kötések [function.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) található.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Ügyfél: a katalógus javaslatai

A következő függvény API-ját a reakciós alkalmazásban az `\src\components\SearchBar\SearchBar.js` összetevő inicializálásának részeként kell meghívni:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure-függvény: adott dokumentum lekérése 

Az `Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) fogadja az azonosítót, és a keresési indexből visszaadja a dokumentum objektumot. 

A keresési API-hoz tartozó útválasztás a kötések [function.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) található.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Ügyfél: adott dokumentum lekérése 

Ezt a függvény API-t a reakciós alkalmazás az `\src\pages\Details\Detail.js` összetevő inicializálásának részeként hívja meg:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Következő lépések

* [Az Azure SQL-adatkészletek indexelése](search-indexer-tutorial.md)
