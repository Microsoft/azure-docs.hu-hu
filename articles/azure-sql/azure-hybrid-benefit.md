---
title: Azure Hybrid Benefit
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meglévő SQL Server-licencek használata a Azure SQL Database és az SQL felügyelt példányának kedvezményei esetén.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: f7a37e761e37e295bbb92e442b1813ebded2a7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955278"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit – Azure SQL Database & SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A virtuális mag-alapú vásárlási modell kiépített számítási szintjein a meglévő licenceket a Azure SQL Database és az Azure SQL felügyelt példányain a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával lehet cserélni. Ez az Azure-kedvezmény lehetővé teszi, hogy a SQL Server-licencek frissítési garanciával való használatával akár 30%-ot vagy akár magasabbat is megtakarítson SQL Database & SQL felügyelt példányán. A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) oldalon a megtakarítások meghatározását segítő számológép szerepel.  Vegye figyelembe, hogy a Azure Hybrid Benefit nem vonatkozik Azure SQL Database kiszolgáló nélküli kiszolgálóra.

> [!NOTE]
> A Azure Hybrid Benefitra való váltás nem igényel állásidőt.

![virtuális mag árképzési struktúra](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Licencelési modell kiválasztása

A Azure Hybrid Benefit segítségével csak az alapul szolgáló Azure-infrastruktúráért kell fizetnie, ha a meglévő SQL Server licencét a SQL Server adatbázis-működtető motorhoz (az alapszintű számítási díjszabáshoz) használja, vagy a mögöttes infrastruktúrára és a SQL Server licencre (licenccel együtt) is fizethet.

A licencelési modellt kiválaszthatja vagy módosíthatja a Azure Portalban: 
- Új adatbázisok esetén a létrehozás során válassza az **adatbázis konfigurálása** lehetőséget az **alapok** lapon, és válassza a pénz megtakarítása lehetőséget.
- A meglévő adatbázisok esetében válassza a **Konfigurálás** lehetőséget a **Beállítások** menüben, és válassza a pénz megtakarításának lehetőségét.

A következő API-k egyikével is konfigurálhat új vagy meglévő adatbázist:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A licenc típusának beállítása vagy frissítése a PowerShell használatával:

- [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A licenc típusának beállítása vagy frissítése az Azure CLI használatával:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [az SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

A licenc típusának beállítása vagy frissítése a REST API használatával:

- [Adatbázisok – létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)
- [Adatbázisok – frissítés](/rest/api/sql/databases/update)
- [Felügyelt példányok – létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)
- [Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit kérdések

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Van-e kettős használati jogosultsággal a SQL Server Azure Hybrid Benefit?

A licencek 180 napos kettős használati jogosultsággal rendelkeznek, így biztosítható, hogy a Migrálás zökkenőmentesen fusson. Az 180 napos időszak elteltével a felhőben csak SQL Database használhatja a SQL Server-licencet. Már nem rendelkezik kettős használati jogokkal a helyszínen és a felhőben.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben különbözik a SQL Server Azure Hybrid Benefit a licencek mobilitásával?

A licencek mobilitási előnyeit a frissítési garanciával rendelkező ügyfeleink SQL Server biztosítjuk. Ez lehetővé teszi a licencek újbóli hozzárendelését egy partner megosztott kiszolgálóin. Ezt az előnyt az Azure IaaS és az AWS EC2 is használhatja.

A SQL Server Azure Hybrid Benefit két kulcsfontosságú területen különbözik a licenc mobilitástól:

- Gazdasági előnyöket biztosít a nagyvirtualizált munkaterhelések Azure-ba való áthelyezéséhez. A SQL Server Enterprise Edition rendszerű ügyfelek négy maggal rendelkezhetnek az Azure-ban az általános célú SKU-ban minden olyan mag számára, amely a helyszínen a nagyvállalati szintű virtualizált alkalmazások számára készült. A licencek mobilitása nem teszi lehetővé a virtualizált számítási feladatok felhőbe való áthelyezésének speciális költségeit.
- A szolgáltatás az Azure-ban (SQL felügyelt példányon) található, nagyszámú, SQL Servertel kompatibilis

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Milyen jogokkal rendelkezik a SQL Server Azure Hybrid Benefit?

A SQL Database és az SQL felügyelt példányának ügyfelei a következő jogokkal rendelkeznek a SQL Serverhoz tartozó Azure Hybrid Benefithoz:

|Licenc-lábnyom|Mire Azure Hybrid Benefit a SQL Server?|
|---|---|
|SQL Server Enterprise Edition Core-ügyfelek a SA-val|<li>A nagy kapacitású, általános célú vagy üzletileg kritikus SKU-ra is fizethet alapdíjat</li><br><li>1 mag helyszíni = 4 mag a nagy kapacitású SKU-ban</li><br><li>1 mag helyszíni = 4 mag általános célú SKU-ban</li><br><li>1 mag a helyszínen = 1 mag üzletileg kritikus SKU-ban</li>|
|SQL Server Standard Edition Core-ügyfelek a SA-val|<li>A nagy kapacitású, általános célú vagy üzletileg kritikus SKU-ra is fizethet alapdíjat</li><br><li>1 mag a helyszínen = 1 mag a nagy kapacitású SKU-ban</li><br><li>1 mag a helyszínen = 1 mag általános célú SKU-ban</li><br><li>4 mag helyszíni = 1 mag üzletileg kritikus SKU-ban</li>|
|||


## <a name="next-steps"></a>Következő lépések

- Az Azure SQL-alapú üzembe helyezési lehetőség kiválasztásával kapcsolatos segítségért lásd: [a megfelelő üzembe helyezési lehetőség kiválasztása az Azure SQL-ben](azure-sql-iaas-vs-paas-what-is-overview.md).
- A SQL Database és az SQL felügyelt példány funkcióinak összehasonlítását lásd: [SQL Database & SQL felügyelt példányának funkciói](database/features-comparison.md).
