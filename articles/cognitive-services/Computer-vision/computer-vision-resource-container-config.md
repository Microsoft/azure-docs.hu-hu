---
title: OCR-tárolók olvasásának konfigurálása – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálhatja az OCR-tárolók beolvasásához szükséges és választható beállításokat a Computer Visionban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3e6c4b73e8aeb26c6ac4025ef3c07fb4f8d48eaf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308642"
---
# <a name="configure-read-ocr-docker-containers"></a>Az OCR Docker-tárolók olvasásának konfigurálása

A parancs argumentumai segítségével konfigurálja a Computer Vision OCR-tároló futásidejű környezetét `docker run` . Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) , a [`Billing`](#billing-configuration-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](computer-vision-how-to-install-containers.md).

A tároló a következő tároló-specifikus konfigurációs beállításokkal is rendelkezik:

|Kötelező|Beállítás|Rendeltetés|
|--|--|--|
|No|ReadEngineConfig:ResultExpirationPeriod| csak a v 2.0 tárolók. Az eredmény lejárati ideje (óra). Az alapértelmezett érték 48 óra. A beállítás azt határozza meg, hogy a rendszeren Mikor kell törölni a felismerési eredményeket. Ha például a `resultExpirationPeriod=1` rendszer a folyamat után 1 órával törli a felismerés eredményét. Ha `resultExpirationPeriod=0` a rendszer törli az eredmény beolvasása után az elismerés eredményét.|
|No|Gyorsítótár: Redis| csak a v 2.0 tárolók. Lehetővé teszi az Redis tárolását az eredmények tárolásához. *Szükség* van gyorsítótárra, ha több olvasási OCR-tároló van elhelyezve egy terheléselosztó mögött.|
|No|Üzenetsor: RabbitMQ|csak a v 2.0 tárolók. Lehetővé teszi a RabbitMQ számára a feladatok elküldését. A beállítás akkor hasznos, ha több olvasási OCR-tároló van elhelyezve egy terheléselosztó mögött.|
|No|Üzenetsor: Azure: QueueVisibilityTimeoutInMilliseconds | csak v3. x tárolók. Az az idő, ameddig az üzenet láthatatlan lesz, ha egy másik feldolgozó feldolgozza azt. |
|No|Tárolás::D ocumentStore:: MongoDB|csak a v 2.0 tárolók. Engedélyezi a MongoDB az állandó eredményű tároláshoz. |
|No|Storage: ObjectStore: AzureBlob: ConnectionString| csak v3. x tárolók. Azure Blob Storage-beli kapcsolatok karakterlánca. |
|No|Tárolás: TimeToLiveInDays| csak v3. x tárolók. Eredmény lejárati ideje (nap). A beállítás azt határozza meg, hogy a rendszeren Mikor kell törölni a felismerési eredményeket. Az alapértelmezett érték 2 nap (48 óra), ami azt jelenti, hogy az adott időszaknál hosszabb élettartamok nem garantáltak a sikeres lekéréshez. |
|No|Feladat: MaxRunningTimeSpanInMinutes| csak v3. x tárolók. Egyetlen kérelem maximális futási ideje. Az alapértelmezett érték 60 perc. |

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a `Cognitive Services` tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _Cognitive Services_ erőforráshoz [`Billing`](#billing-configuration-setting) .

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** áttekintés, címkézett `Endpoint`

Ne felejtse el hozzáadni az `vision/<version>` útválasztást a VÉGPONT URI-hoz az alábbi táblázatban látható módon. 

|Kötelező| Name | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v3.2` |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával.

A Computer Vision tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](computer-vision-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Computer Vision tárolók nem használják ezt.|
|Választható| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok a háttér perjelet használják `\` , mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `Computer Vision` Azure `Computer Vision` Keys oldalon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Computer Vision` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](computer-vision-how-to-install-containers.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](computer-vision-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure  `Cognitive Services` Resource Keys oldal kulcsa.

## <a name="container-docker-examples"></a>Tárolók Docker-példák

A következő Docker-példák az olvasási OCR-tárolóra vonatkoznak.


# <a name="version-32"></a>[3,2-es verzió](#tab/version-3-2)

### <a name="basic-example"></a>Alapszintű példa

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Példa naplózásra 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

### <a name="basic-example"></a>Alapszintű példa

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Példa naplózásra 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Következő lépések

* [A tárolók telepítésének és futtatásának](computer-vision-how-to-install-containers.md)áttekintése.
