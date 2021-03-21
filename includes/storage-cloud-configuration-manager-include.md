---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027535"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) osztály elemzi a konfigurációs beállításokat. Az alkalmazás az asztali gépen, egy mobileszközön, egy Azure-beli virtuális gépen vagy egy Azure Cloud Service-ben futó ügyfélalkalmazások beállításait elemzi.

A csomagra való hivatkozáshoz `CloudConfigurationManager` adja hozzá a következő `using` irányelveket:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy konfigurációs fájlból:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Az Azure Configuration Manager használata nem kötelező. Használhat olyan API-t is, mint például a .NET-keretrendszer [ConfigurationManager osztálya](/dotnet/api/system.configuration.configurationmanager).