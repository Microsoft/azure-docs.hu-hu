---
title: OData keresés. score-függvény referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a Search. score függvény Azure Cognitive Search lekérdezésekben való használatához.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923976"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData `search.score` függvény az Azure Cognitive Search

Ha a [ **$OrderBy** paraméter](search-query-odata-orderby.md)nélkül küld egy Azure Cognitive Search-lekérdezést, a visszaadott eredmények csökkenő sorrendben lesznek rendezve a relevancia pontszám szerint. Még ha a **$OrderBy** is használja, a rendszer alapértelmezés szerint a relevancia pontszámát fogja használni. Néha azonban hasznos lehet a relevancia pontszámának kezdeti rendezési feltételként való használata, valamint a döntetlen-megszakító más feltételeinek megadása. A `search.score` függvény lehetővé teszi ezt.

## <a name="syntax"></a>Syntax

A $orderby szintaxisa a következő `search.score`  : `search.score()` . A függvény `search.score` nem végez paramétereket. A `asc` (z) vagy a rendezési sorrend megadásával is használható `desc` , ugyanúgy, mint a **$OrderBy** paraméter bármely más záradéka. A rendezési feltételek listájában bárhol megjelenhet.

## <a name="example"></a>Példa

Rendezheti a szállodákat csökkenő sorrendben `search.score` , a és a alapján `rating` , majd növekvő sorrendben, a megadott koordináták távolsága alapján, hogy két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között szerepeljenek a legközelebb:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Következő lépések  

- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search EST API-val&#41;](/rest/api/searchservice/Search-Documents)