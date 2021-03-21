---
title: Docker-tárolók telepítése és futtatása a Face API
titleSuffix: Azure Cognitive Services
description: A Face API Docker-tárolójának használatával azonosíthatja és azonosíthatja a képeken található emberi arcokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
keywords: helyszíni, Docker, tároló, azonosítás
ms.openlocfilehash: 36cbd7bd24304871593b107f9b8ed9be02ce46de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706792"
---
# <a name="install-and-run-face-containers-preview"></a>Face containers telepítése és futtatása (előzetes verzió)

> [!IMPORTANT]
> Elérte a Face-tároló felhasználóinak korlátját. Jelenleg nem fogadunk el új alkalmazásokat a Face-tárolóhoz.

Az Azure Cognitive Services Face API egy linuxos Docker-tárolót biztosít, amely észleli és elemzi a képeken található emberi arcokat. Emellett azonosítja azokat az attribútumokat is, amelyek olyan arc-tereptárgyak, mint például az orr és a szemek, a nemek, az életkor és más, géppel előre jelzett arc-funkciók. Az észlelésen kívül a Face utasítással megtekintheti, hogy két arc van-e ugyanazon a képen, vagy a különböző képek azonosak-e a megbízhatósági pontszám használatával. Az arc emellett összehasonlíthatja az arcokat egy adatbázissal, hogy megtudja, van-e már hasonló vagy azonos arc. A hasonló arcok csoportokba rendezése közös vizualizációs tulajdonságok használatával is elvégezhető.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

Az arc Service-tárolók használata előtt meg kell felelnie a következő előfeltételeknek.

|Kötelező|Cél|
|--|--|
|A Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> Windows rendszeren a Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekre van szüksége a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről. Emellett az alapszintű parancsok ismeretére is szükség van `docker` .| 
|Face erőforrás |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Face** -erőforrást és a hozzá tartozó API-kulcsot és a végpont URI-ját. Mindkét érték elérhető az erőforrás **Áttekintés** és **kulcsok** oldalain. Szükségük van a tároló elindítására.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

A következő táblázat az egyes Face Service-tárolók számára lefoglalható minimális és ajánlott CPU-magokat és memóriát ismerteti.

| Tároló | Minimális | Ajánlott | Tranzakció/másodperc<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 mag, 2 GB memória | 1 mag, 4 GB memória |10, 20|

* Minden mag legalább 2,6 GHz-es vagy gyorsabb lehet.
* Másodpercenkénti tranzakciók (TPS).

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a Docker-lekéréssel

Elérhetők a Face szolgáltatáshoz tartozó tároló lemezképek. 

| Tároló | Adattár |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker-lekérés a Face tárolóhoz

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő folyamat használatával dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run) a szükséges számlázási beállításokkal. További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a Docker futtatásával

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A és értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{ENDPOINT_URI}` `{API_KEY}` .

[](face-resource-container-config.md#example-docker-run-commands) A parancs például `docker run` elérhető.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy Face tárolót futtat a tároló rendszerképből.
* Egy CPU-mag és 4 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és kioszt egy pszeudo TTY-t a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen. 

További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához, vagy a tároló nem indul el. További információ: [számlázás](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt. 

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`).


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](./face-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak lehetnek a tároló indításakor vagy futtatásakor előforduló problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

Az arc Service-tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő Face-erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](./face-resource-container-config.md).

## <a name="summary"></a>Összefoglalás

Ebből a cikkből megtudhatta, hogyan töltheti le, telepítheti és futtathatja a Face Service-tárolókat. Összegezve:

* A rendszer letölti a tároló lemezképeit a Azure Container Registryról.
* A tároló lemezképei a Docker-ben futnak.
* Az REST API vagy az SDK használatával hívhat meg műveleteket a Face Service-tárolókban a tároló gazda URI-ja megadásával.
* A tárolók példányainak létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyezniük kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* A konfigurációs beállításokkal kapcsolatban lásd: [tárolók konfigurálása](face-resource-container-config.md).
* Az arcok észlelésével és azonosításával kapcsolatos további tudnivalókért tekintse meg a [Face Overview](Overview.md)című témakört.
* További információ a tároló által támogatott módszerekről: [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* További Cognitive Services tárolók használatához lásd: [Cognitive Services tárolók](../cognitive-services-container-support.md).
