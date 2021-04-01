---
title: A dekorációs jelölők használata a szöveg kiemeléséhez – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a szövegeket, és kiemelheti a keresési eredményeket a Bing Web Search API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351878"
---
# <a name="using-decoration-markers-to-highlight-text"></a>A dekorációs jelölők használata a szöveg kiemeléséhez

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing támogatja a találatok kiemelését, amelyek bizonyos válaszok megjelenítési karakterláncában a lekérdezési kifejezéseket (vagy a Bing által megtalált egyéb kifejezéseket) jelölik. Előfordulhat például, hogy egy weblap eredményének `name` , `displayUrl` és `snippet` mezőinek megjelölése megjelölt lekérdezési feltételeket tartalmaz. 

Alapértelmezés szerint a Bing nem tartalmazza a megjelenítési karakterláncok kiemelését jelölőket. A jelölők engedélyezéséhez foglalja bele a `textDecorations` lekérdezési paramétert a kérelembe, és állítsa be a következőre: `true` .

## <a name="hit-highlighting-example"></a>Találatok kiemelése példa

Az alábbi példa egy webes eredményt mutat be `Sailing Dinghy` . A Bing a lekérdezési kifejezés kezdetét és végét a E000 és a E001 Unicode-karakterek használatával jelölte meg.
  
![Találatok kiemelése](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Az eredménynek a felhasználói felületen való megjelenítése előtt cserélje le azokat a Unicode-karaktereket, amelyek megfelelnek a megjelenítési formátumnak.

## <a name="marker-formatting"></a>Jelölő formázása

A Bing lehetővé teszi, hogy Unicode-karaktereket vagy HTML-címkéket jelölőként használjon. A használni kívánt jelölők megadásához adja meg a [Szövegformátum](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) lekérdezési paramétert: 

| Érték             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-karakterek (alapértelmezett) |
| `textFormat=HTML` | HTML-karakterek              |

## <a name="additional-text-decorations"></a>További szöveg-kitüntetések

A Bing számos különböző szöveget is visszaadhat. A `Computation` Válasz tartalmazhat például alszkriptet jelölőket a lekérdezési kifejezéshez `log(2)` a `expression` mezőben.

![számítási jelölők](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Ha a kérelem nem adott meg kitüntetéseket, a `expression` mező a következőt fogja tartalmazni: `log10(2)` . 

Ha `textDecorations` a a `true` , a Bing az alábbi jelölőket is tartalmazhatja a válaszok megjelenítési karakterláncában. Ha nincs megfelelő HTML-címke, a táblázatcella üres.

|Unicode|HTML|Leírás
|-|-|-
|U + E000|\<b>|A lekérdezési időszak elejét jelöli (találatok kiemelése)
|U + E001|\</b>|A lekérdezési kifejezés végét jelöli
|U + E002|\<i>|A dőlt tartalom elejét jelöli 
|U + E003|\</i>|A dőlt tartalom végét jelöli
|U + E004|\<br/>|Sortörést jelöl
|U + E005||Egy telefonszám elejét jelöli
|U + E006||Egy telefonszám végét jelöli
|U + E007||A címek kezdetét jelöli.
|U + E008||A címek végét jelöli
|U + E009|\&nbsp;|Nem feltört helyet jelöl
|U + E00C|\<strong>|Félkövér tartalom kezdetét jelöli.
|U + E00D|\</strong>|A félkövér tartalom végét jelöli
|U + E00E||Azon tartalom kezdetének jelzése, amelynek a hátterének világosabbnak kell lennie, mint a környező háttérnek
|U + E00F||Azon tartalom végét jelöli, amelynek a hátterének világosabbnak kell lennie, mint a környező háttérnek
|U + E010||Azon tartalom kezdetét jelöli, amelynek a hátterének sötétebbnek kell lennie, mint a környező háttérnek
|U + E011||Azon tartalom végét jelzi, amelynek a hátterének sötétebbnek kell lennie, mint a környező háttérnek
|U + E012|\<del>|A tartalom elejének megjelölését jelzi
|U + E013|\</del>|A tartalom azon végét jelöli, amelyet át kell húzni
|U + E016|\<sub>|Az alszkript tartalmának elejét jelöli
|U + E017|\</sub>|Az alszkript tartalmának végét jelöli
|U + E018|\<sup>|A felső indexű tartalom elejét jelöli
|U + E019|\</sup>|A felső szintű tartalom végét jelöli

## <a name="next-steps"></a>Következő lépések

* [Mi a Bing Web Search API?](overview.md) 
* [Miniatűrök átméretezése és körülvágása](resize-and-crop-thumbnails.md)