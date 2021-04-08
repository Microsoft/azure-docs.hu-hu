---
title: Docker-tárolók telepítése és futtatása a Text Analytics API
titleSuffix: Azure Cognitive Services
description: A Text Analytics API Docker-tárolók használatával természetes nyelvi feldolgozást végezhet, például a helyszíni elemzéseket.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: helyszíni, Docker, tároló, érzelmek elemzése, természetes nyelvi feldolgozás
ms.openlocfilehash: ac82781ed4a05fbbca7f5b16edb0b5349dfc400a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432060"
---
# <a name="install-and-run-text-analytics-containers"></a>A Text Analytics-tárolók telepítése és futtatása

> [!NOTE]
> * A Hangulatelemzés és a nyelvfelismerés tárolója már általánosan elérhető. A Key kifejezés kinyerő tárolója a nyilvános előzetes verzióként érhető el.
> * Az entitások összekapcsolása és megtekintő szolgáltatás jelenleg nem érhető el tárolóként.
> * Ahhoz, hogy a Text Analytics az állapotfigyelő tárolóhoz hozzáférhessen, egy [kérelem űrlapra](https://aka.ms/csgate)van szükség. Jelenleg a használatért nem számítunk fel díjat.
> * Lehetséges, hogy a tároló rendszerképének helyei nemrég módosultak. Ebből a cikkből megtudhatja, hogy melyik a tároló frissített helye.

A tárolók lehetővé teszik a Text Analytics API-k a saját környezetében való futtatását, és kiválóan alkalmasak saját biztonsági és adatszabályozási követelményeihez. A Text Analytics tárolók fejlett természetes nyelvi feldolgozást biztosítanak a nyers szövegeken, és három fő funkciót tartalmaznak: a hangulat elemzését, a kulcsfontosságú kifejezés kinyerését és a nyelvfelismerés. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

> [!IMPORTANT]
> Az ingyenes fiók havonta 5 000 tranzakcióra van korlátozva, és csak az **ingyenes** és a **standard** szintű <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">díjszabás</a> érvényes a tárolók esetében. A tranzakciós kérelmek díjszabásával kapcsolatos további információkért lásd az [Adatkorlátokat](../overview.md#data-limits)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A Text Analytics tárolók futtatásához rendelkeznie kell a gazdagép és a tároló környezetével.

## <a name="preparation"></a>Előkészítés

Text Analytics tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|A Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a** Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről, valamint az alapszintű `docker` parancsokról.| 
|Erőforrás Text Analytics |A tároló használatához a következőket kell tennie:<br><br>Egy Azure [text Analytics-erőforrás](../../cognitive-services-apis-create-account.md) ingyenes (F0) vagy standard (S) [díjszabási szinttel](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). A társított API-kulcs és végpont URI-JÁT a Azure Portal erőforrás **kulcs és végpont** lapjára kattintva érheti el. <br><br>**{API_KEY}**: a két elérhető erőforrás-kulcs egyike. <br><br>**{ENDPOINT_URI}**: az erőforrás végpontja. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

Az alábbi táblázat a Text Analytics tárolók minimális és ajánlott specifikációit ismerteti. Legalább 2 gigabájt (GB) memóriára van szükség, és mindegyik CPU-mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet. Az engedélyezett tranzakció/szakasz (TPS) is szerepel a felsorolásban.

|  | Gazdagépek minimális specifikációi | Ajánlott gazdagépi specifikációk | Minimális TPS | Maximális TPS|
|---|---------|-------------|--|--|
| **Nyelvfelismerés, fő kifejezés kibontása**   | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15 | 30|
| **Hangulatelemzés**   | 1 mag, 2 GB memória | 4 mag, 8 GB memória |15 | 30|
| **Text Analytics Health-1 dokumentumhoz/kérelemhez**   |  4 mag, 10 GB memória | 6 mag, 12GB memória |15 | 30|
| **Text Analytics a Health-10 Documents/Request**   |  6 mag, 16GB memória | 8 mag, 20 GB memória |15 | 30|

A CPU-mag és a memória `--cpus` a `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

A Text Analytics tároló lemezképei a Microsoft Container Registry érhetők el.

# <a name="sentiment-analysis"></a>[Hangulatelemzés ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Kulcsszókeresés (előzetes verzió)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics for Health (előzetes verzió)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a `docker run`

A tárolók futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A tároló továbbra is futni fog, amíg le nem állítja.

> [!IMPORTANT]
> * A következő részben található Docker-parancsok a háttér perjelet használják `\` , mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
> * A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).
> * Az érzelmek elemzése és a nyelvfelismerés tárolók általánosan elérhetők. A Key kifejezés kinyerő tárolója az API v2-es verzióját használja, és előzetes verzióban érhető el.

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Kulcsszókeresés (előzetes verzió)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics for Health (előzetes verzió)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt.

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`).

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](../text-analytics-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Text Analytics tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő _text Analytics_ -erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta Text Analytics tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Text Analytics három Linux-tárolót biztosít a Docker számára, és különböző képességeket ágyaz be:
   * *Hangulatelemzés*
   * *Kulcsszókeresés (előzetes verzió)* 
   * *Nyelvfelismerés*
   * *Text Analytics for Health (előzetes verzió)*
* A rendszer letölti a tároló lemezképeit a Microsoft Container Registry (MCR) vagy az előzetes verziójú tároló adattárból.
* A tároló lemezképei a Docker-ben futnak.
* A REST API vagy az SDK használatával meghívhatja a műveleteket Text Analytics tárolókban a tároló gazdagép URI azonosítójának megadásával.
* A tárolók létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* A [tárolók konfigurálásának](../text-analytics-resource-container-config.md) áttekintése konfigurációs beállításokhoz
* A funkciókkal kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) című témakört.
