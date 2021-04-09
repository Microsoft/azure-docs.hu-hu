---
title: A Nyelvfelismerés tároló példányának ellenőrzése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan ellenőrizheti a Nyelvfelismerés tároló példányát.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009895"
---
### <a name="verify-the-language-detection-container-instance"></a>A Nyelvfelismerés tároló példányának ellenőrzése

1. Válassza az **Áttekintés** lapot, és másolja ki az IP-címet.
1. Nyisson meg egy új böngésző fület, és adja meg az IP-címet. Írja be például a `http://<IP-address>:5000 (http://55.55.55.55:5000` következőt:). Megjelenik a tároló kezdőlapja, amelyből megtudhatja, hogy fut-e a tároló.

    ![A tároló kezdőlapjának megtekintése annak ellenőrzéséhez, hogy fut-e](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Válassza ki a **szolgáltatás API leírása** hivatkozást, hogy megnyissa a tároló hencegő lapját.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás** lehetőséget. A paraméterek megjelennek, amely tartalmazza a következő bemeneti példát:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. **ShowStats** beállítása a következőre: `true` .

1. A szöveg hangulatának meghatározásához válassza a **végrehajtás** lehetőséget.

    A tárolóban csomagolt modell egy 0 és 1 közötti értéket generál, ahol a 0 negatív hangulat, az 1 pedig pozitív hangulat.

    A visszaadott JSON-válasz magában foglalja a frissített szövegbeviteli adatok hangulatát:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Most már a megfelelő módon összekapcsolhatók a válasz adattartalmának JSON-adatai és az eredeti kérelem adattartalmát tartalmazó dokumentumai `id` . Minden dokumentumot egymástól függetlenül kezelnek, például a és a különböző statisztikáit `characterCount` `transactionCount` . Emellett minden egyes létrejövő dokumentum rendelkezik a `detectedLanguages` `name` `iso6391Name` (,) és az `score` összes észlelt nyelv tömbével. Ha a rendszer több nyelvet észlel, a a `score` legvalószínűbb nyelv meghatározására szolgál.