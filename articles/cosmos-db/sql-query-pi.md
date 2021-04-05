---
title: PI Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function PI Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0ebe94d8b06c342fc61fae4255fa354150974c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341708"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A PI konstans értékét adja vissza.  
  
## <a name="syntax"></a>Syntax
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a értékét adja vissza `PI` .  
  
```sql
SELECT PI() AS pi 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
