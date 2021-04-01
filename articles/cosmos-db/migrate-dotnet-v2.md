---
title: Az alkalmazás migrálása a Azure Cosmos DB .NET SDK 2,0 (Microsoft. Azure. Cosmos) használatára
description: Ismerje meg, hogyan frissítheti meglévő .NET-alkalmazását a v1 SDK-ból a .NET SDK v2 for Core (SQL) API-ra.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94550084"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Az alkalmazás migrálása a Azure Cosmos DB .NET SDK v2 használatára

> [!IMPORTANT]
> Fontos megjegyezni, hogy jelenleg a .NET SDK v3 verziója érhető el, és a v2 és v3 közötti áttelepítési terv [itt](migrate-dotnet-v3.md)érhető el. Ha szeretne többet megtudni a Azure Cosmos DB .NET SDK v2-ről, tekintse meg a [kibocsátási megjegyzéseket](sql-api-sdk-dotnet.md), a [.net GitHub-tárházat](https://github.com/Azure/azure-cosmos-dotnet-v2), a .net SDK v2 [teljesítménnyel kapcsolatos tippeket](performance-tips.md)és a [hibaelhárítási útmutatót](troubleshoot-dot-net-sdk.md).
>

Ebből a cikkből megtudhatja, hogyan frissíthet meglévő v1 .NET-alkalmazást Azure Cosmos DB .NET SDK v2 for Core (SQL) API-ra. Azure Cosmos DB .NET SDK v2 megfelel a `Microsoft.Azure.DocumentDB` névtérnek. Az ebben a dokumentumban szereplő információkat akkor használhatja, ha az alkalmazást a következő Azure Cosmos DB .NET-platformok bármelyikéről telepíti át a v2 SDK használatára `Microsoft.Azure.Cosmos` :

* Azure Cosmos DB .NET-keretrendszer v1 SDK az SQL API-hoz
* Azure Cosmos DB .NET Core SDK v1 az SQL API-hoz

## <a name="whats-available-in-the-net-v2-sdk"></a>A .NET v2 SDK-ban elérhető elemek

A v2 SDK számos használhatósági és teljesítménybeli javítást tartalmaz, többek között a következőket:

* A TCP Direct mód támogatása nem Windows rendszerű ügyfelek esetén
* Többrégiós írási támogatás
* A lekérdezés teljesítményének fejlesztése
* Térinformatikai/geometriai gyűjtemények és indexelés támogatása
* A Direct/TCP átviteli diagnosztika nagyobb mértékű fejlesztése
* A közvetlen TCP átviteli verem frissítései a létesített kapcsolatok számának csökkentése érdekében
* A RequestTimeout késések csökkentésének fejlesztése

A legtöbb újrapróbálkozási logika és az SDK alacsonyabb szintjei nagyrészt változatlanok maradnak.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Miért érdemes migrálni a .NET v2 SDK-ra

A számos teljesítménybeli fejlesztés mellett a legújabb SDK-ban végrehajtott új funkció-beruházások nem lesznek a régebbi verzióra portolva.

Emellett a régebbi SDK-k újabb verziókra lesznek lecserélve, a v1 SDK pedig [karbantartási módba](sql-api-sdk-dotnet.md)lép. A legjobb fejlesztési élmény érdekében javasoljuk, hogy az alkalmazást az SDK újabb verziójára migrálja.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Jelentős változások a v1 SDK-ból v2 SDK-ra

### <a name="direct-mode--tcp"></a>Közvetlen mód + TCP

A .NET v2 SDK mostantól támogatja a közvetlen és az átjáró üzemmódot is. A közvetlen mód támogatja a TCP protokollon keresztüli csatlakozást, és jobb teljesítményt nyújt, mivel közvetlenül csatlakozik a háttérbeli replikához kevesebb hálózati ugrással.

További részletekért olvassa el a [Azure Cosmos db SQL SDK kapcsolati módok útmutatóját](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Munkamenet-jogkivonat formázása

A v2 SDK már nem a v1-ben használt *egyszerű* munkamenet-jogkivonat formátumát használja, ehelyett az SDK *vektoros* formázást használ. A formátumot át kell alakítani, ha az ügyfélalkalmazás más verziókkal való továbbítása történik, mivel a formátumok nem felcserélhetők.

További információ: [a munkamenet-tokenek formátumának konvertálása a .net SDK-ban](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>A .NET Change feed Processor SDK használata

A .NET-módosítási hírcsatorna 2.1. x `Microsoft.Azure.DocumentDB` verziójához 2,0-es vagy újabb verzió szükséges.

A 2.1. x függvénytár a következő kulcsokat módosítja:

* A stabilitás és a diagnosztizálás fejlesztése
* A hibák és kivételek jobb kezelése
* További támogatás a particionált bérletek gyűjteményéhez
* Speciális bővítmények az interfész és az osztály megvalósításához a `ChangeFeedDocument` hibák további kezelésére és nyomon követésére
* Egy egyéni tároló használatának támogatása a folytatási tokenek megtartásához partíción

További információ: a hírcsatorna-feldolgozó függvénytárra vonatkozó [kibocsátási megjegyzések](sql-api-sdk-dotnet-changefeed.md)módosítása.

### <a name="using-the-bulk-executor-library"></a>A tömeges végrehajtó kódtár használata

A v2 tömeges végrehajtó könyvtára jelenleg függőséggel rendelkezik a Azure Cosmos DB .NET SDK 2.5.1-es vagy újabb verziójával.

További információ: [Azure Cosmos db tömeges végrehajtó függvénytárának áttekintése](bulk-executor-overview.md) és a .net tömeges végrehajtó függvénytárának [kibocsátási megjegyzései](sql-api-sdk-bulk-executor-dot-net.md).

## <a name="next-steps"></a>Következő lépések

* [További teljesítménnyel kapcsolatos tippek](sql-api-get-started.md) a Azure Cosmos db for SQL API v2 használatával az alkalmazás optimalizálásához a maximális teljesítmény érdekében
* További információ [a v2 SDK-val kapcsolatos lehetőségekről](sql-api-dotnet-samples.md)