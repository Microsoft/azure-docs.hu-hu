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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455914"
---
A blob trigger számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezeknek az értékeknek ugyanazok a szemantikai értékei, mint a [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) típusa.

|Tulajdonság  |Típus  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az indító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-ja az elsődleges helyen.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blobhoz tartozó felhasználó által definiált metaadatok.|

A következő C# parancsfájl és JavaScript-példák például az indító blob elérési útját naplózzák, beleértve a tárolót:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```