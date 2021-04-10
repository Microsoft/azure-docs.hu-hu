---
title: Azure Cosmos DB Table API .NET SDK &-erőforrások
description: Ismerkedjen meg a .NET Azure Cosmos DB Table APIével, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és az egyes verziók közötti módosításokat.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566829"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB table .NET API: letöltési és kibocsátási megjegyzések
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Hivatkozások|
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Gyors útmutató**|[Azure Cosmos DB: alkalmazás létrehozása a .NET-tel és a Table API](create-table-dotnet.md)|
|**Oktatóanyag**|[Azure Cosmos DB: Fejlesztés a Table API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md)|
|**Jelenleg támogatott keretrendszer**|[Microsoft .NET-keretrendszer 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> A .NET-keretrendszer SDK [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) karbantartási módban van, és hamarosan elavulttá válik. Frissítsen a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) új .NET Standard Library webhelyre, és folytassa a Table API által támogatott legújabb funkciók beszerzésével.

> Ha az előzetes verzióban hozta létre a Table API-fiókot, hozzon létre egy [új Table API-fiókot](create-table-dotnet.md#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Hibajavítások

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Hibajavítások

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Többrégiós írási támogatás hozzáadva
* Rögzített NuGet-csomag függőségei Microsoft.Azure.DocumentDB, Microsoft. OData. Core, Microsoft. OData. EDM, Microsoft. térbeli

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Rögzített NuGet-csomagok függőségei a Microsoft. Azure. Storage. Common és a Microsoft.Azure.DocumentDB.
* Hibajavítások a tábla szerializálásakor, ha a JsonConvert. DefaultSettings konfigurálva van.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* A helytelenül formázott Etagek való ellenőrzésének hozzáadása közvetlen módban.
* Rögzített LINQ lekérdezési hiba az átjáró módban.
* A szinkron API-k mostantól a SynchronizationContext tulajdonságot segítségével futnak a szál készletében.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism és TableQueryContinuationTokenLimitInKb hozzáadása a TableRequestOptions
* Hibajavítások

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Általánosan elérhető kiadás

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0 – előzetes verzió

* Kezdeti előzetes kiadás

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

A `Microsoft.Azure.CosmosDB.Table` függvénytár jelenleg csak a .net-keretrendszerhez érhető el, és karbantartási üzemmódban van, és hamarosan elavulttá válik. Új funkciók és funkciók és optimalizálások csak a .NET Standard Library [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)számára vehetők fel, ezért azt javasoljuk, hogy frissítsen a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)verzióra.

A [WindowsAzure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) előzetes verziója elavult. A WindowsAzure. Storage-PremiumTable SDK 2018 november 15-én megszűnik, amikor a kivont SDK-ra irányuló kérések nem lesznek engedélyezve.

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [2.1.2](#2.1.2) |Szeptember 16., 2019| |
| [2.1.0](#2.1.0) |2019. január 22.|Április 01., 2020 |
| [2.0.0](#2.0.0) |Szeptember 26., 2018|Március 01., 2020 |
| [1.1.3](#1.1.3) |2018. július 17.|December 01., 2019 |
| [1.1.1](#1.1.1) |Március 26., 2018|December 01., 2019 |
| [1.1.0](#1.1.0) |Február 21., 2018|December 01., 2019 |
| [1.0.0](#1.0.0) |2017. november 15.|November 15., 2019 |
| 0.9.0 – előzetes verzió |November 11., 2017 |November 11., 2019 |

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a hibaüzenet jelenik meg 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

a Microsoft. Azure. CosmosDB. table NuGet-csomag használatának megkísérlése során két lehetőség közül választhat a probléma megoldásához:

* A csomag kezelése konzollal telepítse a Microsoft. Azure. CosmosDB. table csomagot és annak függőségeit. Ehhez írja be a következőt a megoldáshoz tartozó Package Manager konzolon. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Az előnyben részesített NuGet csomagkezelő eszköz használatával telepítse a Microsoft. Azure. Storage. Common NuGet csomagot a Microsoft. Azure. CosmosDB. table telepítése előtt.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

Ha többet szeretne megtudni a Azure Cosmos DB Table APIról, tekintse meg a [Azure Cosmos DB Table API bemutatása](table-introduction.md)című témakört. 
