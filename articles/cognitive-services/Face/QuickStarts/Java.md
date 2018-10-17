---
title: 'Rövid útmutató: Arcfelismerés képen – Face API, Java'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban arcokat fog felismerni egy képről a Face API segítségével és Java használatával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: c43db84bc04dd8fb17f9b3f6e37deba9daced598
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122790"
---
# <a name="quickstart-detect-faces-in-an-image-using-java"></a>Rövid útmutató: Arcfelismerés egy képen Java használatával

Ebben a rövid útmutatóban emberi arcokat fog felismerni egy képen a Face API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához előfizetési kulcs szükséges. Ingyenes próba előfizetési kulcsot itt szerezhet: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) metódussal arcokat ismerhet fel a képeken, és többek között a következő arcattribútumokat adhatja vissza:

* Arcazonosító: Számos Face API-forgatókönyvben használt egyedi azonosító.
* Arcot jelölő téglalap: A képen az arc helyét jelző „bal oldalon, felül, szélesség és hosszúság” érték.
* Jellegzetes pontok: 27 pontból álló jellegzetes pontok egy tömbje, amely az arcösszetevők fontos részeire mutat.
* Az arcattribútumok közé tartozik az életkor, a nem, a mosoly intenzitása, a fejtartás és az arcszőrzet.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Új parancssori alkalmazás létrehozása kedvenc Java IDE környezetében.
2. Cserélje le a Main osztály tartalmát az alábbi kódra (a `package` utasításokat tartsa meg).
3. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
4. Ha szükséges, változtassa meg az `uriBase` értékét arra a helyre, ahonnan az előfizetési kulcsot beszerezte.
5. Töltse le az alábbi globális kódtárakat a Maven-adattárból a projektje `lib` könyvtárába:
   * `org.apache.httpcomponents:httpclient:4.2.4`
   * `org.json:json:20170516`
6. Futtassa a „Main” parancsot.

### <a name="face---detect-request"></a>Face - Detect kérés

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;

public class Main
{
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    public static void main(String[] args)
    {
        HttpClient httpclient = new DefaultHttpClient();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

### <a name="face---detect-response"></a>Face - Detect válasz

A rendszer JSON formátumban adja vissza a sikeres választ.

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>További lépések

Megismerheti, hogyan lehet képen történő arcfelismerésre Face szolgáltatást használó Android-alkalmazást létrehozni. Az alkalmazás megjeleníti a képet benne minden arc köré keretet rajzol.

> [!div class="nextstepaction"]
> [Oktatóanyag: Első lépések a Face API-val Androidon](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
