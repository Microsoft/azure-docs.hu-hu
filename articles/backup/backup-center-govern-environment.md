---
title: Biztonsági mentések szabályozása a Backup Centerrel
description: Ismerje meg, hogyan szabályozhatja az Azure-környezetet annak biztosításához, hogy az összes erőforrás megfelel a Backup Center biztonsági mentési perspektívájának.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506209"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Biztonsági mentések szabályozása a Backup Centerrel

A Backup Center segítségével szabályozhatja az Azure-környezetet, így biztosítva, hogy minden erőforrás megfelel a biztonsági mentési perspektívának. Az alábbiakban a Backup Center irányítási képességeiből tájékozódhat:

* Azure-szabályzatok megtekintése és kiosztása a biztonsági mentéshez

* Tekintse meg az erőforrások megfelelőségét az összes beépített Azure-szabályzatban a biztonsági mentéshez.

* A biztonsági mentéshez nem konfigurált összes adatforrás megtekintése.

## <a name="supported-scenarios"></a>Támogatott esetek

* A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="azure-policies-for-backup"></a>Azure-szabályzatok biztonsági mentéshez

A biztonsági mentéshez elérhető összes [Azure-házirend](../governance/policy/overview.md) megtekintéséhez válassza ki az **Azure-szabályzatok biztonsági mentéshez** menüpontot. Ez megjeleníti az előfizetések és az erőforráscsoportok hozzárendeléséhez elérhető összes beépített és egyéni [Azure-házirend-definíciót a biztonsági mentéshez](policy-reference.md) .

A definíciók bármelyikének kiválasztásával [hozzárendelheti a szabályzatot](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) egy hatókörhöz.

![Azure Policy definíciók kiválasztása](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Biztonsági mentés megfelelősége

A biztonsági mentés megfelelősége menüpontra kattintva megtekintheti az erőforrások [megfelelőségét](../governance/policy/how-to/get-compliance-data.md) az Azure-környezethez rendelt különböző beépített szabályzatoknak megfelelően. Megtekintheti az összes szabályzatnak megfelelő erőforrások százalékos arányát, valamint azokat a házirendeket, amelyek egy vagy több nem megfelelő erőforrással rendelkeznek.

![Biztonsági másolatok megfelelőségének megtekintése](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Védhető adatforrások

A **Protected DataSources** menüpont kiválasztásával megtekintheti az összes olyan adatforrást, amely nem lett konfigurálva a biztonsági mentéshez. A listát adatforrás-előfizetés, Erőforráscsoport, hely, típus és címkék alapján szűrheti. Miután azonosította a biztonsági mentésre szoruló adatforrást, kattintson a jobb gombbal a megfelelő rácsvonal elemre, és válassza a **biztonsági** mentés lehetőséget az erőforrás biztonsági mentésének konfigurálásához.

![Védhető adatforrások menü](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Ha az **SQL-t az Azure virtuális gépen** az adatforrás típusaként választja, a **védhető adatforrások** nézet az összes olyan katalógusbeli virtuális gép listáját jeleníti meg, amelyek nem rendelkeznek biztonsági mentésre konfigurált SQL-adatbázisokkal.
> Ha az **Azure Storage (Azure Files)** lehetőséget választja az adatforrás típusaként, a **védhető adatforrások** nézet megjeleníti az összes olyan Storage-fiók listáját (amely támogatja a fájlmegosztást), amelyek nem rendelkeznek a biztonsági mentésre konfigurált fájlmegosztás-megosztással.


## <a name="next-steps"></a>Következő lépések

* [Biztonsági másolatok figyelése és üzemeltetése](backup-center-monitor-operate.md)
* [Műveletek végrehajtása a Backup Center használatával](backup-center-actions.md)
* [Bepillantást nyerhet a biztonsági másolatokra](backup-center-obtain-insights.md)