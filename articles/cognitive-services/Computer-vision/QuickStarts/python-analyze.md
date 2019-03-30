---
title: 'Gyors útmutató: Egy távoli kép - REST, Python elemzése'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy távoli képet fog elemezni a Computer Vision API és a Python használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 23a85a84cfd2f938bd32e42925c07177203bf636
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630225"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-python-in-computer-vision"></a>Gyors útmutató: A REST API-t és a Python használatával a Computer Vision egy távoli kép elemzése

Ebben a rövid útmutatóban egy távolban tárolt képet fog elemezni vizuális jellemzők kinyerése érdekében a Computer Vision REST API-jával. Az [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) metódussal vizuális jellemzőket nyerhet ki a képek tartalma alapján.

Ezt a rövid útmutatót futtathatja lépésenként egy Jupyter-notebook segítségével a [MyBinderben](https://mybinder.org). A Binder indításához válassza az alábbi gombot:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Pythonnak](https://www.python.org/downloads/) telepítve kell lennie, ha a mintát helyben szeretné futtatni.
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.
- A következő Python-csomagokat telepíteni kell rendelkeznie. Használhat [pip](https://packaging.python.org/tutorials/installing-packages/) Python-csomagok telepítéséhez.
    - [Kérelmek](http://docs.python-requests.org/en/master/)
    - [matplotlib](https://matplotlib.org/)
    - [párnád](https://python-pillow.org/)

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a kód megfelelő területein:
    1. Cserélje le a `subscription_key` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le a `vision_base_url` értéket azon Azure-régió Computer Vision erőforrásának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `image_url` értéket egy olyan kép URL-címére, amelyet elemezni szeretne.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `analyze-image.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python analyze-image.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
import json
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(json.dumps(response.json()))
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintawebhely kielemez és megjelenít egy sikeres, az alábbihoz hasonló választ a parancsablakban:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Python-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](../Tutorials/PythonTutorial.md)
