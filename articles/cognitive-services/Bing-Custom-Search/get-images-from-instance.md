---
title: Rendszerképek lekérése az egyéni nézet – Bing egyéni keresés
titleSuffix: Azure Cognitive Services
description: Rendszerképek lekérése a webes egyéni nézetének Bing Custom Search használatával magas szintű áttekintése.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390332"
---
# <a name="get-images-from-your-custom-view"></a>Rendszerképek lekérése az egyéni nézet

Bing – egyéni rendszerképek keresés lehetővé teszi, hogy az egyéni keresési funkciókat képekkel gazdagabbá teheti. A webes találatokhoz hasonlóan az egyéni keresés a képkeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. A képek, az egyéni lemezképek keresési Bing-API használatával, vagy a tárolt felhasználói felületen funkciót. A felhasználói felületi üzemeltetett szolgáltatás használata könnyen használható és a keresési funkciót és röviden rendelés ajánlott.  A tárolt felhasználói felület képeket konfigurálásával kapcsolatos további információkért lásd: [konfigurálása a központi felhasználói felület](hosted-ui.md).

Ha szeretné jobban szabályozhatja a keresési eredményekkel, használhatja az egyéni lemezképek keresési Bing-API. Mivel az API meghívása hasonlít a Bing Image Search API hívása kivétele [a Bing Képkeresés](../Bing-Image-Search/overview.md) példák az API-t hívná. Azonban mielőtt ezt megtenné, ismerje meg az a [egyéni rendszerképek keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) tartalmat. A legfontosabb különbségek a következők: a támogatott lekérdezési paraméterek (meg kell adni a customConfig lekérdezési paraméter) és a végpont kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
