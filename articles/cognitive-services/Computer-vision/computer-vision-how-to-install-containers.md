---
title: OcR Docker-tárolók olvasása a Computer Vision
titleSuffix: Azure Cognitive Services
description: Az OCR Docker-tárolók Computer Vision képekből és dokumentumokból származó szövegek helyszíni kinyerése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: helyszíni, OCR, Docker, tároló
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779092"
---
# <a name="install-read-ocr-docker-containers"></a>Read OCR Docker-tárolók telepítése

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

A tárolók lehetővé teszik a Computer Vision API-k a saját környezetében való futtatását. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez. Ebből a cikkből megtudhatja, hogyan tölthet le, telepíthet és futtathat Computer Vision tárolókat.

Az *OCR-tároló* olvasása lehetővé teszi nyomtatott és kézzel írt szövegek kinyerése JPEG-, PNG-, BMP-, PDF- és TIFF-fájlformátumokat támogató képekből és dokumentumokból. További információkért tekintse meg a [Read API útmutatóját.](Vision-API-How-to-Topics/call-read-api.md)

## <a name="read-32-container"></a>3.2-es tároló olvasása

A Read 3.2 OCR-tároló a következő eket biztosítja:
* Új modellek a nagyobb pontosság érdekében.
* Több nyelv támogatása ugyanazon a dokumentumon belül.
* Összesen 73 nyelv támogatása. Lásd az [OCR által támogatott nyelvek teljes listáját.](./language-support.md#optical-character-recognition-ocr)
* Egyetlen művelet a dokumentumokhoz és a képekhez.
* Nagyobb méretű dokumentumok és képek támogatása.
* Megbízhatósági pontszámok.
* Nyomtatásra és kézzel írt szöveggel is írt dokumentumok támogatása.
* A dokumentumban csak a kijelölt oldal(ak)ból lehet szöveget kinyerni.
* Szövegsor kimeneti sorrendjének alapértelmezett értékről természetesebb olvasási sorrendre való kiválasztása csak latin nyelvek esetén.
* A szövegsorok besorolása kézzel írott stílusként vagy nem csak latin nyelvű szövegként.

Ha jelenleg Read 2.0-tárolókat használ, [](read-container-migration-guide.md) tekintse meg a migrálási útmutatót az új verziók változásainak megismeréséért.

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt teljesítenie kell az alábbi előfeltételeket:

|Kötelező|Cél|
|--|--|
|A Docker-motor| Telepítenie kell a Docker Engine-t egy [gazdagépre.](#the-host-computer) A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy engedélyezze a tárolók számára a kapcsolódást és a számlázási adatok Azure-ba való elküldését. <br><br> **Windows rendszeren** a Docker-t is úgy kell konfigurálni, hogy támogassa a Linux-tárolókat.<br><br>|
|A Docker ismerete | Alapszintű ismeretekkel kell rendelkezik a Docker olyan fogalmairól, mint a regisztrációs jegyzékek, adattárak, tárolók és tároló rendszerképek, valamint az alapszintű `docker` parancsok ismerete.| 
|Computer Vision erőforrás |A tároló csak akkor használható, ha:<br><br>Egy Azure **Computer Vision** erőforrás és a társított API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás Áttekintés és Kulcsok oldalán, amelyek szükségesek a tároló elindítani.<br><br>**{API_KEY}**: A Kulcsok lapon elérhető két **erőforráskulcs** egyike<br><br>**{ENDPOINT_URI}**: Az Áttekintés oldalon megadott **végpont**|

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="request-approval-to-run-the-container"></a>Jóváhagyás kérése a tároló futtatásához

Töltse ki és küldje el a [kéreleműrlapon,](https://aka.ms/csgate) hogy jóváhagyást kérjen a tároló futtatásához. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdagép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektorbővítmények támogatása

A **gazdaszámítógép** futtatja a Docker-tárolót. A *gazdagépnek támogatnia* kell [az Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) bővítményeket. A Linux-gazdagépek AVX2-támogatását a következő paranccsal ellenőrizheti:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> A gazdagépnek *támogatnia kell* az AVX2-t. A tároló *nem fog megfelelően* működni az AVX2 támogatása nélkül.

### <a name="container-requirements-and-recommendations"></a>Tárolókra vonatkozó követelmények és javaslatok

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének lekérte a következővel: `docker pull`

Elérhetőek az Olvasáshoz használható tároló-rendszerképek.

| Tároló | Container Registry / Adattár / Rendszerkép neve |
|-----------|------------|
| 2.0-s előzetes verzió olvasása | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Olvasás 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltsön le egy tároló-rendszerképet.

### <a name="docker-pull-for-the-read-ocr-container"></a>Docker-lekért írás az OCR-tárolóhoz

# <a name="version-32"></a>[3.2-es verzió](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[2.0-s előzetes verzió](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló már a gazdagépen [van,](#the-host-computer)a következő folyamattal dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) érhetők el `docker run` a parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Futtassa a tárolót a következővel: `docker run`

Futtassa a tárolót a [docker run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal. A és [az érték lekért](#gathering-required-parameters) részleteiért tekintse meg a szükséges paraméterek `{ENDPOINT_URI}` összegyűjtését. `{API_KEY}`

[Példák](computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra.

# <a name="version-32"></a>[3.2-es verzió](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja az OCR-tároló olvasása a tároló rendszerképből.
* 8 processzormagot és 18 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A rendszer automatikusan eltávolítja a tárolót, miután az kilép. A tároló rendszerképe továbbra is elérhető a gazdagépen.

# <a name="version-20-preview"></a>[2.0-s előzetes verzió](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja az OCR-tároló olvasása a tároló rendszerképből.
* 8 processzormagot és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A rendszer automatikusan eltávolítja a tárolót, miután az kilép. A tároló rendszerképe továbbra is elérhető a gazdagépen.

---


További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) érhetők `docker run` el a parancsra. 

> [!IMPORTANT]
> A , és beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló `Eula` `Billing` nem indul `ApiKey` el.  További információ: [Számlázás.](#billing)

Ha nagyobb átviteli sebességre van szüksége (például többoldalas fájlok feldolgozásakor), fontolja meg több tároló üzembe helyezését egy [Kubernetes-fürtön](deploy-computer-vision-on-premises.md)az [Azure Storage](../../storage/common/storage-account-create.md) és az Azure [Queue használatával.](../../storage/queues/storage-queues-introduction.md)

Ha az Azure Storage-ban tárolja [a](../../storage/common/storage-configure-connection-string.md) képek feldolgozásra való tárolását, létrehozhat egy kapcsolati sztringet, amely a tároló hívása során használható.

A kapcsolati sztring megkeresi:

1. Lépjen a **tárfiókok lapra** a Azure Portal, és keresse meg a fiókját.
2. Kattintson a **hozzáférési kulcsok elemre** a bal oldali navigációs listában.
3. A kapcsolati sztring a Kapcsolati sztring **alatt található**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt. 

# <a name="version-32"></a>[3.2-es verzió](#tab/version-3-2)

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`). A Swagger elérési útja a következő: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[2.0-s előzetes verzió](#tab/version-2)

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`). A Swagger elérési útja a következő: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Aszinkron olvasás


# <a name="version-32"></a>[3.2-es verzió](#tab/version-3-2)

A és a művelettel aszinkron módon olvashatja a képeket, hasonlóan ahhoz, ahogy az Computer Vision szolgáltatás ezeket a megfelelő `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` REST-műveleteket használja. Az aszinkron POST metódus egy olyan metódust ad vissza, amely a `operationId` HTTP GET-kérés azonosítójaként lesz használva.


A swagger felhasználói felületen válassza a lehetőséget a `Analyze` böngészőben való kibontásához. Ezután válassza **a Try it out Choose** file  >  **(Fájl kiválasztása) lehetőséget.** Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Ha az aszinkron POST sikeresen lefutott, **http 202-es** állapotkódot ad vissza. A válasz részeként egy fejléc tartalmazza a kérés `operation-location` eredményvégpontját.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A `operation-location` a teljes URL-cím, amely egy HTTP GET-en keresztül érhető el. Itt látható az előző képen látható `operation-location` URL-cím végrehajtására adott JSON-válasz:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[2.0-s előzetes verzió](#tab/version-2)

A és a művelettel aszinkron módon olvashatja a képeket, hasonlóan ahhoz, ahogy az Computer Vision szolgáltatás ezeket a megfelelő `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` REST-műveleteket használja. Az aszinkron POST metódus egy metódust ad vissza, amely a `operationId` HTTP GET-kérés azonosítójaként lesz használva.

A swagger felhasználói felületen válassza a lehetőséget a `asyncBatchAnalyze` böngészőben való kibontásához. Ezután válassza a Try it out Choose file **(Próbálja** ki  >  **a fájl kiválasztása) lehetőséget.** Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Ha az aszinkron POST sikeresen lefutott, **http 202-es** állapotkódot ad vissza. A válasz részeként egy fejléc tartalmazza a kérés `operation-location` eredményvégpontját.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A `operation-location` a teljes URL-cím, és egy HTTP GET-en keresztül érhető el. Itt látható az előző képen látható `operation-location` URL-cím végrehajtására adott JSON-válasz:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Ha több olvasási OCR-tárolót helyez üzembe egy terheléselosztási eszköz mögött, például a Docker Compose vagy a Kubernetes alatt, akkor külső gyorsítótárral kell lennie. Mivel a feldolgozó tároló és a GET kérelemtároló nem biztos, hogy azonos, egy külső gyorsítótár tárolja az eredményeket, és megosztja őket a tárolók között. A gyorsítótár beállításaival kapcsolatos részletekért lásd: [Computer Vision Docker-tárolók konfigurálása.](./computer-vision-resource-container-config.md)

### <a name="synchronous-read"></a>Szinkron olvasás

A következő művelettel szinkron módon olvashat be egy képet. 

# <a name="version-32"></a>[3.2-es verzió](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[2.0-s előzetes verzió](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

A kép teljes beolvasása után az API csak akkor ad vissza JSON-választ. Ez alól az egyetlen kivétel, ha hiba történik. Hiba esetén a rendszer a következő JSON-t ad vissza:

```json
{
    "status": "Failed"
}
```

A JSON-válaszobjektum objektumdiagramja megegyezik az aszinkron verzióval. Ha Ön JavaScript-felhasználó, és a típusbiztonságot szeretné, fontolja meg a TypeScript használatát a JSON-válasz leadásáért.

Példaként tekintse meg a <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript-védőfalat</a>  itt, és válassza a Futtatás lehetőséget a könnyű használat megjelenítéséhez.

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha úgy futtatja a [](./computer-vision-resource-container-config.md#mount-settings) tárolót, hogy engedélyezve van a kimeneti csatlakoztatás és a naplózás, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak lehetnek a tároló indítása vagy futtatása során történt problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Cognitive Services a számlázási adatokat az Azure-fiók megfelelő erőforrásával küldik el az Azure-nak.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a lehetőségekről: [Tárolók konfigurálása.](./computer-vision-resource-container-config.md) 

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a tárolók letöltésével, telepítésével és futtatásával kapcsolatos fogalmakat és munkafolyamatokat Computer Vision. Összegezve:

* Computer Vision egy Linux-tárolót biztosít a Docker számára, amely az Olvasást teszi lehetővé.
* Az olvasási tároló rendszerképének futtatásához egy alkalmazásra van szükség. 
* A tároló rendszerképe a Dockerben fut.
* A tároló gazda URI-ját meg REST API vagy AZ SDK használatával hívhatja meg az OCR-tárolók olvasása műveleteket.
* A tárolók példányosulásakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> Cognitive Services tárolók nem futnak az Azure-hoz való csatlakozás nélkül méréshez. Az ügyfeleknek lehetővé kell tenniük, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a konfigurációs beállítások tárolókonfigurációját ismertető cikkeket. [](computer-vision-resource-container-config.md)
* A nyomtatott és kézzel írt szövegek felismeréséről az [OCR](overview-ocr.md) áttekintésében talál további információt
* A tároló [](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) által támogatott metódusokkal kapcsolatos részletekért tekintse meg a Read API-t.
* A gyakori [kérdések (GYIK)](FAQ.md) című cikkből megtudhatja, hogyan háríthatja el a Computer Vision kapcsolatos problémákat.
* További Cognitive Services [használata](../cognitive-services-container-support.md)
