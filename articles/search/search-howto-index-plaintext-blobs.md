---
title: Keresés az egyszerű szöveges Blobok között
titleSuffix: Azure Cognitive Search
description: A keresési indexelő beállítása egyszerű szöveg kinyerésére az Azure-blobokból a teljes szöveges kereséshez az Azure Cognitive Searchban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509452"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Egyszerű szöveges Blobok indexelése az Azure-ban Cognitive Search

Ha blob- [Indexelő](search-howto-indexing-azure-blob-storage.md) használatával Kinyeri a kereshető blob szövegét a teljes szöveges kereséshez, hozzárendelhet egy elemzési módot a jobb indexelési eredmények eléréséhez. Alapértelmezés szerint az indexelő a blob tartalmát egyetlen darab szövegként elemzi. Ha azonban az összes blob egyszerű szöveget tartalmaz ugyanabban a kódolásban, akkor az elemzési mód használatával jelentősen javíthatja az indexelési teljesítményt `text` .

Az elemzés használatára vonatkozó javaslatok a `text` következők:

+ Fájl típusa. txt
+ A fájlok bármilyen típusúak, de maga a tartalom szöveg (például a program forráskódja, HTML, XML stb.). A megjelölés nyelvén található fájlok esetében minden szintaktikai karakter statikus szövegként jelenik meg.

Emlékezzen rá, hogy minden indexelő szerializál a JSON-ra. Alapértelmezés szerint a teljes szövegfájl tartalma egy nagy mezőn belül lesz indexelve `"content": "<file-contents>"` . Minden új sor és visszatérési utasítás be van ágyazva a Content (tartalom) mezőbe, és a következőképpen van megadva: `\r\n\` .

Ha részletesebb eredményt szeretne, és ha a fájl típusa kompatibilis, vegye figyelembe a következő megoldásokat:

+ [`delimitedText`](search-howto-index-csv-blobs.md) elemzési mód, ha a forrás CSV
+ [ `jsonArray` vagy `jsonLines` ](search-howto-index-json-blobs.md)ha a forrás JSON

A tartalmak több részre való feltörésére szolgáló harmadik lehetőség az [AI](cognitive-search-concept-intro.md)-bővítés formájában speciális funkciókra van szükség. Olyan elemzést ad hozzá, amely azonosítja és hozzárendeli a fájl különböző keresési mezőkhöz tartozó részleteit. Teljes vagy részleges megoldást talál a [beépített szakismeretekkel](cognitive-search-predefined-skills.md), de a megoldás egy olyan tanulási modell lenne, amely az egyéni tanulási modellbe belefoglalja a tartalmat, és egy [Egyéni szakértelmet](cognitive-search-custom-skill-interface.md)tartalmaz.

## <a name="set-up-plain-text-indexing"></a>Egyszerű szöveges indexelés beállítása

Az egyszerű szöveges Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a `parsingMode` Configuration tulajdonsággal a `text` [create Indexer](/rest/api/searchservice/create-indexer) -kérelemben:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Alapértelmezés szerint a `UTF-8` rendszer a kódolást feltételezi. Másik kódolás megadásához használja a következő `encoding` konfigurációs tulajdonságot: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Példa kérésre

Az elemzési módok az indexelő definíciójában vannak megadva.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Következő lépések

+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [BLOB-indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md)
+ [A blob indexelésének áttekintése](search-blob-storage-integration.md)