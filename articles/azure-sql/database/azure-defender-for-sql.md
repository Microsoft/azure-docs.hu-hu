---
title: Azure Defender SQL-hez
description: Ismerje meg az adatbázis-biztonsági rések kezelésének funkcióit, valamint azokat a rendellenes tevékenységeket, amelyek fenyegetést jelenthetnek az adatbázisra Azure SQL Database, Azure SQL felügyelt példányban vagy Azure Szinapszisban.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508811"
---
# <a name="azure-defender-for-sql"></a>Azure Defender SQL-hez
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Az SQL-hez készült Azure Defender egységes csomag a fejlett SQL-biztonsági funkciókhoz. Az Azure Defender a Azure SQL Database, az Azure SQL felügyelt példányaihoz és az Azure szinapszis Analyticshez érhető el. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek feltárására és kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Milyen előnyökkel jár az Azure Defender for SQL?

Az Azure Defender fejlett SQL biztonsági képességeket biztosít, beleértve az SQL sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és segíteni a lehetséges adatbázis-sebezhetőségeket. A szolgáltatás láthatóságot biztosít a biztonsági állapotában, valamint a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges műveleteket is tartalmaz.
- Az [Advanced Threat Protection](threat-detection-overview.md) észleli az adatbázisai hozzáférésére és az adatbázisai biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. Folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és azonnali biztonsági riasztásokat biztosít a potenciális sebezhetőségekről, az Azure SQL-injektálási támadásokról és a rendellenes adatbázis-hozzáférési mintákról. Az Advanced Threat Protection által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Az SQL-hez készült Azure Defendert csak egyszer kell engedélyeznie, ekkor ugyanis az összes benne foglalt funkciót is engedélyezi. Egyetlen kattintással engedélyezheti az Azure Defendert az Azure [-ban vagy](logical-servers.md) az SQL-ben felügyelt példányon lévő összes adatbázishoz. Az Azure Defender-beállítások engedélyezéséhez vagy kezeléséhez az [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) szerepkörhöz vagy az adatbázis vagy a kiszolgáló rendszergazdai szerepköreihez kell tartoznia.

Az Azure Defender for SQL díjszabásával kapcsolatos további információkért tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése

Az Azure Defender a [Azure Portalon](https://portal.azure.com)keresztül érhető el. Engedélyezze az Azure Defender számára, hogy a kiszolgáló vagy a felügyelt példány **biztonsági** fejléce alatt **Security Center** navigáljon.

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte az Azure Defender használatát ugyanazon erőforráscsoport és régió egy másik kiszolgálóján, akkor a rendszer a meglévő Storage-fiókot használja.
>
> Az Azure Defender díja Azure Security Center standard szintű díjszabással van összhangban, ahol a csomópont a teljes kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie a kiszolgálón található összes adatbázis vagy a felügyelt példány Azure Defender szolgáltatással való védelméhez. Először kipróbálhatja az Azure Defendert az ingyenes próbaverzióval.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Az Azure Defender for SQL engedélyezése az Azure SQL-adatbázisokon belül":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Biztonsági rések nyomon követése és a veszélyforrások kivizsgálásának vizsgálata

Kattintson a **sebezhetőségi felmérési** kártyára a sebezhetőségi vizsgálatok és jelentések megtekintéséhez és kezeléséhez, valamint a biztonsági Terme nyomon követéséhez. Ha a rendszer megkapta a biztonsági riasztásokat, kattintson a komplex **veszélyforrások elleni védelem** kártyára a riasztások részleteinek megtekintéséhez, valamint az Azure-előfizetésben lévő összes riasztásról a Azure Security Center biztonsági riasztások lapon tekintheti meg a konszolidált jelentést.

## <a name="manage-azure-defender-settings"></a>Az Azure Defender beállításainak kezelése

Az Azure Defender beállításainak megtekintése és kezelése:

1. A kiszolgáló vagy a felügyelt példány **biztonsági** területén válassza a **Security Center** lehetőséget.

    Ezen az oldalon megtekintheti az SQL-hez készült Azure Defender állapotát:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Az Azure Defender for SQL Azure SQL Database-adatbázisokon belüli állapotának ellenőrzése":::

1. Ha az Azure Defender for SQL engedélyezve van, az előző ábrán látható módon megjelenik egy **konfigurálási** hivatkozás. Az Azure Defender for SQL beállításainak szerkesztéséhez válassza a **Konfigurálás** lehetőséget.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="biztonsági kiszolgáló beállításai":::

1. Végezze el a szükséges módosításokat, majd válassza a **Mentés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md)
- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-configure.md)
- További információ a [Azure Security Center](../../security-center/security-center-introduction.md)