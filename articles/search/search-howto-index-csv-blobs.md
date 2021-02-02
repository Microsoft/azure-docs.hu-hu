---
title: Keresés CSV-Blobok között
titleSuffix: Azure Cognitive Search
description: CSV-fájl kinyerése és importálása az Azure Blob Storage-ból a delimitedText-elemzési mód használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430479"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>CSV-Blobok indexelése delimitedText-elemzési mód és blob-indexek használatával az Azure-ban Cognitive Search

Az Azure Cognitive Search [blob Indexer](search-howto-indexing-azure-blob-storage.md) olyan `delimitedText` elemzési módot biztosít a CSV-fájlokhoz, amely a CSV-vonalakat külön keresési dokumentumként kezeli. Például a következő vesszővel tagolt szöveg miatt a `delimitedText` keresési index két dokumentumot fog eredményezni: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Az `delimitedText` elemzési mód nélkül a CSV-fájl teljes tartalma egyetlen keresési dokumentumként lesz kezelve.

Ha egyetlen blobból több keresési dokumentumot hoz létre, ügyeljen arra, hogy az [indexelési Blobok segítségével több keresési dokumentumot készítsen](search-howto-index-one-to-many-blobs.md) , hogy megtudja, hogyan működnek a dokumentumok kulcsának hozzárendelése. A blob indexelő képes olyan értékek megkeresésére vagy létrehozására, amelyek egyedileg határozzák meg az egyes új dokumentumokat. Pontosabban létrehozhat egy átmeneti értéket, `AzureSearch_DocumentKey` amely akkor jön létre, amikor egy blobot kisebb részekre kell elemezni, ahol a rendszer ezt az értéket használja a keresési dokumentum kulcsaként az indexben.

## <a name="setting-up-csv-indexing"></a>CSV-indexelés beállítása

CSV-Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a `delimitedText` [create Indexer](/rest/api/searchservice/create-indexer) -kérelem elemzési módjával:

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` azt jelzi, hogy az egyes Blobok első (nem üres) sora fejléceket tartalmaz.
Ha a Blobok nem tartalmaznak kezdeti fejlécet, a fejléceket az indexelő konfigurációjában kell megadni: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

A elválasztó karaktert a konfigurációs beállítás használatával szabhatja testre `delimitedTextDelimiter` . Például:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Jelenleg csak az UTF-8 kódolás támogatott. Ha más kódolások támogatására van szüksége, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search)-on.

> [!IMPORTANT]
> Ha a tagolt szöveges elemzési módot használja, az Azure Cognitive Search feltételezi, hogy az adatforrásban lévő összes blob CSV-fájl lesz. Ha a CSV-és a nem CSV-Blobok együttes használatát is meg kell adni ugyanabban az adatforrásban, akkor szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Példák kérése

Mindez együttesen a teljes hasznos adatokat mutatja. 

DataSource 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexelő

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


