---
title: Címkék használata a hangeszközök lekérdezéséhez
description: Tippek Címkék használatával a dedikált SQL-készletekhez tartozó lekérdezésekhez az Azure szinapszis Analyticsben.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462753"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Címkék használata a dedikált SQL-készletekre vonatkozó lekérdezésekhez az Azure szinapszis Analyticsben

Ebben a cikkben a megoldás olyan tippekkel szolgál, amelyekkel a dedikált SQL-készletekben címkék segítségével hozhatja ki a megoldásokat.

## <a name="what-are-labels"></a>Mik a Címkék?

A dedikált SQL-készlet támogatja a lekérdezési címkék nevű koncepciót. Mielőtt bármilyen mélységbe kerül, tekintsük át a következő példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sorban a "saját lekérdezés" karakterláncot kell a lekérdezéshez felcímkézni. Ez a címke hasznos lehet, mert a címke lekérdezési lehetőséggel rendelkezik a DMV.

A címkék lekérdezése egy olyan mechanizmust biztosít, amely lehetővé teszi a problémák lekérdezését, és segít az előrehaladás azonosításában egy ELT futtatásával.

A jó elnevezési konvenció igazán segít. Ha például a címkét a projekt, eljárás, utasítás vagy Megjegyzés alapján indítja el, egyedi módon azonosítja a lekérdezést a verziókövetés összes kódja között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerinti kereséshez:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során elengedhetetlen, hogy szögletes zárójeleket vagy idézőjeleket helyezzen el a szó címkéje körül. A címke egy foglalt szó, és hibát okoz, ha nincs elválasztva.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
