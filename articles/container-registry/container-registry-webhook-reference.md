---
title: Beállításjegyzék webhook-sémájának referenciája
description: Hivatkozás egy Azure Container registryben található webhook-kérésekhez tartozó JSON-adattartalomra, amely akkor jön létre, amikor a webhookok engedélyezve vannak az összetevők leküldéses vagy törlési eseményeihez.
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74455975"
---
# <a name="azure-container-registry-webhook-reference"></a>Webhook-hivatkozás Azure Container Registry

A tároló-beállításjegyzékhez olyan [webhookokat állíthat be](container-registry-webhook.md) , amelyek eseményeket állítanak elő, amikor bizonyos műveletek végrehajtása történik. Engedélyezheti például, hogy a rendszer akkor aktiválja a webhookokat, amikor egy tárolói képet vagy Helm-diagramot továbbítanak egy beállításjegyzékbe, vagy törölve lettek. Webhook indításakor Azure Container Registry a megadott végpontra vonatkozó információkat tartalmazó HTTP-vagy HTTPS-kérést ad ki. A végpont ezután feldolgozza a webhookot, és ennek megfelelően cselekszik.

A következő részek a támogatott események által generált webhook-kérelmek sémáját részletezik. Az események részei tartalmazzák az esemény típusához tartozó hasznos adatokat, például a kérések hasznos adatait, valamint egy vagy több, a webhookot kiváltó példás parancsot.

További információ a webhookok Azure Container registryben való konfigurálásáról: [Azure Container Registry webhookok használata](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-kérelmek

### <a name="http-request"></a>HTTP-kérelem

Egy aktivált webhook HTTP `POST` -kérést küld a webhook konfigurálásakor megadott URL-végpontnak.

### <a name="http-headers"></a>HTTP-fejlécek

A webhook-kérelmek közé tartozik a `Content-Type` , `application/json` Ha nem adott meg `Content-Type` Egyéni fejlécet a webhookhoz.

A kéréshez a webhookhoz megadott egyéni fejléceken felül nem kerül további fejlécek.

## <a name="push-event"></a>Leküldéses esemény

A webhook akkor aktiválódik, amikor egy tároló képét leküldenek egy adattárba.

### <a name="push-event-payload"></a>Leküldéses esemény hasznos adatai

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, amikor a webhook eseményt aktiválták.|
|`action`|Sztring|A webhook eseményt indító művelet.|
|[cél](#target)|Összetett típus|A webhook eseményt kiváltó esemény célpontja.|
|[kérelem](#request)|Összetett típus|A webhook eseményt létrehozó kérelem.|

### <a name="target"></a><a name="target"></a>cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma. Ugyanaz, mint a Length mező.|
|`digest`|Sztring|A tartalom kivonata, amelyet a Registry v2 HTTP API-specifikáció határoz meg.|
|`length`|Int32|A tartalom bájtjainak száma. Ugyanaz, mint a size mező.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A Képcímke neve.|

### <a name="request"></a><a name="request"></a>kérelem

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Sztring|Az eseményt kezdeményező kérelem azonosítója.|
|`host`|Sztring|A beállításjegyzék-példány külsőleg elérhető állomásneve, amelyet a HTTP-állomásfejléc a bejövő kérelmekben megadott.|
|`method`|Sztring|Az eseményt létrehozó kérelem metódusa.|
|`useragent`|Sztring|A kérelem felhasználói ügynökének fejléce.|

### <a name="payload-example-image-push-event"></a>Hasznos adatok például: leküldéses esemény

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Példa a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) -parancsra, amely elindítja a rendszerképek **leküldéses** eseményét webhookot:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagram leküldéses eseménye

A webhook akkor aktiválódik, ha egy Helm-diagramot egy adattárba küldenek.

### <a name="chart-push-event-payload"></a>Diagram leküldéses eseményének adattartalma

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, amikor a webhook eseményt aktiválták.|
|`action`|Sztring|A webhook eseményt indító művelet.|
|[cél](#helm_target)|Összetett típus|A webhook eseményt kiváltó esemény célpontja.|

### <a name="target"></a><a name="helm_target"></a>cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma.|
|`digest`|Sztring|A tartalom kivonata, amelyet a Registry v2 HTTP API-specifikáció határoz meg.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A diagram címkéjének neve|
|`name`|Sztring|A diagram neve.|
|`version`|Sztring|A diagram verziója.|

### <a name="payload-example-chart-push-event"></a>Hasznos példa: diagram leküldéses esemény

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Példa az [Azure CLI](/cli/azure/acr) -parancsra, amely elindítja a **chart_push** Event webhookot:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Esemény törlése

A webhook akkor aktiválódik, amikor egy rendszerkép-tárházat vagy jegyzékfájlt törölnek. Nem aktiválódik, ha a címke törölve lett.

### <a name="delete-event-payload"></a>Esemény-adattartalom törlése

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, amikor a webhook eseményt aktiválták.|
|`action`|Sztring|A webhook eseményt indító művelet.|
|[cél](#delete_target)|Összetett típus|A webhook eseményt kiváltó esemény célpontja.|
|[kérelem](#delete_request)|Összetett típus|A webhook eseményt létrehozó kérelem.|

### <a name="target"></a><a name="delete_target"></a> cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`digest`|Sztring|A tartalom kivonata, amelyet a Registry v2 HTTP API-specifikáció határoz meg.|
|`repository`|Sztring|A tárház neve.|

### <a name="request"></a><a name="delete_request"></a> kérelem

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Sztring|Az eseményt kezdeményező kérelem azonosítója.|
|`host`|Sztring|A beállításjegyzék-példány külsőleg elérhető állomásneve, amelyet a HTTP-állomásfejléc a bejövő kérelmekben megadott.|
|`method`|Sztring|Az eseményt létrehozó kérelem metódusa.|
|`useragent`|Sztring|A kérelem felhasználói ügynökének fejléce.|

### <a name="payload-example-image-delete-event"></a>Hasznos adat – példa: rendszerkép-törlési esemény

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Példa olyan [Azure CLI](/cli/azure/acr) -parancsokra, amelyek egy **delete** Event webhookot indítanak el:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Diagram törlési eseménye

A webhook egy Helm-diagram vagy-adattár törlésekor aktiválódik. 

### <a name="chart-delete-event-payload"></a>Diagram törlése esemény hasznos adatai

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, amikor a webhook eseményt aktiválták.|
|`action`|Sztring|A webhook eseményt indító művelet.|
|[cél](#chart_delete_target)|Összetett típus|A webhook eseményt kiváltó esemény célpontja.|

### <a name="target"></a><a name="chart_delete_target"></a> cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma.|
|`digest`|Sztring|A tartalom kivonata, amelyet a Registry v2 HTTP API-specifikáció határoz meg.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A diagram címkéjének neve|
|`name`|Sztring|A diagram neve.|
|`version`|Sztring|A diagram verziója.|

### <a name="payload-example-chart-delete-event"></a>Hasznos adat példa: diagram törlési esemény

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Példa az [Azure CLI](/cli/azure/acr) -parancsra, amely elindítja a **chart_delete** Event webhookot:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Következő lépések

[Azure Container Registry webhookok használata](container-registry-webhook.md)