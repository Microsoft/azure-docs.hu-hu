---
title: Tárolók konfigurálása – Face
titleSuffix: Azure Cognitive Services
description: Az arc-tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. A kötelező és a választható beállítások is elérhetők.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5287fe6df4a2f0cb728bf0e705a4ce989ca38487
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934036"
---
# <a name="configure-face-docker-containers"></a>Arc Docker-tárolók konfigurálása

Az **arc** -tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) , a [`Billing`](#billing-configuration-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _Cognitive Services_ erőforráshoz [`Billing`](#billing-configuration-setting) .

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** áttekintés, címkézett `Endpoint`

Ne feledje, hogy a példában látható módon hozzáadja az _arc_ -útválasztást a végpont URI-hoz. 

|Kötelező| Name | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](face-how-to-install-containers.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI-konfigurációs beállítások

A szakaszban található konfigurációs beállítások a `CloudAI` tárolóra jellemző egyedi beállításokat biztosítanak. A következő beállítások és objektumok támogatottak a szakasz Face tárolójában: `CloudAI`

| Name | Adattípus | Leírás |
|------|-----------|-------------|
| `Storage` | Objektum | A Face tároló által használt tárolási forgatókönyv. További információ a tárolási forgatókönyvekről és az objektumhoz kapcsolódó beállításokról `Storage` : [tárolási forgatókönyv beállításai](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Tárolási forgatókönyv beállításai

A Face tároló a tárolt adatoktól függően blobokat, gyorsítótárat, metaadatokat és üzenetsor-adatokat tárol. Például a **LargePersonGroup** vonatkozó betanítási indexek és eredmények blob-adatként vannak tárolva. A Face tároló két különböző tárolási forgatókönyvet biztosít az ilyen típusú adatokkal való interakció és tárolás során:

* Memória  
  A memóriában mind a négy adattípus tárolódik. Nem oszlanak meg, és nem is állandóak. Ha a Face tároló le van állítva vagy el lett távolítva, a tárolóban lévő összes tárolt adatmennyiség megsemmisül.  
  Ez a Face tároló alapértelmezett tárolási forgatókönyve.
* Azure  
  A Face tároló az Azure Storage és a Azure Cosmos DB használatával osztja el ezeket a négyféle adattípust az állandó tárolók között. A blob-és üzenetsor-adatkezelést az Azure Storage kezeli. A metaadatok és a gyorsítótár-adatok Azure Cosmos DB kezelik. Ha a Face tároló le van állítva vagy el lett távolítva, a tárolóban lévő összes tárolt adatmennyiség az Azure Storage-ban és a Azure Cosmos DBban marad.  
  Az Azure Storage-forgatókönyv által használt erőforrások a következő további követelményekkel rendelkeznek
  * Az Azure Storage-erőforrásnak a StorageV2-fiók típusát kell használnia
  * A Azure Cosmos DB erőforrásnak a Azure Cosmos DB API-ját kell használnia a MongoDB

A tárolási forgatókönyveket és a társított konfigurációs beállításokat az objektum felügyeli `Storage` , a `CloudAI` konfigurációs szakaszban. A következő konfigurációs beállítások érhetők el az `Storage` objektumban:

| Name | Adattípus | Leírás |
|------|-----------|-------------|
| `StorageScenario` | Sztring | A tároló által támogatott tárolási forgatókönyv. A következő értékek érhetők el<br/>`Memory` – Alapértelmezett érték. A tároló nem állandó, nem elosztott és memóriában tárolt tárterületet használ egycsomópontos, ideiglenes használathoz. Ha a tároló le van állítva vagy el lett távolítva, a tároló tárterülete megsemmisül.<br/>`Azure` – A Container Azure-erőforrásokat használ a tároláshoz. Ha a tároló le van állítva vagy el lett távolítva, a tároló tárterülete megmarad.|
| `ConnectionStringOfAzureStorage` | Sztring | A tároló által használt Azure Storage-erőforráshoz tartozó kapcsolatok karakterlánca.<br/>Ez a beállítás csak akkor érvényes `Azure` , ha meg van adva a `StorageScenario` konfigurációs beállításhoz. |
| `ConnectionStringOfCosmosMongo` | Sztring | A tároló által használt Azure Cosmos DB erőforráshoz tartozó MongoDB-kapcsolatok karakterlánca.<br/>Ez a beállítás csak akkor érvényes `Azure` , ha meg van adva a `StorageScenario` konfigurációs beállításhoz. |

Az alábbi parancs például meghatározza az Azure Storage-forgatókönyvet, és minta-kapcsolódási karakterláncokat biztosít az Azure Storage-hoz, és Cosmos DB a Face tárolóban tárolt adattároláshoz használt erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

A tárolási forgatókönyvet külön kezelik a bemeneti csatlakoztatások és a kimeneti csatlakoztatások. Ezen szolgáltatások kombinációját egyetlen tárolóhoz is megadhatja. Az alábbi parancs például egy Docker-kötést határoz meg a `D:\Output` gazdagépen lévő mappához a kimeneti csatlakoztatásként, majd egy tárolót hoz létre a Face tároló lemezképből, és a naplófájlokat JSON formátumban menti a kimeneti csatlakoztatásra. A parancs az Azure Storage-forgatókönyvet is megadja, és minta-kapcsolódási karakterláncokat biztosít az Azure Storage-hoz, valamint Cosmos DB a Face tárolóban tárolt adattároláshoz használt erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

A Face containers nem használ bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](face-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | A Face containers nem használja ezt.|
|Választható| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](face-how-to-install-containers.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok a háttér perjelet használják `\` , mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `Face` Azure `Face` Keys oldalon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Face` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](face-how-to-install-containers.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](face-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure  `Cognitive Services` Resource Keys oldal kulcsa. 

## <a name="face-container-docker-examples"></a>Face Container Docker-példák

A következő Docker-példák a Face tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Következő lépések

* [A tárolók telepítésének és futtatásának](face-how-to-install-containers.md) áttekintése
