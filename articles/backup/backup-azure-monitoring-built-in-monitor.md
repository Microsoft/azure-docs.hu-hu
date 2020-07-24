---
title: Azure Backup védett munkaterhelések figyelése
description: Ebből a cikkből megtudhatja, hogyan használhatók a Azure Backup munkaterhelések figyelési és értesítési képességei a Azure Portal használatával.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 645bf701a8f8561b2cb4f19378036ada82c4bedd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054550"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup munkaterhelések figyelése

Azure Backup több biztonsági mentési megoldást biztosít a biztonsági mentési követelmények és az infrastruktúra-topológia (helyszíni vagy Azure) alapján. Minden biztonsági mentési felhasználónak vagy rendszergazdának látnia kell, hogy mi történik az összes megoldásban, és fontos forgatókönyvekben várhatóan értesítést kapjon. Ez a cikk a Azure Backup szolgáltatás által biztosított figyelési és értesítési képességeket részletezi.

## <a name="backup-jobs-in-recovery-services-vault"></a>Biztonsági mentési feladatok a Recovery Services-tárolóban

A Azure Backup a Azure Backup által védett munkaterhelések számára beépített figyelési és riasztási képességeket biztosít. A Recovery Services-tároló beállításaiban a **figyelés** szakasz beépített feladatokat és riasztásokat tartalmaz.

![RS-tár beépített figyelése](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

A feladatok akkor jönnek létre, amikor olyan műveletek történnek, mint a biztonsági mentés konfigurálása, a biztonsági mentés, a visszaállítás, a biztonsági másolat törlése és így tovább.

Az alábbi Azure Backup-megoldásokból származó feladatok itt láthatók:

- Azure-beli virtuális gép biztonsági mentése
- Azure-fájlok biztonsági mentése
- Azure-beli számítási feladatok biztonsági mentése, például SQL és SAP HANA
- Microsoft Azure Recovery Services (MARS) ügynök

A System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) feladatok nem jelennek meg.

> [!NOTE]
> Az Azure-beli virtuális gépeken belüli SQL-és SAP HANA biztonsági másolatok nagy számú biztonsági mentési feladattal rendelkeznek. A naplók biztonsági mentései például 15 percenként is futtathatók. Így az adatbázis-munkaterhelések esetében csak a felhasználó által aktivált műveletek jelennek meg. Az ütemezett biztonsági mentési műveletek nem jelennek meg.

## <a name="backup-alerts-in-recovery-services-vault"></a>Biztonsági mentési riasztások Recovery Services-tárolóban

A riasztások elsődlegesen olyan helyzetek, amikor a felhasználók értesítést kapnak, hogy el tudják végezni a megfelelő lépéseket. A **biztonsági mentési riasztások** szakasz a Azure Backup szolgáltatás által generált riasztásokat jeleníti meg. Ezeket a riasztásokat a szolgáltatás definiálja, és a felhasználó nem hozhat létre egyéni riasztásokat.

### <a name="alert-scenarios"></a>Riasztási forgatókönyvek

A szolgáltatás a következő forgatókönyveket figyelmeztethető forgatókönyvként határozza meg.

- Biztonsági mentési/visszaállítási hibák
- A biztonsági mentés sikeres volt, Microsoft Azure Recovery Services (MARS) ügynök figyelmeztetései
- A védelem leállítása és a védelem leállítása az adattörlési szolgáltatással

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Itt jelennek meg a következő Azure Backup-megoldások riasztásai

- Azure-beli virtuális gép biztonsági mentései
- Azure File biztonsági mentései
- Az Azure munkaterhelés biztonsági mentései, mint például az SQL, SAP HANA
- Microsoft Azure Recovery Services (MARS) ügynök 

> [!NOTE]
> A System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) riasztásai itt nem jelennek meg.

### <a name="consolidated-alerts"></a>Konszolidált riasztások

Az Azure munkaterhelés biztonsági mentési megoldásai, például az SQL és a SAP HANA esetében a naplók biztonsági mentése nagyon gyakori (akár 15 percenként, a szabályzatnak megfelelően) hozható létre. Ezért is lehetséges, hogy a napló biztonsági mentésével kapcsolatos hibák is nagyon gyakoriak (akár 15 percenként). Ebben az esetben a végfelhasználó akkor lesz túlterhelve, ha riasztás jön létre az egyes hibák előfordulásakor. Így a rendszer riasztást küld az első előfordulásra, és ha a későbbi hibák ugyanarra az alapvető ok miatt következnek be, akkor a további riasztások nem jönnek létre. Az első riasztás a hibák számával frissül. Ha azonban a felhasználó inaktiválja a riasztást, akkor a következő esemény egy másik riasztást indít el, és ez az esemény első riasztása lesz. Így Azure Backup végrehajtja a riasztások összevonását az SQL és a SAP HANA biztonsági mentések esetében.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Kivételek, ha a riasztás nem merült fel

A riasztások nem jelennek meg a hibák miatt. Ezek a következők:

- A felhasználó explicit módon megszakította a futó feladatot.
- A feladatot nem sikerült végrehajtani, mert folyamatban van egy másik biztonsági mentési művelet (semmi köze itt, mert csak meg kell várni, amíg az előző feladatnak befejeződik)
- A virtuális gép biztonsági mentési feladata meghiúsul, mert a biztonsági másolattal rendelkező Azure-beli virtuális gép már nem létezik
- [Konszolidált riasztások](#consolidated-alerts)

A fenti kivételek úgy lettek kialakítva, hogy a műveletek eredménye (elsősorban a felhasználó által aktivált) azonnal megjelenik a portálon/PS/CLI-ügyfeleken. Így a felhasználó azonnal tisztában van, és nem igényel értesítést.

### <a name="alert-types"></a>Riasztástípusok

A riasztás súlyossága alapján a riasztásokat háromféle típusban lehet meghatározni:

- **Kritikus**: elvileg a biztonsági mentési vagy helyreállítási hibák (ütemezett vagy felhasználó által aktivált) a riasztások generálásához vezetnek, és kritikus riasztásként, valamint olyan romboló műveletekkel is rendelkeznek, mint például a biztonsági mentés törlése.
- **Figyelmeztetés**: Ha a biztonsági mentési művelet sikeres, de néhány figyelmeztetéssel rendelkezik, azok figyelmeztető riasztásként jelennek meg. A figyelmeztető riasztások jelenleg csak Azure Backup ügynök biztonsági mentései esetén érhetők el.
- **Tájékoztatás**: jelenleg Azure Backup szolgáltatás nem hoz létre tájékoztató riasztást.

## <a name="notification-for-backup-alerts"></a>Értesítés a biztonsági mentési riasztásokról

> [!NOTE]
> Az értesítések konfigurálása csak az Azure Portalon végezhető el. A PS/CLI/REST API/Azure Resource Manager sablon támogatása nem támogatott.

A riasztások kiemelése után a rendszer értesíti a felhasználókat. A Azure Backup e-mailben egy beépített értesítési mechanizmust biztosít. Megadhat egyéni e-mail-címeket vagy terjesztési listát, amelyekről értesítést kaphat, ha riasztás jön létre. Azt is megadhatja, hogy az egyes riasztások értesítést kapjanak-e, vagy egy óránkénti kivonatban csoportosítsa őket, majd értesítést kap.

![RS-tároló beépített e-mail-értesítése](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Ha az értesítés konfigurálva van, egy üdvözlő vagy bevezető e-mailt fog kapni. Ezzel erősíti meg, hogy Azure Backup e-maileket küldhet ezekre a címekre riasztás esetén.<br>

Ha a gyakoriságot egy óránkénti kivonatoló értékre állították be, és egy órán belül megoldották a riasztást, akkor nem lesz része a közelgő óránkénti kivonatnak.

> [!NOTE]
>
> - Ha olyan roncsolásos műveletet végez, mint például a **védelem leállítása a törlési adatokkal** , akkor a rendszer riasztást küld, és e-mailt küld az előfizetés-tulajdonosoknak, a rendszergazdáknak és a társ-rendszergazdáknak akkor is, ha az értesítések nincsenek konfigurálva a helyreállítási tárolóhoz.
> - A sikeres feladatok értesítésének konfigurálásához használja a [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Riasztások inaktiválása

Aktív riasztás inaktiválásához/feloldásához kattintson az inaktiválni kívánt riasztáshoz tartozó listaelemre. Ekkor megnyílik egy képernyő, amely részletes információkat jelenít meg a riasztásról, és a felül található "inaktiválás" gombra kattint. Erre a gombra kattintva megváltoztathatja a riasztás állapotát az "inaktív" értékre. Inaktiválhat egy riasztást úgy is, hogy a jobb gombbal rákattint a riasztáshoz tartozó listaelemre, és kiválasztja az "inaktiválás" elemet.

![RS Vault-riasztás inaktiválása](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>További lépések

[Azure Backup-munkaterhelések figyelése Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md)
