---
title: Tárolók konfigurálása
titlesuffix: Face - Azure Cognitive Services
description: Tárolók konfigurációs beállításait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: d30c2218fe20d6b760f379caf52ca0bf97e1c750
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071497"
---
# <a name="configure-face-docker-containers"></a>Face Docker-tárolók konfigurálása

A **Face** tároló-futtatókörnyezet használatával lett konfigurálva a `docker run` parancssori argumentumokat. Ez a tároló rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállítások a számlázási beállításokat is. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _Cognitive Services_ megadott erőforrás a [ `Billing` ](#billing-configuration-setting) konfigurációs beállítás.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **A cognitive Services** erőforrás-kezelés alatt **kulcsok**

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg a végpont URI-t, a _Cognitive Services_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _Cognitive Services_ erőforrást az Azure-ban. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **A cognitive Services** áttekintése, címkével `Endpoint`

Ne felejtse el hozzáadni a _Face_ útválasztás, a végpont URI-t, a példában látható módon. 

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI konfigurációs beállításai

A konfigurációs beállításokat a `CloudAI` szakaszban adja meg a tároló egyedi tároló-specifikus beállítások. A következő beállításokat, és az objektumok a Face tároló támogatottak a `CloudAI` szakasz

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Storage` | Objektum | A tárolási eset, a Face tárolót használja. További információ a storage-forgatókönyvek és kapcsolódó beállításait a `Storage` objektumazonosító, lásd: [tárolási forgatókönyv beállításai](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Tárolási forgatókönyv beállításai

A Face tároló tárolja a blob, a gyorsítótár, a metaadatok és a sorban lévő adatok függően mi tárol. Ha például képzési indexeket és a egy nagy embercsoportok eredményei formájában tárolja Blobadatok. A Face tárolót biztosít, két különböző adattárolási forgatókönyvekhez való interakció során és az ilyen típusú adatok tárolására:

* Memory (Memória)  
  Minden négy típusú adatokat a memóriában tárolódnak. Nem használ osztják, sem pedig az állandó. A Face tároló leállt, vagy eltávolítja, ha az összes adat a tárolóban, hogy a tároló a semmisíteni.  
  Ez az az alapértelmezett tárolási eset a Face tároló.
* Azure  
  A Face tároló Azure Storage és az Azure Cosmos DB használatával ezek négy típusú adatok szét a tartós tároláshoz. Azure Storage BLOB és üzenetsor adatokat kezeli. Azure Cosmos DB kezeli az metaadatok és a gyorsítótár-adatokat. A Face tároló leállt, vagy eltávolítja, ha az adatok a tároló storage-ban tárolt, az Azure Storage és Azure Cosmos DB marad.  
  Az Azure storage-forgatókönyv által használt erőforrások az alábbi további követelményekkel rendelkezik.
  * Az Azure Storage-erőforrást kell használnia a StorageV2 fiók típusa
  * Az Azure Cosmos DB erőforrás az Azure Cosmos DB API-t kell használnia a mongodb-hez

A storage-forgatókönyvek és a kapcsolódó konfigurációs beállítások kezeli a `Storage` objektumot, a a `CloudAI` konfigurációs szakaszban. A következő beállítások érhetők el a `Storage` objektum:

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `StorageScenario` | Karakterlánc | A tárolási eset, a tároló által támogatott. A következő értékek közül választhat<br/>`Memory` – Alapértelmezett érték. Tároló nem állandó, nem elosztott és a memórián belüli tároló használ egy csomópontos, ideiglenes használatra. A tároló leállt, vagy eltávolítja, ha a tárolót a tároló megsemmisülésekor.<br/>`Azure` -Tároló Azure-erőforrásokat használ a tároláshoz. A tároló leállítása vagy eltávolítja, a rendszer megőrzi a tároló a tárolót.|
| `ConnectionStringOfAzureStorage` | Karakterlánc | Az Azure Storage-erőforrás a tároló által használt kapcsolati karakterláncára.<br/>Ez a beállítás csak akkor, ha érvényes `Azure` van megadva a `StorageScenario` konfigurációs beállítás. |
| `ConnectionStringOfCosmosMongo` | Karakterlánc | A MongoDB kapcsolati karakterláncot az Azure Cosmos DB-erőforrás a tárolót használja.<br/>Ez a beállítás csak akkor, ha érvényes `Azure` van megadva a `StorageScenario` konfigurációs beállítás. |

Például a következő parancsot adja meg az Azure storage-forgatókönyv, és minta kapcsolati karakterláncok biztosít a Face tároló adatok tárolására használt Azure Storage és a Cosmos DB erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

A tárolási eset külön-külön történik a csatlakoztatása bemeneti és kimeneti csatlakoztatása. Ezek a Funkciók, az egyetlen tároló kombinációját is megadhat. Például a következő parancsot határozza meg a Docker kötési csatlakoztatási a `D:\Output` mappát a gazdagépen, a kimeneti csatlakoztatási majd példányosít egy tárolót a Face tárolórendszerképet, naplófájlok mentése, a kimeneti csatlakoztatási JSON formátumban. A parancs is itt adhatja meg az Azure storage-forgatókönyv, és minta kapcsolati karakterláncok biztosít a Face tároló adatok tárolására használt Azure Storage és a Cosmos DB erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxybeállításai hitelesítő adatok

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot.

A Face tárolók ne használja a bemeneti vagy kimeneti csatlakoztatja képzési vagy szolgáltatás adatok tárolására. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](face-how-to-install-containers.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a Docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | String | Face tárolók ez nem használható.|
|Optional| `Output` | String | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](face-how-to-install-containers.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: Az alábbi szakaszok a Docker-parancsokat használhatja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{BILLING_KEY} | A végpont kulcs a Cognitive Services-erőforrás. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | A számlázási végpontértéknek többek között a régiók és a face útválasztást.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](face-how-to-install-containers.md#billing).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** az Azure `Cognitive Services` erőforráslapján kulcsok. 

## <a name="face-container-docker-examples"></a>Nyomtatandó oldallal tároló Docker-példák

Az alábbi Docker-példák a face tároló is. 

### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Naplózás példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](face-how-to-install-containers.md)
