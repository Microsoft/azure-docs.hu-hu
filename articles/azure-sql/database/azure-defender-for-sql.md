---
title: Azure Defender SQL-hez
description: Ismerje meg az adatbázis-biztonsági rések kezelésének funkcióit, valamint azokat a rendellenes tevékenységeket, amelyek fenyegetést jelenthetnek az adatbázisra Azure SQL Database, Azure SQL felügyelt példányban vagy Azure Szinapszisban.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452312"
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
Az Azure Defender-csomagok több módon is engedélyezhetők. Az előfizetés szintjén (**ajánlott**) engedélyezheti az alábbiakat:

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programozott módon az REST API, az Azure CLI, a PowerShell vagy a Azure Policy használatával](#enable-azure-defender-plans-programatically)

Azt is megteheti, hogy az erőforrás szintjén engedélyezte az [Azure Defender engedélyezése Azure SQL Database számára](#enable-azure-defender-for-azure-sql-database-at-the-resource-level) című témakörben leírtak szerint.

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Az Azure Defender Azure SQL Database engedélyezése az előfizetés szintjén Azure Security Center
Az Azure Defender Azure SQL Databaseának engedélyezése az előfizetés szintjén Azure Security Centeron belül:

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Security Center**.
1. A Security Center menüjében válassza a **díjszabás és beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Módosítsa a terv beállítást a **be** értékre.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Az Azure Defender Azure SQL Database engedélyezése az előfizetés szintjén.":::

1. Kattintson a **Mentés** gombra.


### <a name="enable-azure-defender-plans-programatically"></a>Azure Defender-csomagok programozott módon engedélyezése 

Az Azure rugalmassága számos programozott módszer használatát teszi lehetővé az Azure Defender-csomagok engedélyezéséhez. 

A következő eszközök bármelyikével engedélyezheti az Azure Defender használatát az előfizetéséhez: 

| Metódus       | Utasítások                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API     | [Díjszabási API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az Security díjszabása](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Csomagok díjszabása](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Az Azure Defender engedélyezése a Azure SQL Database erőforrás szintjén

Javasoljuk, hogy az előfizetési szinten engedélyezze az Azure Defender-csomagokat, és ez segít a nem védett erőforrások létrehozásában. Ha azonban az Azure Defender kiszolgáló szintjén való engedélyezésének szervezeti oka van, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a kiszolgálót vagy a felügyelt példányt.
1. A **Biztonság** fejléc alatt válassza a **Security Center** lehetőséget.
1. Válassza **Az Azure Defender engedélyezése az SQL-hez** lehetőséget.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Az Azure Defender for SQL engedélyezése az Azure SQL-adatbázisokon belül.":::

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte az Azure Defender használatát ugyanazon erőforráscsoport és régió egy másik kiszolgálóján, akkor a rendszer a meglévő Storage-fiókot használja.
>
> Az Azure Defender díja Azure Security Center standard szintű díjszabással van összhangban, ahol a csomópont a teljes kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie a kiszolgálón található összes adatbázis vagy a felügyelt példány Azure Defender szolgáltatással való védelméhez. Először kipróbálhatja az Azure Defendert az ingyenes próbaverzióval.


## <a name="manage-azure-defender-settings"></a>Az Azure Defender beállításainak kezelése

Az Azure Defender beállításainak megtekintése és kezelése:

1. A kiszolgáló vagy a felügyelt példány **biztonsági** területén válassza a **Security Center** lehetőséget.

    Ezen az oldalon megtekintheti az SQL-hez készült Azure Defender állapotát:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Az Azure Defender for SQL Azure SQL-adatbázisokon belüli állapotának ellenőrzése.":::

1. Ha az Azure Defender for SQL engedélyezve van, az előző ábrán látható módon megjelenik egy **konfigurálási** hivatkozás. Az Azure Defender for SQL beállításainak szerkesztéséhez válassza a **Konfigurálás** lehetőséget.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Az Azure Defender for SQL beállításai.":::

1. Végezze el a szükséges módosításokat, majd válassza a **Mentés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md)
- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-configure.md)
- További információ a [Azure Security Center](../../security-center/security-center-introduction.md)