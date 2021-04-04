---
title: Tároló konfigurálása anomália-detektor API-hoz
titleSuffix: Azure Cognitive Services
description: Az anomália érzékelő API-tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363714"
---
# <a name="configure-anomaly-detector-containers"></a>Anomáliadetektor-tárolók konfigurálása

Az **anomália detektor** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokat tartalmaz:

|Kötelező|Beállítás|Rendeltetés|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|A számlázási információk nyomon követésére szolgál.|
|No|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Yes|[Számlázás](#billing-configuration-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Yes|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|No|[Fluentd](#fluentd-settings)|Írási napló és opcionálisan metrikus adatok egy Fluent-kiszolgáló számára.|
|No|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|No|[Logging](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|No|[Tartók](#mount-settings)|Adatok olvasása és írása a gazdagépről a tárolóba és a tárolóból a gazdagépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) , a [`Billing`](#billing-configuration-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _anomália-detektor_ erőforráshoz [`Billing`](#billing-configuration-setting) .

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **anomália-detektor** erőforrás-kezelése a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás megadja az Azure-beli _rendellenesség-Kiderítő_ erőforrás végponti URI-ját a tároló számlázási adatainak méréséhez. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _anomália-detektor_ erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **anomália-detektor** áttekintése, címkézve `Endpoint`

|Kötelező| Name | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](anomaly-detector-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával.

Az anomáliák Kiderítő tárolói nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](anomaly-detector-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Az anomália detektor tárolói nem használják ezt.|
|Választható| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](anomaly-detector-container-howto.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő szakaszban lévő Docker-parancsok a hátsó perjelet használják `\` , mint a bash-rendszerhéj vonalának folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. Például a Windows vonal folytatási karaktere egy kalap `^` . Cserélje le a vissza perjelet a kalapra. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a zárójelben lévő értéket a `{}` saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `Anomaly Detector` Azure `Anomaly Detector` Keys oldalon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Anomaly Detector` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](anomaly-detector-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](anomaly-detector-container-howto.md#billing).
> A ApiKey értéke az Azure-beli anomáliák Detektorának erőforrás-kulcsai oldalának **kulcsa** . 

## <a name="anomaly-detector-container-docker-examples"></a>Anomáliák detektor Container Docker-példák

A következő Docker-példák az anomália detektor tárolóhoz tartoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Példa a naplózásra parancssori argumentumokkal

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Következő lépések

* [Rendellenesség-Kiderítő tároló üzembe helyezése Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)