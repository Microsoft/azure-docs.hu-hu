---
title: Azure Hybrid Benefit
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Használjon meglévő SQL Server licenceket a Azure SQL Database és SQL Managed Instance kedvezményekhez.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779200"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit – Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A virtuálismag-alapú vásárlási modell kiépített számítási rétegében kedvezményes díjszabásra cserélheti meglévő licenceket a Azure SQL Database és Azure SQL Managed Instance a [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/) Ezzel az Azure-juttatással akár 30%-ot vagy akár magasabb megtakarítást is megtakaríthat a SQL Database & SQL Managed Instance- és SQL Server-licencei Frissítési Garancia. Az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) lapon található egy kalkulátor a megtakarítások meghatározásához.  Vegye figyelembe, hogy a Azure Hybrid Benefit kiszolgáló nélküli Azure SQL Database nem vonatkozik.

> [!NOTE]
> A Azure Hybrid Benefit nem igényel állásidőt.

![Virtuálismag-díjszabási struktúra](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Licencmodell kiválasztása

Az Azure Hybrid Benefit-val dönthet úgy, hogy csak a mögöttes Azure-infrastruktúráért fizet a meglévő SQL Server-licencének használatával a SQL Server-adatbázismotorhoz (alap számítási díjszabás), vagy fizethet a mögöttes infrastruktúráért és az SQL Server-licencért (licencbe foglalt díjszabás).

A licencelési modellt az Azure Portal: 
- Új adatbázisok esetén a létrehozás  során válassza az Adatbázis konfigurálása lehetőséget az Alapvető **beállítások** lapon, majd válassza a pénz megtakarítása lehetőséget.
- Meglévő adatbázisok esetén a Beállítások  **menüben** válassza a Konfigurálás lehetőséget, majd a megtakarításhoz lehetőséget.

Az alábbi API-k egyikével új vagy meglévő adatbázist is konfigurálhat:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A licenctípus beállítása vagy frissítése a PowerShell használatával:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A licenc típusának beállítása vagy frissítése az Azure CLI használatával:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST API](#tab/rest)

A licenctípus beállítása vagy frissítése a következő REST API:

- [Adatbázisok – Létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)
- [Adatbázisok – Frissítés](/rest/api/sql/databases/update)
- [Felügyelt példányok – létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)
- [Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit kérdések

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Vannak kettős használatú jogosultságok a Azure Hybrid Benefit-SQL Server?

A migrálás zökkenőmentes futtatásának biztosítása érdekében 180 napig rendelkezik a licenc kettős használatára vonatkozó jogosultságokkal. Ezt a 180 napos időszakot követően csak a felhőben SQL Server használhatja a SQL Database. Már nem rendelkezik kettős felhasználású jogosultságokkal a helyszínen és a felhőben.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben Azure Hybrid Benefit a SQL Server és a licenc mobilitása?

Licenc mobilitási előnyöket kínálunk a SQL Server ügyfelek Frissítési Garancia. Ez lehetővé teszi a licenceik ismételt hozzárendelését a partnerek megosztott kiszolgálóihoz. Ezt a kedvezményt az Azure IaaS és az AWS EC2 szolgáltatásban használhatja.

Azure Hybrid Benefit két SQL Server tér el a licences mobilitástól:

- Gazdasági előnyöket biztosít a nagymértékben virtualizált számítási feladatok Azure-ba való áthelyezéséhez. SQL Server Enterprise Edition-ügyfelek négy magot kaphatnak az Azure-ban általános célú termékváltozatban minden olyan maghoz, amely a helyszínen található a nagy mértékben virtualizált alkalmazásokhoz. A licenc mobilitása nem teszi lehetővé a virtualizált számítási feladatok felhőbe való áthelyezésének különleges költségbeli előnyeit.
- Olyan PaaS-célhelyet biztosít az Azure-ban (SQL Managed Instance), amely nagy mértékben kompatibilis a SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Milyen jogosultságok vonatkoznak a Azure Hybrid Benefit a SQL Server?

SQL Database és SQL Managed Instance-ügyfelek a következő jogosultságokkal Azure Hybrid Benefit a SQL Server:

|Licenclábnyom|Mit Azure Hybrid Benefit a SQL Server?|
|---|---|
|SQL Server Enterprise Edition fő ügyfeleinek SA-val|<li>Fizethet alapdíjat a hyperscale, általános célú vagy üzletileg kritikus termékváltozat alapján</li><br><li>1 magos helyszíni = 4 mag a nagy skálázású termékváltozatban</li><br><li>1 magos helyszíni = 4 mag általános célú termékváltozatban</li><br><li>1 magos helyszíni = 1 mag üzletileg kritikus termékváltozatban</li>|
|SQL Server Standard Edition fő ügyfeleinek SA-val|<li>Fizethet alapdíjat a hyperscale, általános célú vagy üzletileg kritikus termékváltozat alapján</li><br><li>1 magos helyszíni = 1 mag a hyperscale termékváltozatban</li><br><li>1 magos helyszíni = 1 mag általános célú termékváltozatban</li><br><li>4 magos helyszíni = 1 mag üzletileg kritikus termékváltozatban</li>|
|||


## <a name="next-steps"></a>Következő lépések

- Ha segítségre van szüksége az üzembehely Azure SQL kiválasztásához, tekintse meg [a megfelelő](azure-sql-iaas-vs-paas-what-is-overview.md)üzembe helyezési lehetőség kiválasztását a Azure SQL.
- A szolgáltatások és a SQL Database SQL Managed Instance összehasonlítását a SQL Database & SQL Managed Instance [tartalmaz.](database/features-comparison.md)
