---
title: Azure Analysis Services kezelése a PowerShell használatával | Microsoft Docs
description: Ismerteti Azure Analysis Services PowerShell-parancsmagokat a gyakori felügyeleti feladatokhoz, például a kiszolgálók létrehozásához, a műveletek felfüggesztéséhez vagy a szolgáltatási szint módosításához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018763"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Az Azure Analysis Services kezelése a PowerShell-lel

Ez a cikk a Azure Analysis Services kiszolgáló-és adatbázis-felügyeleti feladatainak végrehajtásához használt PowerShell-parancsmagokat ismerteti. 

A kiszolgáló erőforrás-kezelési feladatai, például a kiszolgálók létrehozása vagy törlése, a kiszolgálói műveletek felfüggesztése vagy folytatása, vagy a szolgáltatási szint (szint) módosítása Azure Analysis Services parancsmagokat használ. Egyéb feladatok olyan adatbázisok kezeléséhez, mint például a szerepkörök tagjainak hozzáadása vagy eltávolítása, a feldolgozás vagy a particionálási parancsmagok, amelyek ugyanabban a SqlServer-modulban szerepelnek, mint a SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladat megköveteli, hogy rendszergazdai jogosultságokkal rendelkezzen a kezelt Analysis Services-kiszolgálón. Az ütemezett PowerShell-feladatok felügyelet nélküli műveletek. Az ütemező szolgáltatást futtató fióknak vagy szolgáltatásnak rendszergazdai jogosultságokkal kell rendelkeznie a Analysis Services kiszolgálón. 

Azure PowerShell parancsmagokat használó kiszolgálói műveletek esetén a fióknak vagy a Feladatütemezőt futtató fióknak az erőforrás tulajdonosi szerepköréhez is tartoznia kell az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)szolgáltatásban. 

## <a name="resource-and-server-operations"></a>Erőforrás-és kiszolgálói műveletek 

Modul telepítése – [az. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentáció – [az. AnalysisServices-hivatkozás](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Adatbázis-műveletek

Azure Analysis Services adatbázis-műveletek ugyanazt a SqlServer modult használják, mint SQL Server Analysis Services. Azonban nem minden parancsmag támogatott a Azure Analysis Services. 

A SqlServer modul a feladat-specifikus adatbázis-kezelési parancsmagokat, valamint az általános célú Invoke-ASCmd parancsmagot is tartalmazza, amely egy táblázatos modell parancsnyelv (TMSL) lekérdezését vagy parancsfájlját fogadja el. Azure Analysis Services a következő parancsmagok támogatottak a SqlServer modulban.

Modul telepítése – [SQLServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentáció – [SQLServer-hivatkozás](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Támogatott parancsmagok

|Parancsmag|Leírás|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Vegyen fel egy tagot egy adatbázis-szerepkörbe.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Analysis Services adatbázis biztonsági mentése.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Egy tag eltávolítása egy adatbázis-szerepkörből.|   
|[Meghívás – ASCmd](/powershell/module/sqlserver/invoke-ascmd)|TMSL-szkript végrehajtása.|
|[Meghívás – ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Adatbázis feldolgozása|  
|[Meghívás – ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Partíció feldolgozása.| 
|[Meghívás – ProcessTable](/powershell/module/sqlserver/invoke-processtable)|Tábla feldolgozása|  
|[Egyesítés – partíció](/powershell/module/sqlserver/merge-partition)|Partíció egyesítése.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Analysis Services adatbázis visszaállítása.| 
  

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell-modul letöltése](/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer modul a PowerShell-galéria](https://www.powershellgallery.com/packages/SqlServer)    
* [Táblázatos modellek programozása a 1200-es és magasabb kompatibilitási szinthez](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)