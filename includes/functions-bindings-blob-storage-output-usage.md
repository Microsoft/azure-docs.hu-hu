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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381654"
---
### <a name="default"></a>Alapértelmezett

A Blobok írásához a következő típusokat lehet kötni:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> a "in" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.Read` -ben. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

<sup>2</sup> a "be" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.ReadWrite` -ben.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A vagy a alkalmazáshoz való kötés `string` `Byte[]` csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy a típus használata javasolt `Stream` `CloudBlockBlob` . További információ: a jelen cikk [párhuzamosságok és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című szakasza.

### <a name="additional-types"></a>További típusok

A [5.0.0 vagy újabb verzióját](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) használó alkalmazások a [.net-hez készült Azure SDK](/dotnet/api/overview/azure/storage.blobs-readme)-ból is használhatnak típusokat. Ez a verzió támogatja a örökölt,,,,, `CloudBlobContainer` `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` és `CloudAppendBlob` típusokat a következő típusok javára:

- [](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup> . BlobContainerClient
- [](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup> . BlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup> . BlockBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup> . PageBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup> . AppendBlobClient
- [](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup> . BlobBaseClient

<sup>1</sup> a "in" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.Read` -ben. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

<sup>2</sup> a "be" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.ReadWrite` -ben.

A fenti típusokat használó példákat a [bővítmény GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)tekintheti meg.
