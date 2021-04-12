---
title: A Docker Compose használata több tároló üzembe helyezéséhez
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezhet üzembe több Cognitive Services tárolót. Ez a cikk bemutatja, hogyan hangolhat össze több Docker-tároló lemezképet a Docker-összeállítás használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: 3b30b741008f00d435af7bb496990685f9b1781c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067758"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>A Docker Compose használata több tároló üzembe helyezéséhez

Ez a cikk bemutatja, hogyan helyezhet üzembe több Azure Cognitive Services-tárolót. Pontosabban megtudhatja, hogyan hozhat létre több Docker-tároló rendszerképet a Docker-összeállítás használatával.

> A [Docker-összeállítás](https://docs.docker.com/compose/) egy olyan eszköz, amely több tárolós Docker-alkalmazások definiálására és futtatására szolgál. Az összeállítás során YAML-fájlt használ az alkalmazás szolgáltatásainak konfigurálásához. Ezután egyetlen parancs futtatásával hozza létre és indítsa el az összes szolgáltatást a konfigurációból.

Hasznos lehet több tároló lemezképének összehangolása egyetlen gazdagépen. Ebben a cikkben az olvasási és űrlap-felismerő tárolókat fogjuk egyesíteni.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás számos olyan eszközt igényel, amelyeknek helyileg kell telepítenie és futnia:

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services) .
* [Docker-motor](https://www.docker.com/products/docker-engine). Ellenőrizze, hogy a Docker CLI működik-e a konzol ablakban.
* Egy megfelelő árképzési szintű Azure-erőforrás. Ebben a tárolóban csak a következő díjszabási szintek működnek:
  * Csak F0 vagy standard árképzési szinttel **Computer Vision** erőforrást.
  * Csak F0 vagy standard árképzési szinttel rendelkező **űrlap-felismerő** erőforrás.
  * **Cognitive Services** az erőforrást a S0 díjszabási szintjével.
* Ha a lekéréses előnézet tárolót használja, az [online kérelem űrlapját](https://aka.ms/csgate/) kell használnia.

## <a name="docker-compose-file"></a>Docker-összeállítási fájl

A YAML fájl határozza meg az összes telepítendő szolgáltatást. Ezek a szolgáltatások a vagy egy `DockerFile` meglévő tároló képére támaszkodnak. Ebben az esetben két előzetes rendszerképet fogunk használni. Másolja és illessze be a következő YAML-fájlt, és mentse *Docker-levélírás. YAML* néven. Adja meg a megfelelő **apikey**, **Számlázási** és **fájlban lecserélendő endpointuri** értékeket a fájlban.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Hozza létre a könyvtárakat a **kötetek** csomópont alatt megadott gazdagépen. Erre a megközelítésre azért van szükség, mert a könyvtáraknak léteznie kell, mielőtt mennyiségi kötések használatával próbál csatlakoztatni egy rendszerképet.

## <a name="start-the-configured-docker-compose-services"></a>A konfigurált Docker-összeállítási szolgáltatások elindítása

A Docker-összeállítási fájlok lehetővé teszik a megadott szolgáltatás életciklusának minden szakaszának kezelését: a szolgáltatások elindítása, leállítása és újraépítése; a szolgáltatás állapotának megtekintése; és a log streaming. Nyisson meg egy parancssori felületet a projekt könyvtárában (ahol a Docker-levélírás. YAML fájl található).

> [!NOTE]
> A hibák elkerülése érdekében győződjön meg arról, hogy a gazdagép megfelelően osztja meg a meghajtókat a Docker Engine használatával. Ha például a *E:\publicpreview* a *Docker-levélírás. YAML* fájlban található könyvtárként használja, ossza meg az **E** meghajtót a Docker használatával.

A parancssori felületen hajtsa végre a következő parancsot a *Docker-levélírás. YAML* fájlban definiált összes szolgáltatás elindításához (vagy újraindításához):

```console
docker-compose up
```

Az első alkalommal, amikor a Docker végrehajtja a **Docker-összeállító** parancsot ezzel a konfigurációval, lekéri a **szolgáltatások** csomópont alatt konfigurált lemezképeket, majd letölti és csatlakoztatja azokat:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

A lemezképek letöltése után a rendszerkép-szolgáltatások el lesznek indítva:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>A szolgáltatás rendelkezésre állásának ellenőrzése

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Íme néhány példa a kimenetre:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Tárolók tesztelése

Nyisson meg egy böngészőt a gazdagépen, és nyissa meg a **localhost** -ot a *Docker-levélírás. YAML* fájl megadott portjának használatával, például: http://localhost:5021/swagger/index.html . Az API **kipróbálás** funkciójának használatával például tesztelheti az űrlap-felismerő végpontot. Mindkét tárolónak elérhetőnek és tesztelhető kell lennie.

![Űrlap-felismerő tároló](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Cognitive Services tárolók](../cognitive-services-container-support.md)
