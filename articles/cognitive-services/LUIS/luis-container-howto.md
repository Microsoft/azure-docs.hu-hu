---
title: Docker-tárolók telepítése és futtatása a LUIS-hoz
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló használatával betöltheti a betanított vagy közzétett alkalmazást, és hozzáférhet a helyszíni előrejelzésekhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: helyszíni, Docker, tároló
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040192"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Docker-tárolók telepítése és futtatása a LUIS-hoz

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

A tárolók lehetővé teszik a LUIS használatát a saját környezetében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez. Ebből a cikkből megtudhatja, hogyan töltheti le, telepítheti és futtathatja a LUIS-tárolókat.

A Language Understanding (LUIS) tároló betölti a betanított vagy közzétett Language Understanding modellt. Luis- [alkalmazásként](https://www.luis.ai)a Docker-tároló hozzáférést biztosít a tároló API-végpontjának lekérdezési előrejelzéséhez. Lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti őket a Language Understanding alkalmazásba az alkalmazás előrejelzési pontosságának javítása érdekében.

A következő videó bemutatja, hogyan használhatja ezt a tárolót.

[![Cognitive Services tárolók bemutatója](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

A LUIS-tároló futtatásához vegye figyelembe a következő előfeltételeket:

|Kötelező|Cél|
|--|--|
|A Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a** Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről, valamint az alapszintű `docker` parancsokról.|
|Azure `Cognitive Services` -erőforrás és Luis [csomagolt alkalmazás](luis-how-to-start-new-app.md) fájlja |A tároló használatához a következőket kell tennie:<br><br>* Egy _Cognitive Services_ Azure-erőforrás és a kapcsolódó számlázási kulcs a számlázási végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges. <br>* Egy betanított vagy közzétett alkalmazás, amely csatlakoztatott bemenetként van csomagolva a tárolóhoz a hozzá tartozó alkalmazás-AZONOSÍTÓval. A csomagolt fájlt a LUIS portálról vagy a szerzői API-k használatával szerezheti be. Ha a [szerzői API](#authoring-apis-for-package-file)-kkal becsomagolta a Luis csomagolási alkalmazást, a _szerzői kulcsra_ is szüksége lesz.<br><br>Ezek a követelmények a parancssori argumentumok átadására szolgálnak a következő változókra:<br><br>**{AUTHORING_KEY}**: ezzel a kulccsal lekérheti a csomagolt alkalmazást a felhőben található Luis szolgáltatásból, és feltöltheti a lekérdezési naplókat a felhőbe. A formátum `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: ez az azonosító az alkalmazás kiválasztására szolgál. A formátum `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Ez a kulcs a tároló elindítására szolgál. A végpont kulcsa két helyen található. Az első az _Cognitive Services_ erőforrás kulcsai listán szereplő Azure Portal. A végpont kulcsa a LUIS portálon is elérhető a kulcsok és a végpont beállításai lapon. Ne használja az alapszintű kulcsot.<br><br>**{ENDPOINT_URI}**: az Áttekintés lapon megadott végpont.<br><br>A [szerzői kulcs és a végpont kulcsa](luis-limits.md#key-limits) eltérő célokat szolgál. Ne használja szinonimaként. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>A csomagfájl API-jai készítése

API-k készítése csomagolt alkalmazásokhoz:

* [Közzétett csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nem közzétett, csak betanított csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

Az alábbi táblázat a tároló gazdagép minimális és ajánlott értékeit sorolja fel. A követelmények a forgalmi mennyiségtől függően változhatnak.

|Tároló| Minimális | Ajánlott | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|LUIS|1 mag, 2 GB memória|1 mag, 4 GB memória|20, 40|

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltheti le az adattárból a tároló képét `mcr.microsoft.com/azure-cognitive-services/language/luis` :

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Az elérhető címkék teljes leírását, például `latest` az előző parancsban használt információt lásd: [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) on Docker hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

![Language Understanding (LUIS) tároló használatának folyamata](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Csomag exportálása](#export-packaged-app-from-luis) a tárolóhoz a Luis Portalról vagy Luis API-kkal.
1. Helyezze át a csomag fájlját a [gazdagépen](#the-host-computer)a szükséges **bemeneti** könyvtárba. A LUIS-csomagfájl átnevezése, módosítása, felülírása vagy kibontása.
1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges _bemeneti csatlakoztatási_ és számlázási beállításokkal. További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).
1. Ha végzett a tárolóval, [importálja a végponti naplókat](#import-the-endpoint-logs-for-active-learning) a Luis-portál kimeneti csatlakoztatásáról, és [állítsa le](#stop-the-container) a tárolót.
1. Az alkalmazás fejlesztéséhez használja a LUIS Portal [aktív tanulását](luis-how-to-review-endpoint-utterances.md) a **végpontok áttekintése hosszúságú kimondott szöveg** oldalon.

A tárolóban futó alkalmazás nem módosítható. Az alkalmazás tárolóban való [módosításához a Luis-portálon](https://www.luis.ai) , vagy a Luis [authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)-k használatával kell módosítania az alkalmazást a Luis szolgáltatásban. Ezután végezze el a betanítást és/vagy közzétételt, majd töltsön le egy új csomagot, majd futtassa újra a tárolót.

A tárolón belüli LUIS-alkalmazás nem exportálható vissza a LUIS szolgáltatásba. Csak a lekérdezési naplók tölthetők fel.

## <a name="export-packaged-app-from-luis"></a>Csomagolt alkalmazás exportálása a LUIS-ből

A LUIS-tárolónak egy betanított vagy közzétett LUIS-alkalmazást kell megadnia a felhasználói hosszúságú kimondott szöveg előrejelzési lekérdezései megválaszolásához. A LUIS-alkalmazás beszerzéséhez használja a betanított vagy a közzétett csomag API-t.

Az alapértelmezett hely az a `input` alkönyvtár, amelyben a parancsot futtatja `docker run` .

Helyezze a csomagfájl egy könyvtárba, és hivatkozzon erre a könyvtárra a Docker-tároló futtatásakor bemeneti csatlakoztatásként.

### <a name="package-types"></a>Csomagok típusai

A bemeneti csatlakoztatási könyvtár az alkalmazás **éles**, **előkészítési** és **verziószámozási** modelljeit is tartalmazhatja. Az összes csomag csatlakoztatva van.

|Csomag típusa|Lekérdezési végpont API|Lekérdezés elérhetősége|Csomag fájlnevének formátuma|
|--|--|--|--|
|Verziószámmal|LETÖLTÉS, KÖZZÉTÉTEL|Csak tároló|`{APP_ID}_v{APP_VERSION}.gz`|
|Előkészítés|LETÖLTÉS, KÖZZÉTÉTEL|Azure és Container|`{APP_ID}_STAGING.gz`|
|Production|LETÖLTÉS, KÖZZÉTÉTEL|Azure és Container|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Ne nevezze át, módosítsa, írja felül vagy bontsa ki a LUIS-csomagok fájljait.

### <a name="packaging-prerequisites"></a>A csomagolás előfeltételei

A LUIS-alkalmazás csomagolása előtt a következőkkel kell rendelkeznie:

|Csomagolási követelmények|Részletek|
|--|--|
|Azure- _Cognitive Services_ erőforrás-példány|A támogatott régiók a következők:<br><br>USA nyugati régiója ( `westus` )<br>Nyugat-Európa ( `westeurope` )<br>Kelet-Ausztrália ( `australiaeast` )|
|Betanított vagy közzétett LUIS-alkalmazás|Nem [támogatott függőségek][unsupported-dependencies]nélkül. |
|Hozzáférés a [gazdaszámítógép](#the-host-computer)fájlrendszeréhez |A gazdaszámítógépnek engedélyeznie kell egy [bemeneti csatlakoztatást](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Alkalmazáscsomag exportálása a LUIS portálról

A LUIS [portál](https://www.luis.ai) lehetővé teszi a betanított vagy közzétett alkalmazás csomagjának exportálását.

### <a name="export-published-apps-package-from-luis-portal"></a>Közzétett alkalmazás csomagjának exportálása a LUIS portálról

A közzétett alkalmazás csomagja a **saját alkalmazások** listája lapon érhető el.

1. Jelentkezzen be a LUIS [portálra](https://www.luis.ai).
1. Jelölje be az alkalmazás nevének bal oldalán található jelölőnégyzetet a listában.
1. Válassza ki a lista fölötti környezetfüggő eszköztár **Exportálás** elemét.
1. Válassza **az Exportálás a tárolóba (gzip)** lehetőséget.
1. Válassza ki az **üzemi tárolóhely** vagy az **átmeneti tárolóhely** környezetét.
1. A csomag le van töltve a böngészőből.

![A tároló közzétett csomagjának exportálása az alkalmazás oldalának exportálás menüjéből](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Verziószámmal rendelkező alkalmazás csomagjának exportálása a LUIS-portálról

A verziószámmal ellátott alkalmazás csomagja a **verziók** listája lapon érhető el.

1. Jelentkezzen be a LUIS [portálra](https://www.luis.ai).
1. Jelölje ki az alkalmazást a listában.
1. Az alkalmazás navigációs sávján válassza a **kezelés** lehetőséget.
1. A bal oldali navigációs sávon válassza a **verziók** lehetőséget.
1. Jelölje be a listában a verzió nevének bal oldalán található jelölőnégyzetet.
1. Válassza ki a lista fölötti környezetfüggő eszköztár **Exportálás** elemét.
1. Válassza **az Exportálás a tárolóba (gzip)** lehetőséget.
1. A csomag le van töltve a böngészőből.

![Exportálja a tárolóhoz tartozó betanított csomagot a verziók oldal exportálás menüjéből.](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Közzétett alkalmazás csomagjának exportálása az API-ból

A következő REST API módszer használatával csomagolja ki a már [közzétett](luis-how-to-publish-app.md)Luis-alkalmazást. Az API-hívásban szereplő helyőrzők saját megfelelő értékeinek helyettesítése a HTTP-specifikáció alatt található táblázat használatával.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
| **{APP_ID}** | A közzétett LUIS-alkalmazás alkalmazás-azonosítója. |
| **{SLOT_NAME}** | A közzétett LUIS-alkalmazás környezete. Használja az alábbi értékek egyikét:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A LUIS-fiók szerzői kulcsa a közzétett LUIS-alkalmazáshoz.<br/>A szerzői kulcsot a LUIS-portál **felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus` – USA nyugati régiója<br/>`westeurope` – Nyugat-Európa<br/>`australiaeast` – Kelet-Ausztrália |

A közzétett csomag letöltéséhez tekintse meg az [API-dokumentációt itt][download-published-package]. Ha a fájl letöltése sikeres volt, a válasz egy LUIS csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolási helyre.

### <a name="export-versioned-apps-package-from-api"></a>A verziószámmal rendelkező alkalmazás csomagjának exportálása az API-ból

A következő REST API módszer használatával csomagolja ki a már [betanított](luis-how-to-train.md)Luis-alkalmazást. Az API-hívásban szereplő helyőrzők saját megfelelő értékeinek helyettesítése a HTTP-specifikáció alatt található táblázat használatával.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
| **{APP_ID}** | A betanított LUIS alkalmazás alkalmazás-azonosítója. |
| **{APP_VERSION}** | A betanított LUIS alkalmazás alkalmazásának verziója. |
| **{AUTHORING_KEY}** | A LUIS-fiók szerzői kulcsa a közzétett LUIS-alkalmazáshoz.<br/>A szerzői kulcsot a LUIS-portál **felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus` – USA nyugati régiója<br/>`westeurope` – Nyugat-Európa<br/>`australiaeast` – Kelet-Ausztrália |

A verzióval ellátott csomag letöltéséhez tekintse meg az [API dokumentációját itt][download-versioned-package]. Ha a fájl letöltése sikeres volt, a válasz egy LUIS csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolási helyre.

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a `docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A és értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{ENDPOINT_URI}` `{API_KEY}` .

[](luis-container-configuration.md#example-docker-run-commands) A parancs például `docker run` elérhető.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Ez a példa a meghajtón lévő könyvtárat használja `C:` , hogy elkerülje az engedélyek ütközését a Windowson. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a Docker szolgáltatás engedélyét.
* Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappájának szintaxisát, valamint a rendszer vonal folytatási karakterét. Ezek a példák egy sor folytatási karakterrel rendelkező Windows-konzolt feltételeznek `^` . Mivel a tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.

A parancs a következőket hajtja végre:

* Tároló futtatása a LUIS-tároló rendszerképből
* Betölti a (z) *C:\input*-on található Luis alkalmazást a Container Host-on.
* Két CPU-mag és 4 gigabájt (GB) memória kiosztása
* Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
* Tároló-és LUIS-naplókat ment a *C:\output*-on lévő, a tároló gazdagépén található kimeneti csatlakoztatásra.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).
> A ApiKey értéke a LUIS portál **Azure Resources** lapján található **kulcs** , amely az Azure `Cognitive Services` Resource Keys oldalon is elérhető.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>A tároló által támogatott Endpoint API-k

Az API v2 és [v3](luis-migration-api-v3.md) verziója is elérhető a tárolóban.

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt. A közzétett (átmeneti vagy éles) alkalmazások végpontjai _eltérő_ útvonalon rendelkeznek, mint a verziószámok a verzióval ellátott alkalmazások esetében.

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`).

# <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/v3)

|Csomag típusa|HTTP-parancs|Útvonal|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétett|LETÖLTÉS, KÖZZÉTÉTEL|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Verziószámmal|LETÖLTÉS, KÖZZÉTÉTEL|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

A lekérdezési paraméterek a lekérdezési válaszban megadhatják, hogy hogyan és mit ad vissza:

|Lekérdezési paraméter|Típus|Cél|
|--|--|--|
|`query`|sztring|A felhasználó megnyilatkozása.|
|`verbose`|boolean|Logikai érték, amely azt jelzi, hogy az összes metaadatot vissza kell-e adni az előre jelzett modellekhez. Az alapértelmezett érték a false (hamis).|
|`log`|boolean|Naplózza a lekérdezéseket, amelyek később az [aktív tanuláshoz](luis-how-to-review-endpoint-utterances.md)is felhasználhatók. Az alapértelmezett érték a false (hamis).|
|`show-all-intents`|boolean|Logikai érték, amely azt jelzi, hogy a rendszer visszaküldi-e az összes szándékot vagy a felső pontozási szándékot. Az alapértelmezett érték a false (hamis).|

# <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/v2)

|Csomag típusa|HTTP-parancs|Útvonal|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétett|[Letöltés](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Közzététel](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Verziószámmal|LETÖLTÉS, KÖZZÉTÉTEL|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

A lekérdezési paraméterek a lekérdezési válaszban megadhatják, hogy hogyan és mit ad vissza:

|Lekérdezési paraméter|Típus|Cél|
|--|--|--|
|`q`|sztring|A felhasználó megnyilatkozása.|
|`timezoneOffset`|szám|A timezoneOffset lehetővé teszi az előre elkészített entitások datetimeV2 által használt [időzóna módosítását](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) .|
|`verbose`|boolean|Az összes leképezést és azok pontszámát adja vissza, ha igaz értékre van állítva. Az alapértelmezett érték a False (hamis), amely csak a felső leképezést adja vissza.|
|`staging`|boolean|Az átmeneti környezet eredményeiből származó lekérdezést adja vissza, ha igaz értékre van állítva. |
|`log`|boolean|Naplózza a lekérdezéseket, amelyek később az [aktív tanuláshoz](luis-how-to-review-endpoint-utterances.md)is felhasználhatók. Alapértelmezett érték: true (igaz).|

***

### <a name="query-the-luis-app"></a>A LUIS-alkalmazás lekérdezése

A közzétett alkalmazások tárolójának lekérdezésére szolgáló CURL-parancs például a következő:

# <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/v3)

Ha egy modellt szeretne lekérdezni egy tárolóhelyen, használja a következő API-t:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Az **átmeneti** környezet lekérdezéséhez cserélje le az útvonalat a `production` következőre `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Egy verzióval ellátott modell lekérdezéséhez használja a következő API-t:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/v2)

Ha egy modellt szeretne lekérdezni egy tárolóhelyen, használja a következő API-t:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Az **átmeneti** környezet lekérdezéséhez módosítsa az **átmeneti** lekérdezési karakterlánc paraméterének értékét igaz értékre:

`staging=true`

Egy verzióval ellátott modell lekérdezéséhez használja a következő API-t:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
A verzió neve legfeljebb 10 karakterből állhat, és csak az URL-címekben engedélyezett karaktereket tartalmazza.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Az aktív tanuláshoz tartozó végponti naplók importálása

Ha a LUIS-tárolóhoz kimeneti csatlakoztatás van megadva, a rendszer az App Query naplófájljait a kimeneti könyvtárba menti, ahol `{INSTANCE_ID}` a a tároló azonosítója. Az alkalmazás-lekérdezési napló tartalmazza a LUIS-tárolóba küldött egyes előrejelzési lekérdezések lekérdezését, válaszát és időbélyegét.

A következő helyen a tároló naplófájljainak beágyazott címtár-szerkezete látható.
```
/output/luis/{INSTANCE_ID}/
```

A LUIS portálon válassza ki az alkalmazást, majd válassza a **végponti naplók importálása** elemet a naplók feltöltéséhez.

![Tároló naplófájljának importálása az aktív tanuláshoz](./media/luis-container-how-to/upload-endpoint-log-files.png)

A napló feltöltése után [tekintse át a hosszúságú kimondott szöveg végpontját](./luis-concept-review-endpoint-utterances.md) a Luis portálon.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához a parancssori környezetben, ahol a tároló fut, nyomja le a **CTRL + C** billentyűkombinációt.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](luis-container-configuration.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A LUIS-tároló számlázási adatokat küld az Azure-nak az Azure-fiókja _Cognitive Services_ erőforrásának használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](luis-container-configuration.md).

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a Language Understanding (LUIS) tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* Language Understanding (LUIS) egy Linux-tárolót biztosít a Docker számára, amely a hosszúságú kimondott szöveg végponti lekérdezési előrejelzéseit biztosítja.
* A rendszer letölti a tároló lemezképeit a Microsoft Container Registryról (MCR).
* A tároló lemezképei a Docker-ben futnak.
* REST API használatával lekérdezheti a tároló végpontjait a tároló gazdagép URI azonosítójának megadásával.
* A tárolók létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [tárolók konfigurálása](luis-container-configuration.md) konfigurációs beállításokat.
* Tekintse meg a [Luis Container korlátozásait](luis-container-limitations.md) az ismert képességekre vonatkozó korlátozásokról.
* A LUIS-funkciókkal kapcsolatos problémák megoldásához tekintse meg a [hibaelhárítást ismertető témakört](troubleshooting.md) .
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container