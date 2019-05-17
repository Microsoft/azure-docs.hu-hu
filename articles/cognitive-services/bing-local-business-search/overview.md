---
title: Mi az a Bing Local Business Search API? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: A Bing Local Business Search API egy RESTful szolgáltatás, amellyel az alkalmazások a közeli helyekkel és vállalkozásokkal kapcsolatos információkat találhatnak keresési lekérdezések alapján.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 3cc9ed4dd108e76da6430a450876b709be514356
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796768"
---
# <a name="what-is-bing-local-business-search"></a>Mi az helyi vállalati keresési Bing?
A helyi vállalati keresési Bing-API egy RESTful szolgáltatás, amely lehetővé teszi az alkalmazások helyi üzletek keresési lekérdezések alapján információ. Ha például `q=<business-name> in Redmond, Washington`, vagy `q=Italian restaurants near me`. 

## <a name="features"></a>Funkciók
| Szolgáltatás | Leírás |  
| -- | -- | 
| [Keresse meg a helyi üzleteket és helyek](quickstarts/local-quickstart.md) | A helyi vállalati keresési Bing-API honosított eredmények olvas be egy lekérdezést. Eredmények a vállalati webhely URL-címet és szöveget, a telefonszám és a földrajzi hely, többek között: GPS koordinálja, város, utca, házszám |  
| [A földrajzi határokon helyi eredmények szűréséhez](specify-geographic-search.md) | Keresési paraméterek használatával korlátozza az eredményeket egy adott földrajzi területre, egy kör alakú területen vagy szögletes határolókeret megadott koordinátáit adja hozzá. | 
| [Helyi üzleti eredmények szűrés kategória szerint](local-categories.md) | Keresse meg a helyi üzleti eredmények kategória szerint. Ez a beállítás a fordított IP-helyre vagy a hívó GPS-koordinátáit honosított találatot, üzleti különböző kategóriákban használja.|

## <a name="workflow"></a>Munkafolyamat
A helyi vállalati keresési Bing-API hívása minden programozási nyelvet, amely HTTP-kérelmeket és elemezni a JSON-válaszok. Ez a szolgáltatás érhető el a REST API használatával.
 
1. Hozzon létre egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing Search APIs-hozzáféréssel rendelkező. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL-címet a keresési kulcsszavakat kódolása a `q=""` lekérdezési paraméter. Ha például `q=nearby+restaurant` vagy `q=nearby%20restaurant`. Állítsa be a tördelés is, ha szükséges. 
3. Küldjön egy [kérést a helyi vállalati keresési Bing-API](quickstarts/local-quickstart.md) 
4. A JSON-válasz elemzése 

> [!NOTE]
> Jelenleg a helyi vállalati keresési csak támogatja a `en-US` piacon. 
> [!NOTE]
> Helyi üzleti keresés jelenleg nem támogatja az automatikus kiegészítés. 

## <a name="next-steps"></a>További lépések
- [Lekérdezés és válasz](local-search-query-response.md)
- [Helyi üzleti keresés a rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresési API-referencia](local-search-reference.md)
- [Használati és megjelenítési követelmények](use-display-requirements.md)
