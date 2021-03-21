---
title: Az adatforráshoz való kapcsolódás beállítása felügyelt identitás használatával
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan állíthat be indexelő-kapcsolatokat egy adatforráshoz felügyelt identitás használatával
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519588"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Indexelő-kapcsolatok beállítása egy adatforráshoz felügyelt identitás használatával

> [!IMPORTANT] 
> A felügyelt identitást használó adatforráshoz való kapcsolódás nem támogatott az ingyenes Azure Cognitive Search-csomaggal.

Az Azure Cognitive Search [Indexelő](search-indexer-overview.md) egy olyan webbejáró, amely lehetővé teszi az adatok adatforrásból való lekérését az Azure Cognitive Searchba. Az indexelő beszerez egy adatforrás-kapcsolatokat a létrehozott adatforrás-objektumból. Az adatforrás-objektum általában tartalmazza a cél adatforráshoz tartozó hitelesítő adatokat. Az adatforrás-objektum például tartalmazhat egy Azure Storage-fiók kulcsát, ha blob Storage-tárolóból kívánja indexelni az adatait.

Sok esetben a hitelesítő adatok közvetlenül az adatforrás-objektumban való megadása nem jelent problémát, de vannak olyan kihívások, amelyek felhasználhatók:
* Hogyan megőrizni a hitelesítő adatokat a kódban, amely létrehozza az adatforrás-objektumot?
* Ha a fiók kulcsa vagy jelszava sérült, és módosítani szeretném, most frissíteni kell az adatforrás-objektumokat az új fiók kulcsával vagy jelszavával, hogy az indexelő újra csatlakozhasson az adatforráshoz.

A probléma megoldásához felügyelt identitás használatával kell beállítania a kapcsolatokat.

## <a name="using-managed-identities"></a>Felügyelt identitások használata

A [felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md) szolgáltatás egy automatikusan felügyelt identitással rendelkező Azure-szolgáltatásokat biztosít Azure Active Directory (Azure ad). Ezt a funkciót az Azure Cognitive Searchban olyan adatforrás-objektum létrehozására használhatja, amely nem tartalmaz hitelesítő adatokat. Ehelyett a keresési szolgáltatás az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával fogja biztosítani az adatforráshoz való hozzáférést.

Ha felügyelt identitással állítja be az adatforrást, módosíthatja az adatforrás hitelesítő adatait, és az indexelő továbbra is csatlakozhatnak az adatforráshoz. Az adatforrás-objektumokat a kódban is létrehozhatja anélkül, hogy hozzá kellene adnia egy fiókot, vagy a Key Vaultt kell használnia a fiók kulcsainak lekéréséhez.

## <a name="limitations"></a>Korlátozások

A következő adatforrások támogatják az indexelő-kapcsolatok felügyelt identitások használatával történő beállítását. 

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (előzetes verzió), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

A következő szolgáltatások jelenleg nem támogatják a felügyelt identitások használatát a kapcsolódás beállításához:
* Tudástár
* Egyéni készségek
 
## <a name="next-steps"></a>Következő lépések

További információ az indexelő-kapcsolatok felügyelt identitások használatával történő beállításáról:

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (előzetes verzió), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)