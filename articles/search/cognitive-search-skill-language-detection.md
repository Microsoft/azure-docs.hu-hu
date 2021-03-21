---
title: Nyelvfelismerés – kognitív képességek
titleSuffix: Azure Cognitive Search
description: Kiértékeli a strukturálatlan szöveget, és minden egyes rekord esetében egy olyan nyelvi azonosítót ad vissza, amely az Azure Cognitive Search AI-dúsítási folyamatában lévő elemzés erősségét jelzi.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548053"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelvfelismerés – kognitív képességek

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. A nyelvi kód párosítva van egy pontszámmal, amely az elemzés erősségét jelzi. Ez a képesség a Cognitive Services [text Analytics](../cognitive-services/text-analytics/overview.md) által biztosított gépi tanulási modelleket használja.

Ez a képesség különösen akkor hasznos, ha meg kell adnia a szöveg nyelvét más képességeknek (például a [Hangulatelemzés skill](cognitive-search-skill-sentiment.md) vagy a [text Split skill](cognitive-search-skill-textsplit.md)).

A nyelvfelismerés a Bing természetes nyelvi feldolgozó kódtárait használja, ami meghaladja a [támogatott nyelvek és régiók](../cognitive-services/text-analytics/language-support.md) számát Text Analytics. A nyelvek pontos listája nincs közzétéve, de az összes széles körben beszélt nyelvet, valamint a változatokat, a dialektusokat és néhány regionális és kulturális nyelvet tartalmaz. Ha a tartalom ritkábban használt nyelven van kifejezve, [kipróbálhatja a NYELVFELISMERÉS API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) -t, és megnézheti, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `(Unknown)` .

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](/dotnet/api/system.string.length) . Ha meg kell szakítania az adatokat, mielőtt elküldené azt a nyelvfelismerés szaktudásának, használhatja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md)is.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bevitelek | Description |
|---------------------|-------------|
| `defaultCountryHint` | Választható Ha a nyelv nem egyértelműsítse, az ISO 3166-1 Alpha-2 2 Letter országkód adható meg a nyelvi észlelési modellre mutató tippként. További részletekért tekintse meg a jelen témakör [text Analytics dokumentációját](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) . Pontosabban a `defaultCountryHint` paramétert olyan dokumentumokkal együtt használják, amelyek nem adják meg `countryHint` explicit módon a bemenetet.  |
| `modelVersion`   | Választható A Text Analytics szolgáltatás meghívásakor használni kívánt modell verziója. Alapértelmezés szerint a legújabb elérhető, ha nincs megadva. Azt javasoljuk, hogy csak akkor válassza ezt az értéket, ha feltétlenül szükséges. További részletekért tekintse meg [a Text Analytics API modell verziószámozását](../cognitive-services/text-analytics/concepts/model-versioning.md) ismertető témakört. |

## <a name="skill-inputs"></a>Szaktudás bemenetei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bevitelek     | Description |
|--------------------|-------------|
| `text` | Az elemezni kívánt szöveg.|
| `countryHint` | ISO 3166-1 Alpha-2 2 Letter országkód, amely a nyelvfelismerés-modellre mutató célzásként használható, ha a nyelv nem egyértelműsítse. További részletekért tekintse meg a jelen témakör [text Analytics dokumentációját](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) . |

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Description |
|--------------------|-------------|
| `languageCode` | Az ISO 6391 nyelvi kódja az azonosított nyelvhez. Például: "en". |
| `languageName` | A nyelv neve. Például: "English". |
| `score` | 0 és 1 közötti érték. A nyelv helyes azonosításának valószínűsége. A pontszám értéke 1-nél kisebb lehet, ha a mondat vegyes nyelvekkel rendelkezik.  |

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Példabemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Példakimenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)