---
title: Az űrlap-felismerő tárolójának telepítése és futtatása
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan használható az Azure Cognitive Services űrlap-felismerő tároló az űrlap-és táblázatos adatelemzéshez.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1be8afb58b22435f4f43b2d6884332a38b7f1e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467476"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Űrlap-felismerő tárolók telepítése és futtatása (kivonás)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Az Azure űrlap-felismerő a gépi tanulási technológiát alkalmazza a kulcs-érték párok és táblák űrlapokból való azonosítására és kinyerésére. Értékeket és táblázatos bejegyzéseket társít a kulcs-érték párokkal, majd kiadja az eredeti fájlban lévő kapcsolatokat tartalmazó strukturált adatokat. 

Ha csökkenteni szeretné a bonyolultságot és egyszerűen integrálhat egy egyéni űrlap-felismerő modellt a munkafolyamat-automatizálási folyamatba vagy más alkalmazásba, meghívhatja a modellt egy egyszerű REST API használatával. Csak öt dokumentumra van szükség, így az eredményeket gyorsan, pontosan és az adott tartalomhoz igazítva érheti el. Nincs szükség nehéz manuális beavatkozásra vagy kiterjedt adatelemzési szaktudásra. És nem szükséges az adatfelirat vagy az adatjegyzet.

| Függvény | Funkciók |
|----------|----------|
| Form Recognizer | <li>PDF-, PNG-és JPG-fájlok feldolgozása<li>Az egyéni modelleket az azonos elrendezésből legalább öt formával ellátott vonatok <li>A kulcs-érték párok és a tábla adatainak kibontása <li>Az Azure Cognitive Services Computer Vision API szövegfelismerés funkció használatával azonosíthatja és kinyerheti a képekből származó nyomtatott szöveget az űrlapokon belül<li>Nincs szükség jegyzet vagy címkézésre |

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

Az űrlap-felismerő tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

| Kötelező | Cél |
|----------|---------|
| A Docker-motor | A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> Windows rendszeren a Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br> |
| A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a beállításjegyzékekről, a tárházról, a tárolók és a tárolók képeiről, valamint az alapszintű `docker` parancsokról. |
| Azure CLI | Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t a gazdagépre. |
| Erőforrás Computer Vision API | A beolvasott dokumentumok és képek feldolgozásához Computer Vision erőforrásra van szükség. A szövegfelismerés szolgáltatást Azure-erőforrásként (a REST API vagy SDK-ban) vagy egy *kognitív-szolgáltatás-felismerő-Text* [tárolóban](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)érheti el. A szokásos számlázási díjak érvényesek. <br><br>Adja át a Computer Vision erőforrás API-kulcsát és végpontját (Azure Cloud vagy Cognitive Services tároló). Használja ezt az API-kulcsot és a végpontot **{COMPUTER_VISION_API_KEY}** és **{COMPUTER_VISION_ENDPOINT_URI}** néven.<br><br> Ha a *kognitív szolgáltatások – felismerés – Text* tárolót használja, ügyeljen arra, hogy:<br><br>Az űrlap-felismerő tároló Computer Vision kulcsa a `docker run` *kognitív szolgáltatások – felismerés – szöveg* tároló Computer Vision parancsában megadott kulcs.<br>A számlázási végpont a tároló végpontja (például: `http://localhost:5000` ). Ha a Computer Vision tárolót és az űrlap-felismerő tárolót is használja ugyanazon a gazdagépen, akkor mindkettő nem indítható el az alapértelmezett *5000*-as porton. |
| Űrlap-felismerő erőforrás | A tárolók használatához a következőket kell tennie:<br><br>Egy Azure- **űrlap-felismerő** erőforrás a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal űrlap- **felismerő** áttekintése és kulcsok oldalain, és mindkét értéknek a tároló elindításához szükségesnek kell lennie.<br><br>**{FORM_RECOGNIZER_API_KEY}**: a kulcsok oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: az Áttekintés lapon megadott végpont |

> [!NOTE]
> A Computer Vision erőforrás neve csak egyetlen szó lehet, kötőjel `-` vagy más speciális karakter nélkül. Ez a korlátozás az űrlap-felismerő és a szövegfelismerés tárolók kompatibilitásának biztosítása érdekében van érvényben.

## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van a szükséges összes Cognitive Services tárolóhoz. A végfelhasználói licencszerződés (EULA) értékének a következőnek kell lennie: `accept` . Emellett a végponti URL-cím és az API-kulcs is szükséges.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Végpont URI `{COMPUTER_VISION_ENDPOINT_URI}` -ja és `{FORM_RECOGNIZER_ENDPOINT_URI}`

A **végpont** URI-értéke a megfelelő kognitív szolgáltatás erőforrásának Azure Portal *Áttekintés* lapján érhető el. Navigáljon az *Áttekintés* lapra, vigye a kurzort a végpont fölé, és egy `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikon jelenik meg. Szükség esetén másolja és használja.

![A végpont URI-ja összegyűjtése későbbi használatra](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Kulcsok `{COMPUTER_VISION_API_KEY}` és `{FORM_RECOGNIZER_API_KEY}`

Ez a kulcs a tároló elindítására szolgál, és a megfelelő kognitív szolgáltatási erőforrás Azure Portal kulcsok lapján érhető el. Navigáljon a *kulcsok* oldalra, és kattintson az `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonra.

![A két kulcs egyikének beolvasása későbbi használatra](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás, például Azure Key Vault használata. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

A következő táblázat ismerteti az egyes űrlap-felismerő tárolók lefoglalásához szükséges minimális és ajánlott CPU-magokat és memóriát:

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Form Recognizer | 2 mag, 4 GB memória | 4 mag, 8 GB memória |
| szövegfelismerés | 1 mag, 8 GB memória | 2 mag, 8 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

> [!Note]
> A minimális és ajánlott értékek a Docker korlátain alapulnak, *nem* a gazdagép erőforrásaihoz.

Az űrlap-felismerő és a szövegfelismerés tárolók is szükségesek, vegye figyelembe, hogy az **szövegfelismerés** -tároló [részletesen a cikken kívül található.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő folyamat használatával dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-by-using-the-docker-run-command)a kötelező számlázási beállításokkal. További [példák](form-recognizer-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>A tároló futtatása a Docker Run parancs használatával

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A, a és az értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` `{FORM_RECOGNIZER_API_KEY}` .

[](form-recognizer-container-configuration.md#example-docker-run-commands) A parancs például `docker run` elérhető.

### <a name="form-recognizer"></a>Form Recognizer

> [!NOTE]
> A példákban használt könyvtárak a `--mount` Windows könyvtár elérési útjai. Ha Linux vagy macOS rendszert használ, módosítsa a környezetének paraméterét. 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

A parancs a következőket hajtja végre:

* Egy űrlap-felismerő tárolót futtat a tároló rendszerképből.
* 2 CPU-magot és 8 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.
* Csatlakoztat egy/input és egy/output-kötetet a tárolóhoz.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Különálló tárolók futtatása különálló Docker-futtatási parancsokkal

Az ugyanazon a gazdagépen helyileg üzemeltetett űrlap-felismerő és szöveges felismerő kombináció esetén használja a következő két példa Docker CLI-parancsokat:

Futtassa az első tárolót a 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Futtassa a második tárolót az 5001-as porton.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Minden további tárolónak egy másik porton kell lennie. 

### <a name="run-separate-containers-with-docker-compose"></a>Különálló tárolók futtatása a Docker-összeállítással

Az ugyanazon a gazdagépen helyileg üzemeltetett űrlap-felismerő és szöveges felismerő kombináció esetében tekintse meg a következő példát a Docker YAML-fájlját. A szöveges felismerőnek `{COMPUTER_VISION_API_KEY}` azonosnak kell lennie a `formrecognizer` és a `ocr` tárolók esetében is. A `{COMPUTER_VISION_ENDPOINT_URI}` csak a `ocr` tárolóban használatos, mert a `formrecognizer` tároló a `ocr` nevet és a portot használja. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> A `Eula` `Billing` tároló futtatásához a, a és a, valamint `ApiKey` a `FormRecognizer:ComputerVisionApiKey` és a beállításokat is `FormRecognizer:ComputerVisionEndpointUri` meg kell adni; egyéb esetben a tároló nem indul el. További információ: [számlázás](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

|Tároló|Végpont|
|--|--|
|űrlap-felismerő|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

A tároló olyan WebSocket-alapú lekérdezési végpont API-kat biztosít, amelyek az [űrlap-felismerő szolgáltatások SDK dokumentációjában](./index.yml)érhetők el.

Alapértelmezés szerint az űrlap-felismerő SDK a online szolgáltatások használja. A tároló használatához módosítania kell az inicializálási módszert. Ez az alábbi példákon látható.

#### <a name="for-c"></a>C esetében #

Váltás az Azure-felhő inicializálási hívásának használatával:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
Ehhez a híváshoz, amely a tároló végpontját használja:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python esetén

Váltás az Azure-felhő inicializálási hívásának használatával:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

Ehhez a híváshoz, amely a tároló végpontját használja:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

A tároló REST-végponti API-kat biztosít, amelyek a [Form felismerő API] https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) hivatkozás oldalon találhatók.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](form-recognizer-container-configuration.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

Az űrlap-felismerő tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő _űrlap-felismerő_ erőforrás használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](form-recognizer-container-configuration.md).

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az űrlap-felismerő tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* Az űrlap-felismerő egy Linux-tárolót biztosít a Docker számára.
* A tároló lemezképeit a rendszer az Azure-beli privát tároló-beállításjegyzékből tölti le.
* A tároló lemezképei a Docker-ben futnak.
* A REST API vagy a REST SDK használatával meghívhatja a műveleteket az űrlap-felismerő tárolóban a tároló gazda URI azonosítójának megadásával.
* Tároló létrehozásakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
>  Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [tárolók konfigurálása](form-recognizer-container-configuration.md) konfigurációs beállításokat.
* Használjon további [Cognitive Services tárolókat](../cognitive-services-container-support.md).