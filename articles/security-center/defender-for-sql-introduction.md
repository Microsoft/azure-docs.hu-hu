---
title: Azure Defender for SQL – az előnyök és funkciók
description: Ismerje meg az Azure Defender for SQL előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 81ae46a7b6c19557eeadd2d5b28ad2fa46e77115
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881367"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Az SQL-hez készült Azure Defender bemutatása

Az SQL-hez készült Azure Defender két Azure Defender-csomagot tartalmaz, amelyek kiterjesztik Azure Security Center [adatbiztonsági csomagját](../azure-sql/database/azure-defender-for-sql.md) , hogy az adatbázisok és az adataik bárhonnan biztonságosak legyenek. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|**Azure Defender az Azure SQL Database-kiszolgálókhoz** – általánosan elérhető (GA)<br>**Azure Defender a gépeken futó SQL-kiszolgálókhoz** – általánosan elérhető (GA) |
|Árképzési|Az **Azure Defender for SQL** -t alkotó két csomag számlázása [a díjszabási oldalon](security-center-pricing.md) látható módon történik|
|Védett SQL-verziók:|[SQL Azure-beli virtuális gépeken](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure arc-kompatibilis SQL-kiszolgálók](/sql/sql-server/azure-arc/overview)<br>Helyszíni SQL-kiszolgálók a Windows rendszerű gépeken az Azure arc nélkül<br>Önálló Azure SQL- [adatbázisok](../azure-sql/database/single-database-overview.md) és [rugalmas készletek](../azure-sql/database/elastic-pool-overview.md)<br>[Felügyelt Azure SQL-példány](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure szinapszis Analytics (korábban SQL DW) dedikált SQL-készlet](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Felhők|![Igen ](./media/icons/yes-icon.png) kereskedelmi felhők<br>![Igen ](./media/icons/yes-icon.png) US gov<br>![Igen ](./media/icons/yes-icon.png) China gov (**részleges**: a riasztások részhalmaza és az SQL Server sebezhetőségi felmérése. A viselkedési veszélyforrások elleni védelem nem érhető el.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Mire használható az Azure Defender az SQL-védelemhez?

**Az Azure Defender for SQL** két különálló Azure Defender-csomagból áll:

- **Az Azure Defender for Azure SQL Database-kiszolgálók a** következőket védik:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Felügyelt Azure SQL-példány](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Dedikált SQL-készlet az Azure Szinapszisban](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- Az **Azure Defender az SQL-kiszolgálókon a gépeken** kiterjeszti az Azure-natív SQL-kiszolgálók védelmét, így teljes mértékben támogatja a hibrid környezeteket, és védelmet biztosít az Azure-ban, más felhőalapú környezetekben és akár helyszíni gépeken üzemeltetett SQL-kiszolgálóknak (az összes támogatott verziónak).
    - [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Helyszíni SQL Server-kiszolgálók:
        - [Azure Arc-kompatibilis SQL Server (előzetes verzió)](/sql/sql-server/azure-arc/overview)
        - [Windows rendszerű gépeken futó SQL Server Azure arc nélkül](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Milyen előnyökkel jár az Azure Defender for SQL?

Ez a két csomag a lehetséges adatbázis-sebezhetőségek azonosítására és enyhítésére, valamint az adatbázisok fenyegetését jelző rendellenes tevékenységek észlelésére szolgáló funkciókat tartalmaz.

- [Sebezhetőségi felmérés](../azure-sql/database/sql-vulnerability-assessment.md) – a szkennelési szolgáltatás felderíti, nyomon követheti és segítheti a lehetséges adatbázis-rések szervizelését. Az értékelési vizsgálatok áttekintést nyújtanak az SQL-gépek biztonsági állapotáról, valamint a biztonsági eredmények részleteiről.

- Komplex [veszélyforrások elleni védelem](../azure-sql/database/threat-detection-overview.md) – az észlelési szolgáltatás, amely folyamatosan FIGYELI az SQL-kiszolgálókat olyan fenyegetésekkel szemben, mint például az SQL-injektálás, a találgatásos támadás és a jogosultságokkal való visszaélés. Ez a szolgáltatás lehetővé teszi, hogy a rendszer részletes biztonsági riasztásokat biztosítson Azure Security Center a gyanús tevékenység részleteit, útmutatást nyújt a fenyegetések enyhítéséhez, valamint az Azure Sentinel használatával folytatott vizsgálatok folytatásának lehetőségeiről. 
    > [!TIP]
    > Tekintse meg az SQL Server rendszerhez készült biztonsági riasztások listáját [a riasztások hivatkozása lapon](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Milyen típusú riasztások biztosítják az Azure Defender for SQL szolgáltatást?

A fenyegetésekkel kapcsolatos bővített biztonsági riasztások akkor aktiválódnak, ha:

- **Lehetséges SQL-injektálási támadások** – az észlelt biztonsági réseket is beleértve, ha az alkalmazások hibás SQL-utasítást hoznak elő az adatbázisban
- **Rendellenes adatbázis-hozzáférés és lekérdezési minták** – például rendellenesen nagy számú sikertelen bejelentkezési kísérlet különböző hitelesítő adatokkal (találgatásos kísérlet)
- **Gyanús adatbázis-tevékenység** – például egy legitim felhasználó, aki egy olyan megsértett számítógépről fér hozzá a SQL Serverhoz, amely kriptográfiai C&c kiszolgálóval kommunikált

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat.



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az SQL-hez készült Azure Defendert.

> [!div class="nextstepaction"]
> [Az Azure Defender szolgáltatással kapcsolatos biztonsági rések ellenőrzése az SQL-kiszolgálókon](defender-for-sql-usage.md)