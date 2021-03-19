---
title: Batch-teszt TSV formátuma – QnA Maker
titleSuffix: Azure Cognitive Services
description: A Batch-tesztek TSV formátumának megismerése
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132126"
---
# <a name="batch-testing-tsv-format"></a>Batch-tesztelés TSV formátuma

A Batch-teszt [forráskódból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) vagy [letölthető végrehajtható zip-fájlból](https://aka.ms/qna_btzip)érhető el. A Batch-teszt futtatására szolgáló parancs formátuma a következő:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Várt érték|
|--|--|
|1|[TSV-beviteli mezőkkel](#tsv-input-fields) formázott TSV-fájl neve|
|2|A végpont URI-ja a QnA Maker portál közzététel lapjáról.|
|3|VÉGPONT – kulcs, a QnA Maker portál közzététel lapján található.|
|4|a Batch test által az eredményekhez létrehozott TSV-fájl neve.|

A következő információk segítségével megismerheti és implementálhatja a Batch-teszteléshez használható TSV formátumot. 

## <a name="tsv-input-fields"></a>TSV beviteli mezők

|TSV bemeneti fájl mezői|Jegyzetek|
|--|--|
|KBID|A TUDÁSBÁZIS-azonosító a közzétételi oldalon található.|
|Kérdés|A felhasználó által megadott kérdés.|
|Metaadatcímkék|választható|
|Felső paraméter|választható| 
|Várt válasz azonosítója|választható|

![A kötegelt teszteléshez használható TSV-fájl bemeneti formátuma.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV kimeneti mezők 

|TSV kimeneti fájl paraméterei|Jegyzetek|
|--|--|
|KBID|A TUDÁSBÁZIS-azonosító a közzétételi oldalon található.|
|Kérdés|A bemeneti fájlban megadott kérdés.|
|Válasz|Az Ön tudásbázisának legfontosabb válasza.|
|Válasz azonosítója|Válasz azonosítója|
|Pontszám|A válasz előrejelzési pontszáma. |
|Metaadatcímkék|visszaadott válasz társítva|
|Várt válasz azonosítója|nem kötelező (csak akkor, ha a várt válasz azonosítója meg van adva)|
|Ítélet címkéje|nem kötelező, az értékek a következőek lehetnek: helyes vagy helytelen (csak akkor, ha a várt válasz meg van adva)|
