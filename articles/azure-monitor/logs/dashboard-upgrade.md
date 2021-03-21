---
title: Log Analytics irányítópult-vizualizációk frissítése
description: Ismerje meg, hogyan frissítheti Log Analytics irányítópult-vizualizációkat olyan lekérdezésekkel, amelyek hatékony információkat biztosítanak.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030936"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Log Analytics irányítópult-vizualizációk frissítése

Február 2020-án bemutatottunk egy továbbfejlesztett vizualizációs technológiát. A lekérdezési eredmények megjelenítésének, valamint a gyors és hatékony eredmények elérésének javítása és továbbfejlesztése. 

Erről a frissítésről ebben az [Azure-frissítésben](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)olvashat bővebben. 

Ez az új vizualizációs technológia a lekérdezési eredményhalmaz új és továbbfejlesztett élményét kínálja. 

## <a name="dashboards-in-azure"></a>Irányítópultok az Azure-ban

Az Azure-irányítópultok lehetővé teszik a teljes Azure Surface-terület állapotának megjelenítését. Ezek úgy lettek kialakítva, hogy egyetlen panelt biztosítanak az Azure Estate-állapothoz, és számos különböző parancsikont engedélyezzen a gyakori műveletekhez. 

További információ: Azure- [irányítópultok](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Log Analytics irányítópult-részek frissítése

Az új vizualizációs technológia a régi implementációval kapcsolatos gyakori problémákkal foglalkozik, és új képességeket vezet be a Log Analytics részek kitűzéséhez: 

- **Azonos elérhető típusok** – az log Analytics elérhető vizualizációk típusai az irányítópultok rögzített részeiként érhetők el.

- **Konzisztens megjelenés és működés** – a vizualizációk megjelenésének és működésének meglátása a rögzített részeknél már majdnem azonos a log Analytics. A különbségek olyan optimalizációk miatt vannak, amelyekben a vizualizáció adattartalmában enyhe különbségek szükségesek. Tekintse meg a jelen dokumentum részét képező szempontokat, hogy jobban betekintést kapjon ezekbe a különbségekbe.

- **Elemleírások és címkék – az** új rögzített log Analytics vizualizációk támogatják az elemleírásokat. A Pie és a perec diagramok mostantól támogatják a címkéket.

- **Interaktív jelmagyarázatok** – a vizualizációs jelmagyarázatra kattintva a dimenziók hozzáadását és eltávolítását a log Analyticsban látható módon végezheti el.

## <a name="stage-1---opt-in-upgrade-message"></a>1. fázis – a verziófrissítési üzenet

Ha egy Log Analytics rögzített rész frissíthető, az irányítópultok Log Analytics rögzített részein megjelenik *egy új* beléptetési értesítés, amely lehetővé teszi a felhasználóknak a vizualizációk frissítését. Ha az új vizualizációk segítségével szeretné frissíteni a kijelölt vizualizációkat az irányítópulton.

 
![Oldalsáv](media/dashboard-upgrade/update-message-1.png)
 
![A csempék vizualizációjának frissítését bemutató képernyőkép.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Miután közzétette az irányítópultot, a frissítés visszafordíthatatlan. Azonban a módosítások elvesznek, ha az irányítópultról az újbóli közzététel nélkül navigál.  

Ha rákattintott, a vizualizáció frissülni fog az új technológiára. A vizualizációban előforduló finom különbségek a megjelenés és a működésük Log Analytics.

A vizualizációk frissítése után újra közzé kell tennie az irányítópultot, hogy a módosítás életbe lépjen.

![Képernyőkép, amely a frissített vizualizációkat jeleníti meg.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>2. fázis – az összes irányítópult áttelepítése

A kezdeti bekapcsolási időszak lejártát követően a Log Analytics csapat frissíti a rendszer összes irányítópultját. Az összes Azure-irányítópult összehangolása lehetővé teszi, hogy a csapat további vizualizációkat és tapasztalatokat vezessen be a táblán.

## <a name="considerations"></a>Megfontolandó szempontok

Az irányítópultra rögzített vizualizációk Log Analytics az optimális felhasználói élmény érdekében. Tekintse át a következő tervezési szempontokat a vizualizációk irányítópulton való rögzítéséhez.

### <a name="query-time-scope---30-day-limit"></a>Lekérdezési idő hatóköre – 30 napos korlát

Mivel az irányítópultok több lekérdezésből származó vizualizációkat tartalmazhatnak, egy adott rögzített lekérdezés időtartomány-korlátja 30 nap. Egyetlen lekérdezés csak kisebb vagy egyenlő 30 napos időtartamon futhat. Ez a korlátozás egy ésszerű irányítópult-betöltési idő biztosítása.

### <a name="query-data-values---25-values-and-other-grouping"></a>Adatértékek lekérdezése – 25 érték és egyéb csoportosítás

Az irányítópultok vizuálisan tömörek és összetettebbek lehetnek. Az irányítópultok megtekintésekor a kognitív terhelés csökkentése érdekében optimalizálja a vizualizációkat úgy, hogy a megjelenítést 25 különböző adattípusra korlátozza. Ha több mint 25, Log Analytics optimalizálja az adatátvitelt. Az adatok mindegyike különállóként jeleníti meg a legtöbb adattal rendelkező 25 típust, majd egy másik értékre csoportosítja a többi értéket. A következő diagram egy ilyen esetet mutat be.  

![Képernyőkép, amely a 25 különböző adattípusú irányítópultot jeleníti meg.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Irányítópult-frissítés betöltéskor

Az irányítópultok a terhelés után frissülnek. Az irányítópulton rögzített Log Analytics-vizualizációkkal kapcsolatos összes lekérdezés végrehajtása történik, és az irányítópult a betöltés után frissül. Ha az irányítópult lapja nyitva marad, az irányítópulton lévő összes 60 percenként frissül.

## <a name="next-steps"></a>Következő lépések

[Irányítópultok létrehozása és megosztása Log Analytics](../visualize/tutorial-logs-dashboards.md)