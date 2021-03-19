---
title: Nyelvi modell testreszabása Video Indexer API-val
titlesuffix: Azure Media Services
description: Megtudhatja, hogyan szabhatja testre a nyelvi modellt a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047090"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Nyelvi modell testreszabása a Video Indexer API-val

A Video Indexer lehetővé teszi, hogy egyéni nyelvi modelleket hozzon létre a beszédfelismerés testre szabásához az adaptációs szöveg feltöltésével, azaz olyan tartományból származó szöveggel, amelynek a szókincsét szeretné a motorhoz igazítani. A modell betanítása után a rendszer az adaptációs szövegben szereplő új szavakat fogja felismerni.

Az egyéni nyelvi modellekkel kapcsolatos részletes áttekintést és ajánlott eljárásokat lásd: [nyelvi modell testreszabása video Indexer](customize-language-model-overview.md)használatával.

A jelen témakörben leírtak szerint a Video Indexer API-k használatával hozhat létre és szerkeszthet egyéni nyelvi modelleket a fiókjában. Használhatja a webhelyet is a következő témakörben ismertetett módon: a [nyelvi modell testreszabása a video Indexer webhelyén](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

A [nyelvi modell létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API létrehoz egy új egyéni nyelvi modellt a megadott fiókban. Ebben a hívásban tölthet fel fájlokat a nyelvi modellbe. Azt is megteheti, hogy itt létrehozhatja a nyelvi modellt, és később is feltöltheti a modell fájljait a nyelvi modell frissítésével.

> [!NOTE]
> Továbbra is be kell tanítania a modellt a modell engedélyezett fájljaival a fájl tartalmának megismeréséhez. A nyelv tanításának irányai a következő szakaszban találhatók.

A nyelvi modellbe felvenni kívánt fájlok feltöltéséhez a FormData használatával fel kell töltenie a fájlokat a törzsbe a fenti szükséges paraméterek értékének megadása mellett. Ennek a feladatnak két módja van:

* A kulcs a fájlnév és az érték lesz a txt-fájl.
* A kulcs a fájlnév, az érték pedig a txt-fájl URL-címe lesz.

### <a name="response"></a>Reagálás

A válasz metaadatokat biztosít az újonnan létrehozott nyelvi modellhez, valamint a modell összes fájljának metaadatait a példa JSON-kimenetének formátumát követve:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Nyelvi modell betanítása

A [Language Model API betanítása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) egyéni nyelvi modellt használ a megadott fiókban a nyelvi modellben feltöltött és engedélyezett fájlok tartalmával.

> [!NOTE]
> Először létre kell hoznia a nyelvi modellt, és fel kell töltenie a fájljait. A nyelvi modell létrehozásakor vagy a nyelvi modell frissítésével tölthet fel fájlokat.

### <a name="response"></a>Reagálás

A válasz metaadatokat biztosít az újonnan betanított nyelvi modellhez, valamint a modell összes fájljának metaadatait a példa JSON-kimenetének formátumát követve:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

A visszaadott `id` egyedi azonosító, amely a nyelvi modellek megkülönböztetésére szolgál, míg a videók `languageModelId` [feltöltéséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) és a videó API-k [újraindexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (más néven `linguisticModelId` video Indexer feltöltési/újraindexelési API-khoz) is használható.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

A [nyelvi modell törlése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API törli a megadott fiókból származó egyéni nyelvi modellt. A törölt nyelvi modellt használó összes videó esetében Ugyanez az index marad, amíg újra nem indexeli a videót. Ha újraindexeli a videót, hozzárendelhet egy új nyelvi modellt a videóhoz. Ellenkező esetben a Video Indexer az alapértelmezett modellt fogja használni a videó újraindexeléséhez.

### <a name="response"></a>Reagálás

A nyelvi modell törlése után a rendszer nem adott vissza tartalmat.

## <a name="update-a-language-model"></a>Nyelvi modell frissítése

A [Language Model API frissítése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) a megadott fiókban frissíti a Custom Language person modellt.

> [!NOTE]
> Már létre kell hoznia a nyelvi modellt. Ezzel a hívással engedélyezheti vagy letilthatja a modellben található összes fájlt, frissítheti a nyelvi modell nevét, és feltöltheti a nyelvi modellbe felvenni kívánt fájlokat.

A nyelvi modellbe felvenni kívánt fájlok feltöltéséhez a FormData használatával fel kell töltenie a fájlokat a törzsbe a fenti szükséges paraméterek értékének megadása mellett. Ennek a feladatnak két módja van:

* A kulcs a fájlnév és az érték lesz a txt-fájl.
* A kulcs a fájlnév, az érték pedig a txt-fájl URL-címe lesz.

### <a name="response"></a>Reagálás

A válasz metaadatokat biztosít az újonnan betanított nyelvi modellhez, valamint a modell összes fájljának metaadatait a példa JSON-kimenetének formátumát követve:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

A `id` válaszban visszaadott fájlok használatával töltse le a fájl tartalmát.

## <a name="update-a-file-from-a-language-model"></a>Fájl frissítése nyelvi modellből

A [fájl frissítése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) lehetővé teszi `enable` egy fájl nevének és állapotának frissítését egy egyéni nyelvi modellben a megadott fiókban.

### <a name="response"></a>Reagálás

A válasz metaadatokat biztosít az alábbi JSON-kimenet formátumát követően frissített fájlhoz.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Használja a `id` válaszban visszaadott fájl tartalmát a fájl tartalmának letöltéséhez.

## <a name="get-a-specific-language-model"></a>Adott nyelvi modell beszerzése

A [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API adja vissza a megadott nyelvi modell adatait a megadott fiókban, például a nyelvet és a nyelvi modellben lévő fájlokat.

### <a name="response"></a>Reagálás

A válasz metaadatokat biztosít a megadott nyelvi modellhez, valamint a modell összes fájljának metaadatait a példa JSON-kimenetének formátumát követve:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Használja a `id` válaszban visszaadott fájl tartalmát a fájl tartalmának letöltéséhez.

## <a name="get-all-the-language-models"></a>Az összes nyelvi modell lekérése

A [Get all](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API az összes egyéni nyelvi modellt visszaadja egy lista megadott fiókjában.

### <a name="response"></a>Reagálás

A válasz felsorolja a fiókban lévő összes nyelvi modellt, valamint az egyes metaadatokat és fájlokat a példaként megadott JSON-kimenet formátumát követve:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Fájl törlése egy nyelvi modellből

A [delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API törli a megadott fájlt a megadott nyelvi modellből a megadott fiókból.

### <a name="response"></a>Reagálás

A nyelvi modellből való törlés után a rendszer nem adott vissza tartalmat.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Metaadatok beolvasása egy nyelvi modellből származó fájlhoz

A file API-k [metaadatainak beolvasása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) a megadott fájl tartalmát és metaadatait a fiókjában kiválasztott nyelvi modellből adja vissza.

### <a name="response"></a>Reagálás

A válasz JSON formátumban adja meg a fájl tartalmát és metaadatait, a következő példához hasonlóan:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> A példában szereplő fájl tartalma két különálló sorban a "Hello" és a World "szó.

## <a name="download-a-file-from-a-language-model"></a>Fájl letöltése nyelvi modellből

A [Fájl letöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API letölt egy szövegfájlt, amely a megadott fiók megadott nyelvi modelljéből származó adott fájl tartalmát tartalmazza. A szövegfájlnak meg kell egyeznie az eredetileg feltöltött szövegfájl tartalmával.

### <a name="response"></a>Reagálás

A válasz egy szövegfájl letöltését fogja tartalmazni a fájl tartalmával a JSON formátumban.

## <a name="next-steps"></a>Következő lépések

[Nyelvi modell testreszabása webhely használatával](customize-language-model-with-website.md)
