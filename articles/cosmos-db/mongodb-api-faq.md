---
title: A Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdések
description: Válaszok a Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdésekre
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019013"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>A Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdések
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A MongoDB-hez készült Azure Cosmos DB API-protokoll kompatibilitási réteg, amely lehetővé teszi az alkalmazások számára, hogy a meglévő, Közösség által támogatott SDK-k és illesztőprogramok használatával könnyedén és transzparens módon kommunikáljanak a natív Azure Cosmos-adatbázis-motorral. A fejlesztők mostantól a meglévő MongoDB eszközlánccal és képességeiket is használhatják olyan alkalmazások készítéséhez, amelyek kihasználják a Azure Cosmos DB. A fejlesztők a Azure Cosmos DB egyedi képességeivel részesülnek, amelyek globális eloszlást biztosítanak a többrégiós írási replikációval, az automatikus indexeléssel, a biztonsági mentés karbantartásával, a pénzügyi támogatással járó szolgáltatói szerződésekkel (SLA) stb.

## <a name="how-do-i-connect-to-my-database"></a>Hogyan csatlakozni az adatbázishoz?

A MongoDB Azure Cosmos DB API-jával a Cosmos-adatbázishoz való kapcsolódás leggyorsabb módja a [Azure Portal](https://portal.azure.com). Lépjen a fiókjába, majd a bal oldali navigációs menüben kattintson a **gyorskonfigurálás** elemre. A gyors üzembe helyezés a legjobb módszer az adatbázishoz való kapcsolódáshoz szükséges kódrészletek beszerzésére.

Azure Cosmos DB kényszeríti a szigorú biztonsági követelményeket és szabványokat. Azure Cosmos DB fiókoknak hitelesítésre és biztonságos kommunikációra van szükségük TLS-n keresztül, ezért mindenképpen a TLS 1.2-es verzióját használja.

További információ: a [Cosmos-adatbázishoz való kapcsolódás Azure Cosmos db API-MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Hibakódok Azure Cosmos DB API-MongoDB való használatakor?

A Common MongoDB-hibakódokkal együtt a Azure Cosmos DB API-ját a MongoDB-hez saját hibakódok vonatkoznak. Ezek a [hibaelhárítási útmutatóban](mongodb-troubleshoot.md)találhatók.

## <a name="supported-drivers"></a>Támogatott illesztőprogramok

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Támogatott-e a MongoDB-illesztőprogram a Azure Cosmos DB API-MongoDB való használatra?

Igen, használhatja a Simba Mongo ODBC-illesztőjét Azure Cosmos DB API-val a MongoDB

## <a name="next-steps"></a>Következő lépések

* [.NET-Webalkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val](create-mongodb-dotnet.md)
* [Hozzon létre egy, a Java és a MongoDB API-t használó Console-alkalmazást Azure Cosmos DB](create-mongodb-java.md)
