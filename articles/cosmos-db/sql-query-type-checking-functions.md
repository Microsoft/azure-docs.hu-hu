---
title: Típus-ellenőrzési függvények Azure Cosmos DB lekérdezési nyelven
description: Tudnivalók az SQL rendszerfunkcióinak ellenőrzéséről Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546352"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Type Check functions (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A típus-ellenőrzési függvények lehetővé teszik egy kifejezés típusának ellenőrzését egy SQL-lekérdezésen belül. A Type-Check függvények segítségével meghatározhatja, hogy milyen típusú tulajdonságok vannak a menet közben, ha változó vagy ismeretlen. 

## <a name="functions"></a>Functions

Az alábbi táblázat a támogatott beépített típus-ellenőrzési funkciókat tartalmazza:

A következő függvények támogatják a bemeneti értékek típusának ellenőrzését, és mindegyik logikai értéket ad vissza.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Következő lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregate-functions.md)
