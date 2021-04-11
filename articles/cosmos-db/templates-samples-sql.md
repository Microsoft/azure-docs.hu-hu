---
title: Azure Resource Manager sablonok a Azure Cosmos DB Core-hoz (SQL API)
description: Azure Cosmos DB létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: 7163658024d150a7c5d75c3b3ac0b6b6b29cd3cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105037308"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sablonok az Azure Cosmos DB-hez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk csak az alapszintű (SQL) API-fiókok Azure Resource Manager sablonját mutatja be. A [Cassandra](templates-samples-cassandra.md), a [Gremlin](templates-samples-gremlin.md), a [MongoDB](templates-samples-mongodb.md)és a [Table](templates-samples-table.md) API-khoz példákat is találhat.

## <a name="core-sql-api"></a>Core (SQL) API

|**Sablon**|**Leírás**|
|---|---|
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása az autoscale átviteli sebességgel](manage-with-templates.md#create-autoscale) | Ez a sablon egy alapszintű (SQL-) API-fiókot hoz létre két régióban, egy adatbázist és egy tárolót az automatikusan méretezhető átviteli sebességgel. |
|[Azure Cosmos-fiók, adatbázis, tároló létrehozása analitikus tárolóval](manage-with-templates.md#create-analytical-store) | Ez a sablon létrehoz egy core (SQL) API-fiókot az egyik régióban egy olyan tárolóval, amelyen engedélyezve van az analitikai TTL, valamint a manuális vagy automatikus méretezési sebesség használata. |
|[Hozzon létre egy Azure Cosmos-fiókot, egy adatbázist, egy standard (manuális) átviteli sebességgel rendelkező tárolót](manage-with-templates.md#create-manual) | Ez a sablon létrehoz egy core (SQL) API-fiókot két régióban, egy adatbázist és egy standard átviteli sebességgel rendelkező tárolót. |
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása tárolt eljárással, triggerrel és UDF-vel](manage-with-templates.md#create-sproc) | Ez a sablon egy alapszintű (SQL-) API-fiókot hoz létre két régióban egy tárolóhoz tárolt eljárással, triggerrel és UDF-vel. |
|[Azure Cosmos-fiók létrehozása az Azure AD-identitással, a szerepkör-definíciókkal és a szerepkör-hozzárendeléssel](manage-with-templates.md#create-rbac) | Ez a sablon egy alapszintű (SQL-) API-fiókot hoz létre az Azure AD-identitással, a szerepkör-definíciókkal és a szerepkör-hozzárendeléssel. |
|[Privát végpont létrehozása meglévő Azure Cosmos-fiókhoz](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ez a sablon egy meglévő virtuális hálózat meglévő Azure Cosmos Core-(SQL-) API-fiókjához hoz létre privát végpontot. |
|[Ingyenes szintű Azure Cosmos-fiók létrehozása](manage-with-templates.md#free-tier) |  Ez a sablon egy Azure Cosmos DB Core (SQL) API-fiókot hoz létre a szabad szintű szinten. |

Tekintse meg a dokumentációhoz [Azure Resource Manager referenciát Azure Cosmos db](/azure/templates/microsoft.documentdb/allversions) oldalon.
