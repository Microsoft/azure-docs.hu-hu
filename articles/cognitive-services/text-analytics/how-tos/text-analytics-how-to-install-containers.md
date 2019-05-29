---
title: Tárolók telepítése és futtatása
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Hogyan letöltése, telepítése és -tárolókat futtathat szövegelemzési az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: f9f68b74c09bf0122ba856680a60bdb14ffa868f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306530"
---
# <a name="install-and-run-text-analytics-containers"></a>Telepítse és futtassa a Text Analytics tárolók

A Text Analytics tárolók biztosítják a természetes nyelvi feldolgozás speciális a nyers szöveg fölé, és magában foglal három fő funkciót: hangulatelemzést, kulcsszókeresést és nyelvfelismerést. Entitáskapcsolás jelenleg nem támogatott egy tárolóban. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Annak érdekében, hogy a Text Analytics tárolók futtatásához rendelkeznie kell a számítógép és a tároló környezeteknél.

## <a name="preparation"></a>Előkészítés

Szövegelemzés tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|`Cognitive Services` Erőforrás |A tároló használatához rendelkeznie kell:<br><br>A [ _Cognitive Services_ ](text-analytics-how-to-access-key.md) Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket az Azure Portalon a Cognitive Services áttekintése és a kulcsok oldalon érhető el, és a szükséges, hogy a tárolót. Hozzá kell adnia a `text/analytics/v2.0` útválasztás az a végpont URI-t, a következő BILLING_ENDPOINT_URI példában látható módon.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

### <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott, processzormagot legalább 2.6-os gigahertz (GHz) vagy gyorsabb, és a memória (gigabájtban), minden egyes Szövegelemzés tároló lefoglalása.

| Tároló | Minimális | Ajánlott | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Kulcsszókeresés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|
|Nyelvfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|
|Hangulatelemzés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|

* Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.
* TPS - tranzakció / másodperc

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Tárolórendszerképek szövegelemzési Microsoft Tárolóregisztrációs adatbázisból érhetők el. 

| Tároló | Tárház |
|-----------|------------|
|Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
|Hangulatelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltse le a tárolórendszerkép Microsoft Tárolóregisztrációs adatbázisból.

A Text Analytics tárolók rendelkezésre álló címkék teljes leírását lásd a következő tárolókat a Docker hub:

* [Kulcskifejezések kinyerése](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654)

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>A kulcs kifejezés kinyerése tároló docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>A nyelv észlelése tároló docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>A róluk szóló véleményeket tároló docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges a számlázás a beállításokat. További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot a tárolót szolgál, és az Azure Portalon elérhető `Cognitive Services` kulcsok oldalán.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI érték érhető el az Azure-beli `Cognitive Services` – áttekintés oldalra. <br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

Hozzá kell adnia a `text/analytics/v2.0` útválasztás az a végpont URI-t, az előző BILLING_ENDPOINT_URI példában látható módon.

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* A tároló rendszerképét kulcskifejezések tárolóban fut
* Több processzormaggal és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép `https://localhost:5000`, API-k tároló.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](../text-analytics-resource-container-config.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="billing"></a>Számlázás

A Text Analytics tárolók küldés számlázási adatokat az Azure-ba, a használatával egy _Cognitive Services_ az Azure-fiókjába erőforrás. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a Text Analytics tárolók munkafolyamatokat. Összegezve:

* Szövegelemzés biztosít három Linux-tárolók Docker, kulcsszókeresés, nyelvfelismerés és hangulatelemzés fejlécbe foglalja.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val a Text Analytics tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](../text-analytics-resource-container-config.md) a konfigurációs beállítások
* Tekintse meg [– gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) funkció kapcsolatos problémák megoldásához.

