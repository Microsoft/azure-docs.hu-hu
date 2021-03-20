---
title: sys.sp_cleanup_data_retention (Transact-SQL) – Azure SQL Edge
description: Tudnivalók a sys.sp_cleanup_data_retention (Transact-SQL) használatáról az Azure SQL Edge-ben
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935758"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**A következőkre vonatkozik:** Azure SQL Edge

Elvégzi az elavult rekordok törlését olyan táblákból, amelyeken engedélyezve van az adatmegőrzési szabályzat. További információ: [adatmegőrzés](data-retention-overview.md). 

## <a name="syntax"></a>Szintaxis 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumentumok  
`[ @schema_name = ] schema_name`    
 A azon tábla tulajdonosi sémájának a neve, amelyen a tisztítást el kell elvégezni. *schema_name* egy **rendszerneve** típusú kötelező paraméter.
  
`[ @table_name = ] 'table_name'`    
 Annak a táblának a neve, amelyen a kitakarítási műveletet végre kell hajtani. *table_name* egy **rendszerneve** típusú kötelező paraméter.

## <a name="output-parameter"></a>Kimeneti paraméter  

`[ @rowcount = ] rowcount OUTPUT`   
 a sorszám egy opcionális kimeneti paraméter, amely a táblából a rekordok karbantartásának számát jelöli. a *sorszám* int.
  
## <a name="permissions"></a>Engedélyek    
 Db_owner engedélyekre van szükség.

## <a name="next-steps"></a>Következő lépések
- [Adatmegőrzés és az automatikus Adattisztítás](data-retention-overview.md)
- [Korábbi adatok kezelése adatmegőrzési házirenddel](data-retention-cleanup.md) 
- [Az adatmegőrzés engedélyezése és letiltása](data-retention-enable-disable.md)
