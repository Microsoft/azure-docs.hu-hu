---
title: 'Gyors útmutató: Miniatűrkép - REST, Go generálása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Go használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4f13a384cb32596d191625c8f13bc914686c6297
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000181"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-go-in-computer-vision"></a>Gyors útmutató: A REST API-val miniatűrkép generálása, és lépjen a Computer Vision

Ez a rövid útmutatóban egy rendszerképből számítógépes Látástechnológiai REST API használatával létrehozza a miniatűr. Megadhatja a magasságát és szélességét, amely a bemeneti lemezképről oldalarány eltérőek lehetnek. Computer Vision segítségével intelligens vágása nyelvelemző, mind a terület hasznos helyek azonosításához, és hozzon létre körbevágási koordinátái alapján az adott régióban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell a [Góval](https://golang.org/dl/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Megjelenik a származó ingyenes próbaverziós kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) előfizetni a Computer Vision, és a kulcs beszerzése.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a kód megfelelő területein:
    1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `imageUrl` értéket annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a kódot fájlként `.go` kiterjesztéssel. Például: `get-thumbnail.go`.
1. Nyisson meg egy parancsablakot.
1. A parancssorban futtassa a `go build` parancsot a fájl csomagjának lefordításához. Például: `go build get-thumbnail.go`.
1. Amikor a rendszer erre kéri, futtassa a fordított csomagot. Például: `get-thumbnail`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz tartalmazza a miniatűrkép bináris adatait. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="next-steps"></a>További lépések

Fedezze fel a Computer Vision API kép elemzése, hírességek és tereptárgyak felismerése észleléséhez miniatűrök készülnek, és nyomtatott és kézzel írott szövegek kinyerése. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
