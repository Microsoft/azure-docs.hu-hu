---
title: Egyéni nézet keresése – Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Miután konfigurálta az egyéni keresési élményt, tesztelheti azt a Bing Custom Search-portálon belül.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: 07b0dd68e39f555171e5606b71cd1eec92a4035c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353340"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>A Bing Custom Search-példány meghívása a portálról

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Miután konfigurálta az egyéni keresési élményt, tesztelheti azt a Bing Custom Search- [portálon](https://customsearch.ai)belül. 

![a Bing Custom Search portál képernyőképe](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Keresési lekérdezés létrehozása 

Miután bejelentkezett a Bing Custom Search- [portálra](https://customsearch.ai), válassza ki a keresési példányt, és kattintson a **Production (éles** ) fülre. A **végpontok** területen válasszon ki egy API-végpontot (például a webes API-t). Az előfizetés határozza meg, hogy mely végpontok jelenjenek meg.

Keresési lekérdezés létrehozásához adja meg a végpont paramétereinek értékét. Vegye figyelembe, hogy a portálon megjelenő paraméterek a kiválasztott végponttól függően változhatnak. További információért tekintse meg a [Custom Search API-referenciát](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) . A keresési példány által használt előfizetés módosításához adja hozzá a megfelelő előfizetési kulcsot, és frissítse a megfelelő piaci és/vagy nyelvi paramétereket.

Néhány fontos paraméter a következő:


|Paraméter  |Leírás  |
|---------|---------|
|Lekérdezés     | A keresendő keresési kifejezés. Csak a webes, a képi, a videó-és az automatikus kiegészítési végpontokhoz érhető el |
|Egyéni konfigurációs azonosító | A kiválasztott egyéni keresési példány konfigurációs azonosítója. Ez a mező csak olvasható. |
|Piaci     | Az eredményeket kezdeményező piac. Csak a web, a képek, a videó és a futtatott felhasználói felületi végpontok számára érhető el.        |
|Előfizetői azonosító | A teszteléshez használandó előfizetési kulcs. Kijelölhet egy kulcsot a legördülő listából, vagy megadhat egy manuálisan.          |

Ha **további paraméterekre** kattint, a következő paramétereket mutatja be:  

|Paraméter  |Leírás  |
|---------|---------|
|Biztonságos Keresés     | A felnőtt tartalmak weblapjainak szűrésére szolgáló szűrő. Csak a web, a képek, a videó és a futtatott felhasználói felületi végpontok számára érhető el. Vegye figyelembe, hogy a Bing egyéni Video Search csak két értéket támogat: `moderate` és `strict` .        |
|Felhasználói felület nyelve    | A felhasználói felületi karakterláncokhoz használt nyelv. Ha például az üzemeltetett felhasználói felületen engedélyezi a képeket és a videókat, a **kép** és a **videó** lapok a megadott nyelvet használják.        |
|Darabszám     | A válaszban visszaadni kívánt keresési eredmények száma. Csak webes, képi és videós végpontok esetén érhető el.         |
|Eltolás    | A kihagyni kívánt keresési eredmények száma az eredmények visszaadása előtt. Csak webes, képi és videós végpontok esetén érhető el.        |
    
Miután megadta az összes szükséges beállítást, kattintson a **hívás** gombra a JSON-válasz megtekintéséhez a jobb oldali ablaktáblán. Ha kijelöli a futtatott felhasználói felület végpontját, tesztelheti a keresési felületet az alsó ablaktáblán.

## <a name="change-your-bing-custom-search-subscription"></a>Bing Custom Search-előfizetés módosítása

A Bing Custom Search-példányhoz társított előfizetést új példány létrehozása nélkül módosíthatja. Ha az API-hívásokat egy új előfizetésre szeretné elküldeni és felszámolni, hozzon létre egy új Bing Custom Search-erőforrást a Azure Portal. Használja az új előfizetési kulcsot az API-kérelmekben, valamint a példány egyéni konfigurációs AZONOSÍTÓját.

## <a name="next-steps"></a>Következő lépések

- [Egyéni nézet meghívása C-vel #](./call-endpoint-csharp.md)
- [Egyéni nézet meghívása Javával](./call-endpoint-java.md)
- [Egyéni nézet meghívása a NodeJs](./call-endpoint-nodejs.md)
- [Egyéni nézet meghívása a Pythonban](./call-endpoint-python.md)

- [Egyéni nézet meghívása a C# SDK-val](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)