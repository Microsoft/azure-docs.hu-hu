---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 938f55ae0ba911ea3a97cd49e6424bf8aaefdc76
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381688"
---
### <a name="default"></a>Alapértelmezett

A blob bemeneti kötéséhez a következő paramétereket használhatja:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

az <sup>1</sup> érték "be" kötést igényel a `direction` C#-beli *function.jsvagy rendszeren* `FileAccess.ReadWrite` .

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A vagy a alkalmazáshoz való kötés `string` `Byte[]` csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy a típus használata javasolt `Stream` `CloudBlockBlob` . További információ: a jelen cikk [párhuzamosságok és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című szakasza.

### <a name="additional-types"></a>További típusok

A [5.0.0 vagy újabb verzióját](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) használó alkalmazások a [.net-hez készült Azure SDK](/dotnet/api/overview/azure/storage.blobs-readme)-ból is használhatnak típusokat. Ez a verzió támogatja a örökölt,,,,, `CloudBlobContainer` `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` és `CloudAppendBlob` típusokat a következő típusok javára:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)
- [](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup> . BlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup> . BlockBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup> . PageBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup> . AppendBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup> . BlobBaseClient

az <sup>1</sup> érték "be" kötést igényel a `direction` C#-beli *function.jsvagy rendszeren* `FileAccess.ReadWrite` .

A fenti típusokat használó példákat a [bővítmény GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)tekintheti meg.
