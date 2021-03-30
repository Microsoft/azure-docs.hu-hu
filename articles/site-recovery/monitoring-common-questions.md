---
title: Azure Site Recovery figyelésével kapcsolatos gyakori kérdések
description: Választ kaphat a Azure Site Recovery figyeléssel kapcsolatos gyakori kérdésekre, a beépített monitorozás és a Azure Monitor használatával (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82131156"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery figyelésével kapcsolatos gyakori kérdések

Ez a cikk az Azure [site Recovery](site-recovery-overview.md)figyelésével kapcsolatos gyakori kérdésekre ad választ, a beépített site Recovery monitorozás és a Azure Monitor (log Analytics) használatával.

## <a name="general"></a>Általános kérdések

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Miben különbözik a RPO érték a legutóbbi elérhető helyreállítási ponttól?

Site Recovery egy több lépésből álló aszinkron folyamatot használ a gépek Azure-ba történő replikálásához.

- A replikáció utolsó előtti lépésében a gép legutóbbi változásai a metaadatokkal együtt a log/cache Storage-fiókba másolódnak.
- Ezek a változások a helyreállítható pontot azonosító címkével együtt a célként megadott régióban lévő Storage-fiókba/felügyelt lemezre íródnak.
- Site Recovery most létrehozhat egy helyreállítható pontot a gép számára.
- Ezen a ponton a RPO eddig a Storage-fiókba feltöltött módosítások teljesülnek. Ez azt jelenti, hogy a gép RPO ezen a ponton a helyreállítható pontnak megfelelő időbélyegből eltelt idő értékével egyenlő.
- Most Site Recovery kiválasztja a feltöltött adatait a Storage-fiókból, és alkalmazza azt a géphez létrehozott replikás lemezekre.
- Site Recovery Ezután létrehoz egy helyreállítási pontot, és a feladatátvételkor elérhetővé teszi ezt a pontot a helyreállításhoz.
- Így a legújabb elérhető helyreállítási pont a legutóbbi helyreállítási pontnak megfelelő időbélyeget jelzi, amely a már feldolgozott és a replika lemezekre lett alkalmazva.


A replikáló forrásoldali gépen vagy a helyszíni infrastruktúra-kiszolgálókon nem megfelelő rendszeridő a kiszámított RPO értéket fogja eldönteni. Pontos RPO-jelentéskészítés esetén győződjön meg arról, hogy a rendszeróra pontos az összes kiszolgálón és gépen.



## <a name="inbuilt-site-recovery-logging"></a>Beépített Site Recovery naplózás


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Miért különböznek a virtuális gépek a tár infrastruktúra nézetében a replikált elemekben megjelenített összegek számától?

A tár infrastruktúra nézetét replikációs forgatókönyvek szerint kell kiterjeszteni. Csak a jelenleg kijelölt replikációs forgatókönyvben szereplő gépek szerepelnek a nézet darabszámában. Emellett csak azokat a virtuális gépeket számítjuk fel, amelyek az Azure-ba történő replikálásra vannak konfigurálva. A feladatátvételt végző gépek vagy egy helyszíni helyre replikált gépek nem számítanak bele a nézetbe.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Miért különböznek az alapvető eszközök replikált elemeinek száma az irányítópulton lévő replikált elemek teljes számától?

Csak a kezdeti replikálást elvégező gépek szerepelnek a Essentialsben megjelenő darabszámban. A replikált elemek összesen a tároló összes számítógépét tartalmazza, beleértve azokat is, amelyekhez jelenleg folyamatban van a kezdeti replikálás.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Milyen gyakran Site Recovery az erőforrás-naplók küldése Azure Monitor naplóba? 

- A AzureSiteRecoveryReplicationStats és a AzureSiteRecoveryRecoveryPoints 15 percenként küldik el.  
- A AzureSiteRecoveryReplicationDataUploadRate és a AzureSiteRecoveryProtectedDiskDataChurn küldése öt percenként történik. 
- A rendszer a AzureSiteRecoveryJobs a feladatok triggere és befejezése után továbbítja.
- A rendszer a AzureSiteRecoveryEvents egy esemény létrehozásakor küldi el. 
- A AzureSiteRecoveryReplicatedItems minden környezeti változás esetén elküldve. Az adatfrissítési idő jellemzően 15 perccel a változás után következik be. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Mennyi ideig tartanak a Azure Monitor naplókban tárolt adat? 

Alapértelmezés szerint a megőrzés 31 nap. A Log Analytics munkaterület **használati és becsült költségek** szakaszában növelheti az időszakot. Kattintson az **adatmegőrzés** lehetőségre, és válassza ki a tartományt.

### <a name="whats-the-size-of-the-resource-logs"></a>Mi az erőforrás-naplók mérete? 

A napló mérete általában 15-20 KB. 


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan figyelheti [site Recovery beépített monitorozást](site-recovery-monitor-and-troubleshoot.md)vagy [Azure monitort](monitor-log-analytics.md).


