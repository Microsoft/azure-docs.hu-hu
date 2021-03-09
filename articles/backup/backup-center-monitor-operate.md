---
title: Biztonsági mentések monitorozása és üzemeltetése a Backup Centerrel
description: Ez a cikk bemutatja, hogyan figyelheti és működtetheti a biztonsági másolatok méretét a Backup Center használatával
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506125"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Biztonsági másolatok figyelése és üzemeltetése a Backup Center használatával

Biztonsági mentési rendszergazdaként a Backup centert egyetlen Ablaktáblaként használhatja a feladatok és a biztonsági másolatok napi rendszerességű nyomon követéséhez. A Backup Center használatával is elvégezheti a rendszeres műveleteket, például az igény szerinti biztonsági mentési kérelmekre való válaszadást, a biztonsági másolatok visszaállítását, a biztonsági mentési házirendek létrehozását és így tovább.

## <a name="supported-scenarios"></a>Támogatott esetek

* A Backup Center jelenleg támogatott az Azure-beli virtuális gépek biztonsági mentése, az SQL az Azure VM Backup szolgáltatásban, SAP HANA az Azure virtuális gép biztonsági mentésében, Azure Files Backup, Azure Blob Backup, Azure Managed Disks Backup és Azure Database for PostgreSQL Server Backup.
* A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="backup-instances"></a>Biztonsági mentési példányok

A Backup Center lehetővé teszi a biztonsági mentési példányok egyszerű keresését és felderíthetővé tételeit a Backup-birtokon.

A Backup Center **biztonsági mentési példányok** lapján megtekintheti az összes olyan biztonsági mentési példány adatait, amelyhez hozzáfér.

 A következő információkat tekintheti meg az egyes biztonsági mentési példányokról:

* Adatforrás-előfizetés
* Adatforrás-erőforráscsoport
* Legutóbbi helyreállítási pont
* A biztonsági mentési példányhoz társított tár
* Védelmi állapot

 A biztonsági mentési példányok listáját a következő paramétereken is szűrheti:

* Adatforrás-előfizetés
* Adatforrás-erőforráscsoport
* Adatforrás helye
* Adatforrás típusa
* Tároló
* Védelmi állapot
* DataSource-Címkék

A rács egyik elemére a jobb gombbal kattintva műveleteket hajthat végre az adott biztonsági mentési példányon, például az erőforráshoz való navigálás, az igény szerinti biztonsági mentések és visszaállítások, illetve a biztonsági mentés leállítása.

![Biztonsági mentési központ – példányok](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Biztonsági mentési feladatok

A Backup Center lehetővé teszi, hogy részletes információkat tekintse meg a Backup-hagyatékban létrehozott összes feladatról, és tegye meg a megfelelő lépéseket a sikertelen feladatok végrehajtásához.

A Backup Center **biztonsági mentési feladatok** menüpontjának kiválasztásával megtekintheti az összes feladatot. Az egyes feladatok a következő információkat tartalmazzák:

* A feladatokhoz társított biztonsági mentési példány
* Adatforrás-előfizetés
* Adatforrás-erőforráscsoport
* Adatforrás helye
* Feladatok művelete
* Feladatok állapota
* A feladat kezdési időpontja
* Feladatok időtartama

A rácsban lévő elemek kiválasztásával további részleteket tekinthet meg a megadott feladatról. A jobb gombbal egy elemre kattintva megkezdheti az erőforrást a szükséges műveletek elvégzéséhez.

![Biztonsági mentési központ – feladatok](./media/backup-center-monitor-operate/backup-center-jobs.png)

A **biztonsági mentési feladatok** lapon akár az elmúlt hét napban is megtekintheti a feladatokat. A régebbi feladatok megtekintéséhez használjon [biztonsági mentési jelentéseket](backup-center-obtain-insights.md).

## <a name="vaults"></a>Kulcstartók

A biztonsági mentési központban a tárolók menüpont kiválasztásával megtekintheti **az összes** olyan [Recovery Services](backup-azure-recovery-services-vault-overview.md) - [tárolót és biztonsági mentési](backup-vault-overview.md) tárat, amelyhez hozzáfér. A listát a következő paraméterekkel szűrheti:

* Tár-előfizetés
* Tár erőforráscsoport
* Tároló neve
* A Szabályzathoz társított adatforrás-típus

A listában szereplő bármelyik elem kiválasztásával megnyithatja az adott tárat.

![Biztonsági mentési központ – tárolók](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Biztonsági mentési szabályzatok

A Backup Center segítségével megtekintheti és szerkesztheti a biztonsági mentési szabályzatok legfontosabb információit.

A **biztonsági mentési házirendek** menüpont megadásával megtekintheti az összes olyan házirendet, amelyet a biztonsági mentési hagyatékban hozott létre. A listát tár-előfizetés, Erőforráscsoport, adatforrás típusa és tároló alapján szűrheti. A rács egyik elemére a jobb gombbal kattintva megtekintheti az adott szabályzathoz tartozó kapcsolódó elemeket, szerkesztheti a szabályzatot, vagy akár törölheti is, ha szükséges.

![Backup Center – házirendek](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Következő lépések

* [A biztonsági mentési hagyaték szabályozása](backup-center-govern-environment.md)
* [Műveletek végrehajtása a Backup Center használatával](backup-center-actions.md)
* [Bepillantást nyerhet a biztonsági másolatokra](backup-center-obtain-insights.md)
