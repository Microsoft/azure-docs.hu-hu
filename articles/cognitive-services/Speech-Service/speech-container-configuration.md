---
title: Beszédfelismerési tárolók konfigurálása
titleSuffix: Azure Cognitive Services
description: A Speech Service minden tárolót közös konfigurációs keretrendszerrel biztosít, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012169"
---
# <a name="configure-speech-service-containers"></a>Beszédfelismerési szolgáltatás tárolóinak konfigurálása

A beszédfelismerési tárolók lehetővé teszik, hogy az ügyfelek egy olyan beszédfelismerési alkalmazás-architektúrát építsenek ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására optimalizált. Az öt általunk támogatott beszédfelismerési tároló, a **beszéd-szöveg**, az **Egyéni beszéd-** szöveg, a **szöveg-beszéd**, az **neurális szöveg** -beszéd és az **egyéni szöveg-** beszéd.

A **beszédfelismerési** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) , a [`Billing`](#billing-configuration-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _beszédfelismerési_ erőforráshoz [`Billing`](#billing-configuration-setting) .

Ez a beállítás a következő helyen érhető el:

- Azure Portal: a **beszédfelismerés** erőforrás-kezelése a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás megadja az Azure-beli _beszédfelismerési_ erőforrás végponti URI-ját a tároló számlázási adatainak méréséhez. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nak kell lennie az Azure-beli _beszédfelismerési_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

- Azure Portal: **beszédfelismerés** áttekintése, címkézett `Endpoint`

| Kötelező | Name | Adattípus | Leírás |
| -------- | ---- | --------- | ----------- |
| Igen | `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](speech-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával.

A normál beszédfelismerési tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolására. Az egyéni beszédfelismerési tárolók azonban a kötetek csatlakoztatására támaszkodnak.

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](speech-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek.

| Választható | Name | Adattípus | Leírás |
| -------- | ---- | --------- | ----------- |
| Nem engedélyezett | `Input` | Sztring | A normál beszédfelismerési tárolók nem használják ezt. Az egyéni beszédfelismerési tárolók a [mennyiségi csatlakoztatásokat](#volume-mount-settings)használják.                                                                                    |
| Választható | `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Kötet csatlakoztatási beállításai

Az egyéni beszédfelismerési tárolók a [mennyiségi csatlakoztatásokat](https://docs.docker.com/storage/volumes/) használják az egyéni modellek megtartására. Megadhat egy kötet csatlakoztatását úgy, hogy hozzáadja a `-v` (vagy `--volume` ) lehetőséget a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancshoz.

Az egyéni modelleket a rendszer az első alkalommal tölti le, amikor új modellt töltenek be az egyéni Speech Container Docker Run parancs részeként. Az `ModelId` Egyéni beszédfelismerési tárolók szekvenciális futtatása a korábban letöltött modellt fogja használni. Ha nincs megadva a kötet csatlakoztatása, az egyéni modellek nem maradhatnak meg.

A kötet csatlakoztatási beállítása három színes `:` mezőből áll:

1. Az első mező a számítógép kötetének neve, például _C:\input_.
2. A második mező a tároló könyvtára, például _/usr/local/models_.
3. A harmadik mező (opcionális) a beállítások vesszővel tagolt listája, további információ: [kötetek használata](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Kötet csatlakoztatása – példa

```bash
-v C:\input:/usr/local/models
```

Ez a parancs a gazdagépek _C:\input_ könyvtárat csatlakoztatja a tárolók _/usr/local/models_ könyvtárához.

> [!IMPORTANT]
> A mennyiségi csatlakoztatási beállítások csak a **Custom Speech szövegre** és az **egyéni szöveg-beszéd** tárolóra vonatkoznak. A **beszéd – szöveg**, **neurális szöveg-beszéd** és **szöveg – beszéd** tárolók nem használnak mennyiségi csatlakoztatást.

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` . A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](speech-container-howto.md#stop-the-container) .

- **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok a háttér perjelet használják `\` , mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján.
- **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `Speech` Azure `Speech` Keys oldalon.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Speech` Áttekintés oldalán. | Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](speech-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el. További információ: [számlázás](#billing-configuration-setting).
> A ApiKey értéke az Azure Speech Resource Keys oldalának **kulcsa** .

## <a name="speech-container-docker-examples"></a>Beszédfelismerési tároló – példák

A következő Docker-példák a beszédfelismerési tárolóra vonatkoznak.

## <a name="speech-to-text"></a>[Diktálás](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Alapszintű példa a beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Naplózási példa a beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Custom Speech – szöveg](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Alapszintű példa Custom Speech-szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Naplózási példa Custom Speech-szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Alapszintű példa szövegről beszédre

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Naplózási példa szöveg – beszéd

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Egyéni szöveg – beszéd](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Alapszintű példa egyéni szöveg-beszéd átalakításra

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Példa naplózásra egyéni szöveg – beszéd

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[Neurális szöveg – beszéd](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>Alapszintű példa a neurális szöveg-beszéd

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>Naplózási példa a neurális szöveg-beszédhez

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[Beszédfelismerési Nyelvfelismerés](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>Alapszintű példa a beszédfelismerés nyelvének észlelésére

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>Naplózási példa a beszédfelismerés nyelvének észlelésére

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Következő lépések

- [A tárolók telepítésének és futtatásának](speech-container-howto.md) áttekintése
