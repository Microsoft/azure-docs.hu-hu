---
title: 'Gyors útmutató: Miniatűrkép - REST, a Ruby generálása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Ruby használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: fbac507ce904227aebd235323da32f3a4d216075
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994435"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-ruby-in-computer-vision"></a>Gyors útmutató: A REST API-t és a Ruby használatával a Computer Vision miniatűrkép generálása

Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision REST API-jának segítségével. A [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódussal létrehozhatja egy kép miniatűrjét. Megadhatja a magasságát és a szélességét, amely eltérhet a bemeneti kép oldalarányától. Computer Vision segítségével intelligens vágása nyelvelemző, mind a terület hasznos helyek azonosításához, és hozzon létre körbevágási koordinátái alapján az adott régióban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Ruby](https://www.ruby-lang.org/en/downloads/) 2.4.x-es vagy újabb verziójával kell rendelkeznie.
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Megjelenik a származó ingyenes próbaverziós kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) előfizetni a Computer Vision, és a kulcs beszerzése.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Szükség szerint hajtsa végre a következő módosításokat a kódban:
    1. A `<Subscription Key>` helyére írja be az előfizetési kulcsot.
    1. Ha szükséges, cserélje le a `https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze` elemet azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le a `https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\` elemet annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a kódot egy `.rb` kiterjesztésű fájlként. Például: `get-thumbnail.rb`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `ruby` parancsot a minta futtatására. Például: `ruby get-thumbnail.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer bináris formátumban adja vissza a sikeres választ, ahol a bináris adatok a miniatűr képadatainak felelnek meg. Ha a kérés meghiúsul, a válasz a konzolablakban jelenik meg. A meghiúsult kérés válasza tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-t, amellyel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
