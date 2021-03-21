---
title: Docker-tároló beállításai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS Container Runtime-környezet a `docker run` parancs argumentumai alapján van konfigurálva. A LUIS számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001168"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker-tárolók konfigurálása 

A **Language Understanding** (Luis) tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. A LUIS számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tároló-specifikus beállítások a bemeneti [csatlakoztatási beállítások](#mount-settings) és a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokat tartalmaz:

|Kötelező|Beállítás|Rendeltetés|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|A számlázási információk nyomon követésére szolgál.|
|No|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Yes|[Számlázás](#billing-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Yes|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|No|[Fluentd](#fluentd-settings)|Írási napló és opcionálisan metrikus adatok egy Fluent-kiszolgáló számára.|
|No|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|No|[Logging](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|Yes|[Tartók](#mount-settings)|Adatok olvasása és írása a gazdagépről a tárolóba és a tárolóból a gazdagépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-setting) , a [`Billing`](#billing-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey-beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _Cognitive Services_ erőforráshoz [`Billing`](#billing-setting) .

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** erőforrás-kezelés a **kulcsok** területen
* LUIS-portál: **kulcsok és végpont-beállítások** lap. 

Ne használja az indító kulcsot vagy a szerzői kulcsot. 

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Számlázási beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** áttekintés, címkézett `Endpoint`
* LUIS Portal: **kulcsok és végponti beállítások** lap a végpont URI részeként.

| Kötelező | Name | Adattípus | Leírás |
|----------|------|-----------|-------------|
| Igen      | `Billing` | sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](luis-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával. 

A LUIS-tároló nem használ bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](luis-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

A következő táblázat a támogatott beállításokat ismerteti.

|Kötelező| Name | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Igen| `Input` | Sztring | A bemeneti csatlakoztatás célja. Az alapértelmezett érték `/input`. Ez a LUIS-csomag fájljainak helye. <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nem| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ide tartozik a LUIS-lekérdezési naplók és a tároló-naplók. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](luis-container-howto.md#stop-the-container) .

* Ezek a példák a meghajtón lévő könyvtárat használják `C:` , hogy elkerüljék az engedélyek ütközését a Windowson. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a Docker szolgáltatás engedélyét. 
* Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappájának szintaxisát, valamint a rendszer vonal folytatási karakterét. Ezek a példák egy sor folytatási karakterrel rendelkező Windows-konzolt feltételeznek `^` . Mivel a tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `LUIS` Azure `LUIS` Keys oldalon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `LUIS` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](luis-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el. További információ: [számlázás](luis-container-howto.md#billing).
> A ApiKey értéke a LUIS portál kulcsok és végpontok lapjának **kulcsa** , és az Azure `Cognitive Services` Resource Keys oldalon is elérhető. 

### <a name="basic-example"></a>Alapszintű példa

A következő példa a tároló futtatásához lehetséges legkevesebb argumentumot tartalmazhatja:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights példa

A következő példa úgy állítja be a ApplicationInsights argumentumot, hogy a telemetria küldje el Application Insights a tároló futása közben:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Példa naplózásra 

A következő parancs beállítja a naplózási szintet, `Logging:Console:LogLevel` hogy a naplózási szintet állítsa be [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Következő lépések

* [A tárolók telepítésének és futtatásának](luis-container-howto.md) áttekintése
* A LUIS-funkciókkal kapcsolatos problémák megoldásához tekintse meg a [hibaelhárítást ismertető témakört](troubleshooting.md) .
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata