---
title: Az Azure Table Storage támogatása Azure Cosmos DB
description: Megtudhatja, hogyan működnek együtt a Azure Cosmos DB Table API és az Azure Storage-táblák a műveletekkel azonos tábla adatmodell megosztásával
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c2b2a9a03a654e8ccba58e62a1d017cd11db5abc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050756"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB Table API és az Azure Table Storage használatával
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Az Azure Cosmos DB Table API és az Azure Table Storage ugyanazt a táblaadatmodellt használja, és ugyanazokat a létrehozási, törlési, frissítési és lekérdezési műveleteket teszik elérhetővé az SDK-kon keresztül.

> [!NOTE]
> A [kiszolgáló nélküli kapacitás mód](serverless.md) mostantól elérhető a Azure Cosmos db Table API.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB Table API-val

Az [Azure Cosmos DB Table API](table-introduction.md) jelenleg négy SDK-t biztosít a fejlesztéshez: 

* [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .net SDK. Ez a függvénytár a .NET szabványt célozza meg, és ugyanazokat az osztályokat és metódusokat tartalmazza, mint a nyilvános [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), de a Table API használatával is csatlakozhat Azure Cosmos db fiókokhoz. A .NET-keretrendszer [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) függvénytárának felhasználói a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -re való frissítéshez ajánlottak, mivel karbantartási módban vannak, és hamarosan elavultak lesznek.

* [PYTHON SDK](table-sdk-python.md): az új Azure Cosmos db Python SDK az egyetlen SDK, amely támogatja az Azure Table Storage-t a Pythonban. Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.

* [Java SDK](table-sdk-java.md): ez az Azure Storage SDK képes csatlakozni Azure Cosmos db fiókokhoz a Table API használatával.

* [Node.js SDK](table-sdk-nodejs.md): ez az Azure Storage SDK képes csatlakozni Azure Cosmos db fiókokhoz a Table API használatával.


A Table API használatával kapcsolatban a [Table API-val történő fejlesztéshez kapcsolódó gyakori kérdések](table-api-faq.md) között tekinthet meg további információt.

## <a name="developing-with-azure-table-storage"></a>Fejlesztés az Azure Table Storage használatával

Az Azure Table Storage az alábbi SDK-kat biztosítja a fejlesztéshez:

- A [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), a [Microsoft. Azure. Storage. file](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), a [Microsoft. Azure. Storage. üzenetsor](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)és a [Microsoft. Azure. Storage. Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) librarys lehetővé teszi, hogy együttműködjön az Azure Table Storage szolgáltatással. Ha a Azure Cosmos DB Table API használja, használhatja a [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) függvénytárat.
- [PYTHON SDK](https://github.com/Azure/azure-cosmos-table-python). A Pythonhoz készült Azure Cosmos DB Table SDK támogatja a Table Storage szolgáltatást (mivel az Azure Table Storage és a Cosmos DB Table API ugyanazokkal a szolgáltatásokkal és funkciókkal rendelkeznek, és az SDK fejlesztési törekvéseinek factorize érdekében javasoljuk, hogy használja ezt az SDK-t).
- [A Java-hoz készült Azure Storage SDK](https://github.com/azure/azure-storage-java). Ez az Azure Storage SDK ügyféloldali kódtárat biztosít a Java-szolgáltatásban az Azure Table Storage használatához.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Ez az SDK egy Node.js-csomagot és egy böngészőkompatibilis JavaScript ügyféloldali kódtárat biztosít a Storage Table Service használatához.
- [AzureRmStorageTable PowerShell-modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ez a PowerShell-modul parancsmagokat biztosít a Storage Tables használatához.
- [Az Azure Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp/). Ez a kódtár lehetővé teszi alkalmazások létrehozását az Azure Storage-ban.
- [Az Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő Ruby-csomagot biztosít.
- [Azure Storage Table ügyféloldali PHP-kódtára](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő ügyféloldali PHP-kódtárat biztosít.


   





