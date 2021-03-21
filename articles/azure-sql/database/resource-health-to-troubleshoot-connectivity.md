---
title: Az adatbázis állapotának figyelése Azure Resource Health
description: A Azure Resource Health segítségével figyelheti a Azure SQL Database és az Azure SQL felügyelt példányának állapotát, segít a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az SQL-erőforrásokra.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 02/26/2019
ms.openlocfilehash: 1a74a2f44ac9b03e39e9fea48a3428b82111a48c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471997"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>A Resource Health használata a Azure SQL Database és az Azure SQL felügyelt példányaihoz való kapcsolódással kapcsolatos problémák megoldásához
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példányának [Resource Health](../../service-health/resource-health-overview.md#get-started) segítségével diagnosztizálhatja és kérheti a támogatást, ha egy Azure-probléma hatással van az SQL-erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health technikai támogatást nyújt, ha segítségre van szüksége az Azure-szolgáltatással kapcsolatos problémák megoldásához.

![Áttekintés](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Állapot-ellenőrzések

Resource Health meghatározza az SQL-erőforrás állapotát az erőforrásba való bejelentkezés sikerességének és hibájának vizsgálatával. A SQL Database erőforrás Resource Health jelenleg csak rendszerhiba miatt ellenőrzi a bejelentkezési hibákat, és nem a felhasználói hibát. A Resource Health állapota 1 és 2 percenként frissül.

## <a name="health-states"></a>Állapotok

### <a name="available"></a>Elérhető

Az **elérhető** állapot azt jelenti, hogy a Resource Health nem észlelte a bejelentkezési hibákat, mert az SQL-erőforráson rendszerhiba történt.

![Elérhető](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítményű

A **Csökkentett teljesítmény** állapot azt jelenti, hogy a Resource Health főleg sikeres bejelentkezéseket észlelt, de néhány sikertelent is. Ezek valószínűleg átmeneti bejelentkezési hibák. Az átmeneti bejelentkezési hibák okozta kapcsolódási problémák csökkentése érdekében hajtsa végre az [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítményű](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a Resource Health konzisztens bejelentkezési hibákat észlelt az SQL-erőforráshoz. Ha az erőforrás hosszabb ideig ebben az állapotban marad, forduljon az ügyfélszolgálathoz.

![Nem érhető el](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, hogy Resource Health több mint 10 percig nem kapott információt az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jele, a hibaelhárítási folyamat fontos adatpontja. Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva elérhetővé válik. Ha problémákat tapasztal az erőforrással kapcsolatban, az ismeretlen állapot azt sugallhatja, hogy a platformon lévő esemény hatással van az erőforrásra.

![Ismeretlen](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Korábbi információk

A Resource Health Health History (állapot előzményei) szakaszában akár 14 napig is elérheti az állapotot. A szakasz a Resource Health által jelentett állásidőt is tartalmazni fogja (ha elérhető). Az Azure jelenleg az adatbázis-erőforrás leállását jeleníti meg kétperces részletességgel. A tényleges állásidő valószínűleg egy percnél kevesebb. Az átlag 8 másodperc.

### <a name="downtime-reasons"></a>Leállás okai

Ha az adatbázis leállást észlel, elemzést végez az ok megállapításához. Ha elérhető, az állásidő oka a Resource Health állapotának előzményei című szakaszban található. A leállás okai általában 45 percen belül, egy esemény után jelennek meg.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeres időközönként tervezett karbantartást végez – a hardver-vagy szoftver-összetevők frissítése az adatközpontban. Amíg az adatbázis karbantartást végez, az Azure SQL megszakíthat néhány meglévő kapcsolatot, és megtagadhatja az újakat. A tervezett karbantartás során tapasztalt bejelentkezési hibák általában átmenetiek, és az [újrapróbálkozási logika](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkenteni a hatást. Ha továbbra is bejelentkezési hibákat tapasztal, forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Az újrakonfigurálások átmeneti feltételeknek számítanak, és időről időre várhatóak. Ezeket az eseményeket terheléselosztás vagy szoftver/hardver meghibásodása esetén aktiválhatja. A felhőalapú adatbázishoz csatlakozó összes ügyfél-előállító alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia, mivel ez segít az ilyen helyzetek enyhítésében, és általánosságban a hibákat általában a végfelhasználók számára transzparensvé teszi.

## <a name="next-steps"></a>Következő lépések

- További információ az [átmeneti hibákra vonatkozó újrapróbálkozási logikáról](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Az [SQL-kapcsolatok hibáinak elhárítása, diagnosztizálása és letiltása](troubleshoot-common-connectivity-issues.md).
- További információ a [Resource Health riasztások konfigurálásáról](../../service-health/resource-health-alert-arm-template-guide.md).
- A [Resource Health](../../application-gateway/resource-health-overview.md)áttekintése.
- Tekintse át [Resource Health gyakori kérdések](../../service-health/resource-health-faq.md)című szakaszt.
