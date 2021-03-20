---
title: Egyéni önszuggesztió-javaslatok meghatározása – Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Az egyéni automatikus kiegészítés a keresési élményhez kapcsolódó javasolt keresési lekérdezési karakterláncok listáját adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352014"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Egyéni automatikus kiegészítési élmény konfigurálása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Az egyéni automatikus kiegészítés a keresési élményhez kapcsolódó javasolt keresési lekérdezési karakterláncok listáját adja vissza. A javasolt lekérdezési karakterláncok egy részleges lekérdezési karakterláncon alapulnak, amelyet a felhasználó a keresőmezőbe ír be. A lista legfeljebb 10 javaslatot fog tartalmazni. 

Megadhatja, hogy csak az egyéni javaslatokat adja vissza, vagy a Bing-javaslatokat is tartalmazza. Ha Bing-javaslatokat is tartalmaz, az egyéni javaslatok a Bing-javaslatok előtt jelennek meg. Ha elegendő releváns javaslatot nyújt, lehetséges, hogy a javaslatok visszaadott listája nem tartalmaz Bing-javaslatokat. A Bing-javaslatok mindig az egyéni keresési példány kontextusában jelennek meg. 

A keresési lekérdezési javaslatok a példányra való konfigurálásához kattintson az automatikus **javaslat** lapra.  

> [!NOTE]
> A szolgáltatás használatához elő kell fizetnie az egyéni keresésre a megfelelő szinten (lásd a [díjszabást](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Akár 24 óráig is eltarthat, amíg a javaslatok megjelennek a kiszolgálási végponton (API vagy üzemeltetett felhasználói felület).

## <a name="enable-bing-suggestions"></a>Bing-javaslatok engedélyezése

A Bing-javaslatok engedélyezéséhez kapcsolja be az **automatikus Bing Suggestions** csúszkát a be állásba. A csúszka kék színűvé válik.

## <a name="add-your-own-suggestions"></a>Saját javaslatok hozzáadása

Saját lekérdezési karakterlánc-javaslatok hozzáadásához vegye fel őket a listára a **felhasználó által definiált javaslatok** alatt. Ha hozzáad egy javaslatot a listához, nyomja le az ENTER billentyűt, vagy kattintson az **+** ikonra. Megadhatja a javaslatot bármilyen nyelven. Legfeljebb 5 000 lekérdezési karakterláncra vonatkozó javaslatot adhat hozzá.

## <a name="upload-suggestions"></a>Javaslatok feltöltése

Lehetőség van arra, hogy feltöltse a javaslatok listáját egy fájlból. A fájlnak soronként egy keresési lekérdezési karakterláncot kell tartalmaznia. A fájl feltöltéséhez kattintson a feltöltés ikonra, és válassza ki a feltölteni kívánt fájlt. A szolgáltatás kibontja a javaslatokat a fájlból, és hozzáadja azokat a listához.

## <a name="remove-suggestions"></a>Javaslatok eltávolítása

A lekérdezési karakterláncokra vonatkozó javaslat eltávolításához kattintson az eltávolítani kívánt javaslat melletti eltávolítás ikonra.

## <a name="block-suggestions"></a>Javaslatok letiltása

Ha Bing-javaslatokat is tartalmaz, hozzáadhat egy listát a keresési lekérdezési karakterláncokról, amelyeket nem kíván visszaadni a Bingnek. A letiltott lekérdezési karakterláncok hozzáadásához kattintson a **letiltott javaslatok megjelenítése** lehetőségre. Adja hozzá a lekérdezési karakterláncot a listához, majd nyomja le az ENTER billentyűt, vagy kattintson az **+** ikonra. Legfeljebb 50 letiltott lekérdezési karakterláncot adhat hozzá.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Akár 24 óráig is eltarthat, amíg az egyéni automatikus kiegészítés konfigurációs módosításai érvénybe lépnek.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Automatikus kiegészítés engedélyezése a futtatott felhasználói felületen

Az üzemeltetett felhasználói felület lekérdezési karakterláncára vonatkozó javaslatok engedélyezéséhez kattintson a **futtatott felhasználói felület** lehetőségre. Görgessen le a **további konfigurációs** szakaszhoz. A **webes keresés** területen válassza **a be** lehetőséget az automatikus kiegészítés **engedélyezéséhez**. Az automatikus kiegészítés engedélyezéséhez ki kell választania egy olyan elrendezést, amely tartalmaz egy keresőmezőt.


## <a name="calling-the-autosuggest-api"></a>Az automatikus javaslat API meghívása

Ha a Bing Custom Search API használatával szeretne lekérdezési karakterláncokat beolvasni, küldjön egy `GET` kérelmet a következő végpontnak.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A válasz tartalmazza a `SearchAction` javasolt lekérdezési karakterláncokat tartalmazó objektumok listáját.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Minden javaslat tartalmaz egy `displayText` és egy `query` mezőt. A `displayText` mező tartalmazza azt a javasolt lekérdezési karakterláncot, amelyet a keresőmező legördülő listájának feltöltéséhez használ.

Ha a felhasználó egy javasolt lekérdezési karakterláncot választ a legördülő listából, használja a lekérdezési karakterláncot a `query` mezőben a [Bing Custom Search API](overview.md)hívásakor.


## <a name="next-steps"></a>Következő lépések

- [Egyéni javaslatok lekérése]()
- [Egyéni példány keresése](./search-your-custom-view.md)
- [Egyéni tárolt felhasználói felület konfigurálása és felhasználása](./hosted-ui.md)