---
title: sys.external_job_streams (Transact-SQL) – Azure SQL Edge
description: Tudnivalók a sys.external_job_streams használatáról az Azure SQL Edge-ben
keywords: sys.external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900350"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Egy olyan sort ad vissza, amely a külső adatfolyam-feladathoz hozzárendelt bemeneti vagy kimeneti külső stream objektumra vonatkozik.

|Oszlop neve|Adattípus|Leírás|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| A folyamatos átviteli feladattípushoz tartozó objektum-azonosító szám. Ez az oszlop a sys.external_streaming_jobs object_id oszlopát képezi le.|
|**stream_id**|**int**| Az adatfolyam-objektumhoz tartozó objektum-azonosító szám. Ez az oszlop a sys.external_streams object_id oszlopát képezi le. |
|**is_input**|**bit**| 1 Ha a Stream-objektum bemenetet használ a folyamatos átviteli feladathoz, ellenkező esetben 0.|
|**is_output**|**bit**| 1 Ha a Stream objektum kimenetet használ a folyamatos átviteli feladatokhoz, ellenkező esetben 0.|

## <a name="example"></a>Példa

Ez a katalógus-nézet a `sys.external_streams` és a `sys.external_streaming_jobs` katalógus nézeteivel együtt használható. Alább látható egy minta lekérdezés

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Engedélyek

A metaadatoknak a katalógus nézeteiben való láthatósága azon biztonságos elemek migrálására korlátozódik, amelyeknek a felhasználó tulajdonában van, vagy amelyeken a felhasználó engedélyt kapott. További információ: [metaadatok láthatóságának konfigurálása](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Lásd még

- [Katalógus nézetei (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Rendszernézetek (Transact-SQL)](/sql/t-sql/language-reference/)
