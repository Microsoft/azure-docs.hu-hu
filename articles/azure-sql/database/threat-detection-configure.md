---
title: Az Azure Komplex veszélyforrások elleni védelem konfigurálása
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyeken az adatbázis lehetséges biztonsági fenyegetéseket jelez Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 1425003c718ca52c0bea712e9d25cd3e4c035cf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453955"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Az összetett veszélyforrások elleni védelem konfigurálása Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database komplex [veszélyforrások elleni védelme](threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL-injektálásokat**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** . További részletek a komplex [veszélyforrások elleni védelmi riasztásokban](threat-detection-overview.md#alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](threat-detection-overview.md#explore-detection-of-a-suspicious-event) vagy a [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](threat-detection-overview.md) az [Azure Defender for SQL](azure-defender-for-sql.md) -ajánlat része, amely a fejlett SQL biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi Azure Defender for SQL portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Az összetett veszélyforrások elleni védelem beállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a védetté tenni kívánt kiszolgáló konfigurációs lapjára. A biztonsági beállítások lapon válassza a **Security Center** lehetőséget.
3. Az **Azure Defender for SQL** konfigurálása lapon:

   - Engedélyezze az **Azure Defender for SQL** szolgáltatást a kiszolgálón.
   - Az **összetett veszélyforrások elleni védelem beállításainál** adja meg az e-mailek azon listáját, amely a **riasztások küldése** szövegmezőbe a rendellenes adatbázis-tevékenységek észlelése után biztonsági riasztásokat kap.
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="komplex veszélyforrások elleni védelem beállítása":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Az Advanced Threat Protection beállítása PowerShell használatával

A parancsfájlokra példa: a [naplózás és az összetett veszélyforrások elleni védelem konfigurálása a PowerShell használatával](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Következő lépések

- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-overview.md).
- További információ a komplex [veszélyforrások elleni védelemről az SQL felügyelt példányában](../managed-instance/threat-detection-configure.md).  
- További információ [Az Azure Defender for SQL szolgáltatásról](azure-defender-for-sql.md).
- További információ a [naplózásról](../../azure-sql/database/auditing-overview.md)
- További információ az [Azure Security Centerről](../../security-center/security-center-introduction.md)
- A díjszabással kapcsolatos további információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .