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
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381604"
---
### <a name="default"></a>Alapértelmezett

A következő típusú paramétereket használhatja az indító blobhoz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

az <sup>1</sup> érték "be" kötést igényel a `direction` C#-beli *function.jsvagy rendszeren* `FileAccess.ReadWrite` .

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

Kötés a következőhöz: `string` vagy `Byte[]` csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy a típus használata javasolt `Stream` `CloudBlockBlob` . További információ: [párhuzamosság és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) a cikk későbbi részében.

### <a name="additional-types"></a>További típusok

A [5.0.0 vagy újabb verzióját](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) használó alkalmazások a [.net-hez készült Azure SDK](/dotnet/api/overview/azure/storage.blobs-readme)-ból is használhatnak típusokat. Ez a verzió a következő típusok mellett támogatja a örökölt,, `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` és `CloudAppendBlob` típusokat:

- [](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup> . BlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup> . BlockBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup> . PageBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup> . AppendBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup> . BlobBaseClient

az <sup>1</sup> érték "be" kötést igényel a `direction` C#-beli *function.jsvagy rendszeren* `FileAccess.ReadWrite` .

A fenti típusokat használó példákat a [bővítmény GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)tekintheti meg.
