---
title: Az Azure SQL-erőforrások létrehozására vagy módosítására szolgáló T-SQL-parancsok letiltása
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Ez a cikk egy olyan funkciót részletez, amely lehetővé teszi, hogy az Azure-rendszergazdák blokkolják a T-SQL-parancsokat Azure SQL-erőforrások létrehozásához vagy módosításához
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556136"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Mit takar a T-SQL szifilisz funkció?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Ez a funkció lehetővé teszi, hogy az Azure-rendszergazdák a T-SQL használatával letilthatják az Azure SQL-erőforrások létrehozását vagy módosítását. Ezt az előfizetés szintjén kell kikényszeríteni, hogy blokkolja a T-SQL-parancsokat az Azure SQL Database-adatbázisok vagy a felügyelt példányok SQL-erőforrásaiban.

## <a name="overview"></a>Áttekintés

Ha le szeretné tiltani az erőforrások létrehozását és módosítását a T-SQL használatával, és az erőforrás-kezelést egy adott előfizetéshez tartozó Azure Resource Manager sablon (ARM-sablon) használatával kívánja kikényszeríteni, az előfizetési szint előzetes verziójának funkciói Azure Portal használhatók. Ez különösen akkor hasznos, ha az [Azure-szabályzatok](/azure/governance/policy/overview) segítségével kényszeríti ki a szervezeti szabványokat ARM-sablonokon keresztül. Mivel a T-SQL nem tartja be az Azure-szabályzatokat, a T-SQL-létrehozási vagy-módosítási műveletek blokkját is alkalmazhatja. A letiltott szintaxis magában foglalja a szifilisz (létrehozás, frissítés, törlés) utasításait az Azure SQL, pontosabban `CREATE DATABASE` `ALTER DATABASE` és utasításokban található adatbázisokhoz `DROP DATABASE` . 

A T-SQL-alapú szifilisz-műveleteket Azure Portal, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)vagy [Azure CLI](/cli/azure/feature#az_feature_register)használatával lehet blokkolni.

## <a name="permissions"></a>Engedélyek

A szolgáltatás regisztrálásához vagy eltávolításához az Azure-felhasználónak az előfizetés tulajdonos vagy közreműködő szerepkör tagjának kell lennie.

## <a name="examples"></a>Példák

A következő szakasz azt ismerteti, hogyan regisztrálhatók és nem regisztrálhatók a Microsoft. SQL erőforrás-szolgáltatóhoz tartozó előzetes funkciók a Azure Portalban: 

### <a name="register-block-t-sql-crud"></a>A T-SQL-alapú szifilisz blokkolása

1. Azure Portal-előfizetéshez lépjen.
2. Válassza az **előzetes verziójú funkciók** lapot. 
3. Válassza a **T-SQL-alapú szifilisz tiltása** lehetőséget.
4. Miután kiválasztotta a **T-SQL-szifilisz blokkolását**, megnyílik egy új ablak, majd válassza a **regisztráció** lehetőséget a blokk regisztrálásához a Microsoft. SQL erőforrás-szolgáltatóval.

![Válassza a "T-SQL-szifilisz tiltása" lehetőséget az előzetes verziójú funkciók listájában.](./media/block-tsql-crud/block-tsql-crud.png)

![A "T-SQL szifilisz tiltása" jelölőnégyzet bejelölésével válassza a regisztráció lehetőséget.](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Regisztrálja újra a Microsoft. SQL erőforrás-szolgáltatót 
Miután regisztrálta a T-SQL-szifilisz blokkját a Microsoft. SQL erőforrás-szolgáltatóval, újra regisztrálnia kell a Microsoft. SQL erőforrás-szolgáltatót a módosítások érvénybe léptetéséhez. A Microsoft. SQL erőforrás-szolgáltató újbóli regisztrálása:

1. Azure Portal-előfizetéshez lépjen.
2. Válassza az **erőforrás-szolgáltatók** lapot.
3. Keresse meg és válassza a **Microsoft. SQL** erőforrás-szolgáltató elemet.
4. Válassza az **ismételt regisztráció** lehetőséget. 

> [!NOTE]
> Az Újraregisztrálás lépése kötelező, ha a T-SQL-blokkot alkalmazni szeretné az előfizetésre. 

![Regisztrálja újra a Microsoft. SQL erőforrás-szolgáltatót](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>A T-SQL-alapú szifilisz letiltása
Ha el szeretné távolítani a T-SQL létrehozási vagy módosítási műveleteit az előfizetésből, először törölje a korábban regisztrált T-SQL-blokk regisztrációját. Ezután regisztrálja újra a Microsoft. SQL erőforrás-szolgáltatót a fentiekben leírtak szerint a T-SQL-blokk eltávolításához. 


## <a name="next-steps"></a>Következő lépések

- [A Azure SQL Database biztonsági képességeinek áttekintése](security-overview.md)
- [Azure SQL Database ajánlott biztonsági eljárások](security-best-practice.md)
