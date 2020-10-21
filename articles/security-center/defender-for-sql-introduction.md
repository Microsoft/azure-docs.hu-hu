---
title: Azure Defender for SQL – az előnyök és funkciók
description: Ismerje meg az Azure Defender for SQL előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 7301f6bbb33c3eedff2e8c41edeaacb3df66d4c2
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342025"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Az SQL-hez készült Azure Defender bemutatása

Az SQL-hez készült Azure Defender két Azure Defender-csomagot tartalmaz, amelyek kiterjesztik Azure Security Center [adatbiztonsági csomagját](../azure-sql/database/azure-defender-for-sql.md) , hogy az adatbázisok és az adataik bárhonnan biztonságosak legyenek. 

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|**Azure Defender az Azure SQL Database-kiszolgálókhoz** – általánosan elérhető (GA)<br>**Azure Defender a gépeken futó SQL-kiszolgálókon** – előzetes verzió|
|Árképzési|Az **Azure Defender for SQL** -t alkotó két csomag számlázása [a díjszabási oldalon](security-center-pricing.md) látható módon történik|
|Védett SQL-verziók:|Azure SQL Database <br>Felügyelt Azure SQL-példány<br>Azure Synapse Analytics (korábban SQL DW)<br>SQL Server (az összes támogatott verzió)|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Mire használható az Azure Defender az SQL-védelemhez?

**Az Azure Defender for SQL** két különálló Azure Defender-csomagból áll:

- **Az Azure Defender for Azure SQL Database-kiszolgálók a** következőket védik:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Felügyelt Azure SQL-példány](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- A **gépeken futó Azure Defender (előzetes verzió)** kiterjeszti az Azure-natív SQL-kiszolgálók védelmét, így teljes mértékben támogatja a hibrid környezeteket, és védelmet biztosít az Azure-ban, más felhőalapú környezetekben és akár helyszíni gépeken üzemeltetett SQL-kiszolgálóknak (az összes támogatott verziónak).


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Milyen előnyökkel jár az Azure Defender for SQL?

Ez a két csomag a lehetséges adatbázis-sebezhetőségek azonosítására és enyhítésére, valamint az adatbázisok fenyegetését jelző rendellenes tevékenységek észlelésére szolgáló funkciókat tartalmaz.

- [Sebezhetőségi felmérés](../azure-sql/database/sql-vulnerability-assessment.md) – a szkennelési szolgáltatás felderíti, nyomon követheti és segítheti a lehetséges adatbázis-rések szervizelését. Az értékelési vizsgálatok áttekintést nyújtanak az SQL-gépek biztonsági állapotáról, valamint a biztonsági eredmények részleteiről.

- Komplex [veszélyforrások elleni védelem](../azure-sql/database/threat-detection-overview.md) – az észlelési szolgáltatás, amely folyamatosan FIGYELI az SQL-kiszolgálókat olyan fenyegetésekkel szemben, mint például az SQL-injektálás, a találgatásos támadás és a jogosultságokkal való visszaélés. Ez a szolgáltatás lehetővé teszi, hogy a rendszer részletes biztonsági riasztásokat biztosítson Azure Security Center a gyanús tevékenység részleteit, útmutatást nyújt a fenyegetések enyhítéséhez, valamint az Azure Sentinel használatával folytatott vizsgálatok folytatásának lehetőségeiről.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Milyen típusú riasztások biztosítják az Azure Defender for SQL szolgáltatást?

A biztonsági riasztások akkor aktiválódnak, ha:

- **Lehetséges SQL-injektálási támadások** – az észlelt biztonsági réseket is beleértve, ha az alkalmazások hibás SQL-utasítást hoznak elő az adatbázisban
- **Rendellenes adatbázis-hozzáférés és lekérdezési minták** – például rendellenesen nagy számú sikertelen bejelentkezési kísérlet különböző hitelesítő adatokkal (találgatásos kísérlet)
- **Gyanús adatbázis-tevékenység** – például egy SQL-importálási és exportálási művelet exportálási célhelyének változása

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat.



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az SQL-hez készült Azure Defendert.

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- [Az Azure Defender engedélyezése a gépeken futó SQL-kiszolgálókon](defender-for-sql-usage.md)
- [Az Azure Defender engedélyezése az SQL Database-kiszolgálókhoz](../azure-sql/database/azure-defender-for-sql.md)
- [Az SQL-hez készült Azure Defender-riasztások listája](alerts-reference.md#alerts-sql-db-and-warehouse)