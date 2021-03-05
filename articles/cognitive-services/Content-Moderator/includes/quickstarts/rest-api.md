---
title: Content Moderator REST API gyors útmutató
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Content Moderator REST API. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 346a30b538af8006eaada13b00c77762b9cdfca8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193744"
---
Ismerkedjen meg az Azure Content Moderator REST APIával. 

Content Moderator egy AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos tartalmak kezelését. A AI-alapú Content moderációs szolgáltatással szövegeket, képeket és videókat kereshet, és automatikusan alkalmazhatja a tartalmi jelzőket. Ezután integrálhatja alkalmazását a felülvizsgálati eszközzel, amely egy online moderátori környezet az emberi véleményezők csapatának. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.

Használja a Content Moderator REST APIt a következőre:

* Mérsékelt szöveg
* Közepes méretű képek

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" hozzon létre egy Content moderator erőforrást, "  target="_blank"> és hozzon létre egy Content moderator-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Content Moderatorhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A [PowerShell 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)-s vagy újabb verziója, vagy egy hasonló parancssori alkalmazás.


## <a name="moderate-text"></a>Mérsékelt szöveg

A következőhöz hasonló parancsot kell használnia a Content Moderator API meghívásához a szövegtörzs elemzéséhez és az eredmények a konzolra való kinyomtatásához.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Másolja a parancsot egy szövegszerkesztőbe, és végezze el a következő módosításokat:

1. Rendelje hozzá `Ocp-Apim-Subscription-Key` az érvényes Face előfizetési kulcshoz.
1. Módosítsa a lekérdezés URL-címének első részét úgy, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Tetszés szerint módosíthatja a kérelem törzsét az elemezni kívánt szöveges karakterláncra.

Miután elvégezte a módosításokat, nyisson meg egy parancssort, és írja be az új parancsot. 

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A szöveges moderálás eredményei JSON-adatként jelennek meg a konzol ablakban. Például:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

A (z) Content Moderator képernyőket tartalmazó szöveges attribútumokkal kapcsolatos további információkért tekintse meg a [text moderálási fogalmakat](../../text-moderation-api.md) ismertető útmutatót.

## <a name="moderate-images"></a>Közepes méretű képek

A következőhöz hasonló parancsot kell használnia a Content Moderator API meghívásához a távoli rendszerkép moderálásához és az eredmények a konzolra való kinyomtatásához.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Másolja a parancsot egy szövegszerkesztőbe, és végezze el a következő módosításokat:

1. Rendelje hozzá `Ocp-Apim-Subscription-Key` az érvényes Face előfizetési kulcshoz.
1. Módosítsa a lekérdezés URL-címének első részét úgy, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.
1. Igény szerint módosíthatja a `"Value"` kérelem törzsében lévő URL-címet, hogy a rendszer milyen távoli képeket szeretne mérsékelten.

> [!TIP]
> Emellett mérsékelt helyi képeket is használhat, ha a kérelem törzsében továbbítja a bájtos adatbevitelt. Ennek megismeréséhez tekintse meg a [dokumentációt](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) .

Miután elvégezte a módosításokat, nyisson meg egy parancssort, és írja be az új parancsot. 

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A képek moderálási eredményei JSON-adatként jelennek meg a konzol ablakban. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

További információ a Content Moderator képernyőt tartalmazó képattribútumokról: a [képek moderálási fogalmi](../../image-moderation-api.md) útmutatója.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Content Moderator REST API a moderálási feladatok végrehajtásához. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

* [Képek moderálási fogalmai](../../image-moderation-api.md)
* [Szöveg-moderálási fogalmak](../../text-moderation-api.md)
* [Mi az az Azure Content Moderator?](../../overview.md)