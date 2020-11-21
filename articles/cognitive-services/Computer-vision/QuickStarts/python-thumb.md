---
title: 'Rövid útmutató: Miniatűr létrehozása – REST, Python'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Python használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 8781fb07667645e7eaec342adc551950a4719ccc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013757"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Gyors útmutató: miniatűr létrehozása a Computer Vision REST API és a Python használatával

Ebben a rövid útmutatóban egy miniatűrt fog létrehozni egy képből a Computer Vision REST API használatával. A [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c) módszerrel megadhatja a kívánt magasságot és szélességet, a Computer Vision pedig intelligens levágást használ a fontos terület intelligens azonosításához és az adott régió alapján történő levágási koordináták létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .
- Egy Kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához másolja a következő kódot a kódszerkesztőba. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Ezután tegye a következőket:

1. Választható Cserélje le a értékét `image_url` a saját rendszerképének URL-címére.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `get-thumbnail.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer sikeres választ ad vissza bináris adatként, amely a miniatűr képéneket jelöli. A mintának ezt a képet kell megjelenítenie. Ha a kérelem sikertelen, a válasz megjelenik a parancssorablakban, és tartalmaznia kell egy hibakódot.

## <a name="run-in-jupyter-optional"></a>Futtatás a Jupyter (nem kötelező)

A rövid útmutató lépésről lépésre elindítható a [MyBinder](https://mybinder.org)Jupyter notebookjának használatával. A Binder indításához válassza az alábbi gombot:

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Következő lépések

Ezután Ismerkedjen meg egy olyan Python-alkalmazással, amely a Computer Visiont használja az optikai karakterfelismerés (OCR) végrehajtásához. intelligens vágású miniatűrök létrehozása; a képek vizuális funkcióinak felismerése, kategorizálása, címkézése és leírása.

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](https://github.com/Microsoft/Cognitive-Vision-Python)

* A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c).