---
title: Adatbázisok kezelése Azure Automation
description: Ismerje meg, hogyan használható a Azure Automation szolgáltatás a Azure SQL Database nagy léptékű kezeléséhez.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327578"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Azure SQL Database-adatbázisok kezelése Azure Automation használatával

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként használható a Azure SQL Database adatbázisok kezelésének egyszerűbbé tételéhez.

## <a name="about-azure-automation"></a>Tudnivalók Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a hosszan futó, a manuális, a hibákra hajlamos és a gyakran ismétlődő feladatok használatával automatizálható a szervezet megbízhatóságának, hatékonyságának és időpontjának növelése. További információ az első lépésekről: [Azure Automation intro](../../automation/automation-intro.md)

A Azure Automation magas megbízhatóságot és magas rendelkezésre állást biztosító munkafolyamat-végrehajtó motort biztosít, amely a szervezet növekedésének megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse a működési terhelést, és szabadítson fel informatikai/DevOps-személyzetet, hogy az üzleti értékeket felhasználva a Felhőbeli felügyeleti feladatok automatikus futtatásával Azure Automation.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Hogyan segítheti a Azure Automation az adatbázisok kezelését

A Azure Automation segítségével a [Azure PowerShell-eszközökön](/powershell/azure/)elérhető PowerShell- [parancsmagok](/powershell/module/servicemanagement/azure.service/#sql) használatával kezelheti Azure SQL Database adatbázisait. Azure Automation ezen Azure SQL Database PowerShell-parancsmagok elérhetők a dobozból, így az összes SQL Database felügyeleti feladatot elvégezheti a szolgáltatáson belül. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső rendszerek között.

A Azure Automation az SQL-kiszolgálókkal való közvetlen kommunikációt is lehetővé teszi az SQL-parancsok a PowerShell használatával történő kiadásával.

[Azure Automation](../../automation/automation-runbook-gallery.md) runbook és modul-galériák a Microsoft és a Közösség különböző runbookok kínálják, amelyeket importálhat a Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a Azure Automation alapjait, és azt, hogy miként használható a Azure SQL Database kezelésére, kövesse az alábbi hivatkozásokat, amelyekkel többet is megtudhat a Azure Automationről.

- [Az Azure Automation áttekintése](../../automation/automation-intro.md)
- [Az első runbookom](../../automation/learn/automation-tutorial-runbook-graphical.md)
