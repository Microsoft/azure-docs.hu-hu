---
title: 'Gyors útmutató: Szöveges tartalom elemzése a Pythonban – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Szöveges tartalmát a Moderator tartalom SDK a Pythonhoz készült használatával számos nem kívánt tartalom elemzése
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 01c153f2f8836b7d99de57af60b8623e54c6d6fe
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311930"
---
[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Gyors útmutató: Szöveges tartalom elemzése a nem kifogásolt anyagok számára a Pythonban

Ez a cikk bemutatja, és Kódminták segítséget nyújtanak a Pythonhoz készült a Content Moderator SDK használatának megkezdéséhez. Megtanulhatja, hogyan végezhet szűrést kifejezések alapján, és hogyan osztályozhatja a szöveges tartalmakat az esetlegesen kifogásolható tartalmak moderálása céljából.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek
- A Content Moderator előfizetői azonosítója. A Content Moderatorra történő előfizetéshez és az előfizetői azonosító beszerzéséhez kövesse a [Cognitive Services-fiók létrehozásával](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kapcsolatos szakaszban található utasításokat.
- [A Python 2.7-es vagy újabb, illetve 3.5-ös vagy újabb verziója](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) eszköz

## <a name="get-the-content-moderator-sdk"></a>A Content Moderator SDK beszerzése

A Content Moderator Python SDK telepítése nyissa meg egy parancssort, és futtassa a következő parancsot:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Modulok importálása

Hozzon létre egy új Python-szkriptet nevű _ContentModeratorQS.py_ , és adja hozzá a következő kódot a szükséges az SDK importálása. A megfelelő nyomtatási modul a JSON-válaszok könnyebb olvasására is használható.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Változók inicializálása

Ezután adja hozzá a Content Moderator előfizetési kulcs és a végpont URL-változókat. A nevet `CONTENT_MODERATOR_SUBSCRIPTION_KEY` hozzá kell adnia a környezeti változókhoz, és hozzá kell adnia az előfizetési kulcsot az értékhez. Az alap végpont URL-címéhez `CONTENT_MODERATOR_ENDPOINT` adja hozzá a környezeti változókat a régióra jellemző URL-cím értékeként, `https://westus.api.cognitive.microsoft.com`például:. Ingyenes próba-előfizetését kulcsokat hoz létre a a **westus** régióban.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Egy fájlból álló többsoros szövegből álló karakterlánc moderálva lesz. Vegye fel a [content_moderator_text_moderation. txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) fájlt a helyi gyökérkönyvtárba, és adja hozzá a fájl nevét a változókhoz:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Lekérdezés a Moderator szolgáltatás

Hozzon létre egy **ContentModeratorClient** példány, az előfizetési kulcs és a végpont URL-cím használatával. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Ezután használja az ügyfelet a tag **TextModerationOperations** -példánnyal, hogy meghívja a moderációs API- `screen_text`t a függvénnyel. Tekintse meg a **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** referenciadokumentációt hogyan hívhat meg, további tájékoztatást.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>A kinyomtatott Válasz keresése

Futtassa a mintát, és erősítse meg a választ. A művelet végrehajtása sikeres volt, és egy **képernyő** -példányt adott vissza. A sikeres eredmény az alábbi formában jelenik meg:

Ez a rövid útmutató eredmények a következő kimenet használt minta szöveg:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy egyszerű Python-szkriptet egy adott szöveg minta releváns információt ad vissza a Content Moderator Service szolgáltatást használó fejlesztett. Következő lépésként többet tudhat meg arról, mit jelentenek a különböző jelzők és besorolások, így eldöntheti, milyen adatokra van szüksége, és hogyan kezelje ezeket az alkalmazása.

> [!div class="nextstepaction"]
> [Szövegmoderálási útmutató](text-moderation-api.md)
