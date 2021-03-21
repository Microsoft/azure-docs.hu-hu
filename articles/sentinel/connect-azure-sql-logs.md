---
title: Azure SQL Database-diagnosztika és-naplózási naplók összekötése az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan kapcsolódhat az Azure SQL Database diagnosztikai naplóihoz és a biztonsági naplózási naplókhoz az Azure Sentinel szolgáltatáshoz.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807733"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Azure SQL Database-diagnosztika és-naplózási naplók összekötése

Az Azure SQL egy teljes körűen felügyelt, szolgáltatásként szolgáló platform, amely az adatbázis-kezelési funkciókat, például a verziófrissítést, a javítást, a biztonsági mentést és a figyelést kezeli, felhasználói beavatkozás nélkül. 

Az Azure SQL Database-összekötő lehetővé teszi az adatbázisok naplózási és diagnosztikai naplóinak az Azure Sentinelbe való továbbítását, így folyamatosan figyelheti a tevékenységeket az összes példányban.

- A diagnosztikai naplók összekapcsolása lehetővé teszi, hogy különböző adattípusú adatbázis-diagnosztikai naplókat küldjön az Azure Sentinel-munkaterületre.

- A naplózási naplók összekapcsolása lehetővé teszi, hogy kiszolgáló szintjén továbbítsa a biztonsági naplózási naplókat az összes Azure SQL-adatbázisból.

További információ az [Azure SQL Database-adatbázisok figyeléséről](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- A naplózási naplók összekapcsolásához olvasási és írási engedéllyel kell rendelkeznie az Azure SQL Server naplózási beállításaihoz.

## <a name="connect-to-azure-sql-database"></a>Kapcsolódás az Azure SQL Database szolgáltatáshoz
    
1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza a **Azure SQL Database** lehetőséget az adatösszekötők katalógusában, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot**  .

1. Az összekötő lap **konfiguráció** szakaszában figyelje meg, hogy a naplók két kategóriája csatlakoztatható.

### <a name="connect-diagnostics-logs"></a>Diagnosztikai naplók összekötése

1. A **diagnosztikai naplók** területen bontsa ki **manuálisan az Azure SQL-adatbázisok diagnosztikai naplóinak engedélyezése** elemet.

1. Az **Azure SQL** -erőforrások panel megnyitásához válassza az **Azure SQL >megnyitása** hivatkozást.

1. **(Nem kötelező)** Ha egyszerűen szeretné megkeresni az adatbázis-erőforrást, válassza a felső szűrő sáv **szűrő hozzáadása** elemét.
    1. A **szűrő** legördülő listában válassza az **Erőforrás típusa** lehetőséget.
    1. Az **érték** legördülő listában törölje az **összes kijelölése** lehetőséget, majd válassza az **SQL Database** lehetőséget.
    1. Kattintson az **Alkalmaz** gombra.
    
1. Válassza ki azt az adatbázis-erőforrást, amelynek diagnosztikai naplóit el szeretné küldeni az Azure Sentinel szolgáltatásnak.

    > [!NOTE]
    > Minden olyan adatbázis-erőforráshoz, amelynek naplóit össze kívánja gyűjteni, meg kell ismételnie ezt a lépést a lépéstől kezdve.

1. A kiválasztott adatbázis erőforrás lapjának navigációs menüjének **figyelés** területén válassza a **diagnosztikai beállítások** elemet.

    1. Válassza a **+ diagnosztikai beállítások hozzáadása** hivatkozást a tábla alján.

    1. A **diagnosztikai beállítások** képernyőn adjon meg egy nevet a  **diagnosztikai beállítások neve** mezőben.
    
    1. A **cél részletei** oszlopban jelölje be a **Küldés log Analytics munkaterületre** jelölőnégyzetet. Alatta két új mező jelenik meg. Válassza ki a megfelelő **előfizetést** és **log Analytics munkaterületet** (ahol az Azure Sentinel található).

    1. A **Kategória részletei** oszlopba írja be a befoglalni kívánt napló és metrika típusának jelölőnégyzeteit. Javasoljuk, hogy az összes rendelkezésre álló típust a **napló** és a **metrika** területen válassza ki.

    1. Válassza a **Mentés** lehetőséget a képernyő tetején.

- Azt is megteheti, hogy a megadott **PowerShell-parancsfájlt** használja a diagnosztikai naplók összekapcsolásához.
    1. A **diagnosztikai naplók** területen bontsa ki **az engedélyezés PowerShell-parancsfájl használatával** elemet.

    1. Másolja a kód blokkot, és illessze be a PowerShellbe.

### <a name="connect-audit-logs"></a>Naplók összekötése

1. A **naplózási naplók (előzetes verzió)** területen bontsa ki a **naplózási naplók engedélyezése az összes Azure SQL-adatbázisban (a kiszolgáló szintjén)** elemet.

1. Válassza az **Azure sql >megnyitása** hivatkozást az SQL- **kiszolgálók** erőforrás panel megnyitásához.

1. Válassza ki azt az SQL Servert, amelynek naplózási naplóit el szeretné küldeni az Azure Sentinel szolgáltatásnak.

    > [!NOTE]
    > Minden olyan kiszolgálói erőforrás esetében, amelynek a naplóit össze kívánja gyűjteni, meg kell ismételnie ezt a lépést a lépéstől kezdve.

1. A kiválasztott kiszolgáló erőforrás lapján kattintson a **Biztonság** elemre a navigációs menüben, majd válassza a **naplózás** lehetőséget.

    1. Helyezze át az **Azure SQL-naplózás engedélyezése** bekapcsolás **a következőre:.**

    1. A **napló célhelye** területen válassza a **log Analytics (előzetes verzió)** lehetőséget.
    
    1. A megjelenő munkaterületek listájából válassza ki a munkaterületet (ahol az Azure Sentinel található).

    1. Válassza a **Mentés** lehetőséget a képernyő tetején.

- Azt is megteheti, hogy a megadott **PowerShell-parancsfájlt** használja a diagnosztikai naplók összekapcsolásához.
    1. A **naplózási naplók** területen bontsa ki **az engedélyezés PowerShell-parancsfájl használatával** elemet.

    1. Másolja a kód blokkot, és illessze be a PowerShellbe.


> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure SQL Database Diagnostics és a naplózási naplók az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).