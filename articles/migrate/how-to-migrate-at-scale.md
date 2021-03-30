---
title: Áttelepítési gép áttelepítésének automatizálása Azure Migrate
description: Ismerteti, hogyan használhatók a parancsfájlok nagy számú gép áttelepítésére Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754232"
---
# <a name="scale-migration-of-vms"></a>Virtuális gépek áttelepítésének méretezése 

Ebből a cikkből megtudhatja, hogyan használhatók a parancsfájlok a nagy számú virtuális gép (VM) áttelepítéséhez. Az áttelepítés méretezéséhez használja a [Azure site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery szkriptek letölthetők a GitHubon található [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) -tárházban. A parancsfájlok használatával a VMware, az AWS, a GCP-alapú virtuális gépek és a fizikai kiszolgálók áttelepíthetők az Azure-ban felügyelt lemezekre. Ezeket a parancsfájlokat használhatja a Hyper-V virtuális gépek áttelepítéséhez is, ha fizikai kiszolgálóként telepíti át a virtuális gépeket. A Azure Site Recovery PowerShellt használó [parancsfájlok dokumentálva](../site-recovery/vmware-azure-disaster-recovery-powershell.md)vannak.

## <a name="current-limitations"></a>Aktuális korlátozások
- Támogatás csak a statikus IP-cím megadása a célként megadott virtuális gép elsődleges hálózati adapteréhez
- A parancsfájlok nem tesznek Azure Hybrid Benefit kapcsolódó bemeneteket, manuálisan frissítenie kell a replikált virtuális gép tulajdonságait a portálon.

## <a name="how-does-it-work"></a>Hogyan működik?

### <a name="prerequisites"></a>Előfeltételek
A Kezdés előtt a következő lépéseket kell elvégeznie:
- Győződjön meg arról, hogy a Site Recovery-tároló létre lett hozva az Azure-előfizetésében
- Győződjön meg arról, hogy a konfigurációs kiszolgáló és a folyamat kiszolgálója telepítve van a forrás környezetében, és a tár képes felderíteni a környezetet
- Győződjön meg arról, hogy a konfigurációs kiszolgáló létrehoz és társít egy replikációs házirendet
- Győződjön meg arról, hogy a virtuális gép rendszergazdai fiókját adta hozzá a konfigurációs kiszolgálóhoz (amelyet a helyszíni virtuális gépek replikálásához fog használni)
- Győződjön meg arról, hogy a cél-összetevők az Azure-ban jönnek létre
    - Cél erőforráscsoport
    - Cél Storage-fiók (és az erőforráscsoport) – hozzon létre egy Premium Storage-fiókot, ha prémium szintű felügyelt lemezekre kíván áttérni
    - Cache Storage-fiók (és az erőforráscsoport) – hozzon létre egy szabványos Storage-fiókot ugyanabban a régióban, ahol a tároló található.
    - Cél Virtual Network a feladatátvételhez (és az erőforráscsoport)
    - Célként megadott alhálózat
    - Cél Virtual Network a feladatátvételi teszthez (és az erőforráscsoporthoz)
    - Rendelkezésre állási csoport (ha szükséges)
    - Célként megadott hálózati biztonsági csoport és az erőforráscsoport
- Győződjön meg arról, hogy a cél virtuális gép tulajdonságairól döntött
    - Cél virtuális gép neve
    - Cél virtuális gép mérete az Azure-ban (Azure Migrate Assessment használatával is megadható)
    - A virtuális gépen lévő elsődleges hálózati adapter magánhálózati IP-címe
- A szkriptek letöltése [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) -tárházból a githubon

### <a name="csv-input-file"></a>CSV bemeneti fájl
Az összes előfeltétel befejezését követően létre kell hoznia egy CSV-fájlt, amely az összes áttelepíteni kívánt forrásoldali gépre vonatkozó adattal rendelkezik. A bemeneti CSV-fájlnak tartalmaznia kell egy fejlécet a bemeneti adatokkal és egy olyan sorral, amely az összes áttelepíteni kívánt gép részleteit ismerteti. Minden parancsfájl úgy van kialakítva, hogy ugyanazon a CSV-fájlon működjön. A minta CSV-sablon a hivatkozáshoz tartozó Scripts mappában érhető el.

### <a name="script-execution"></a>Parancsfájl-végrehajtás
Miután a CSV elkészült, végrehajthatja a következő lépéseket a helyszíni virtuális gépek áttelepítésének elvégzéséhez:

**. Lépés #** | **Parancsfájl neve** | **Leírás**
--- | --- | ---
1 | asr_startmigration.ps1 | Engedélyezze a replikálást a CSV-ben felsorolt összes virtuális gépen, a parancsfájl CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
2 | asr_replicationstatus.ps1 | A replikáció állapotának ellenőrzését, a parancsfájl létrehoz egy CSV-t az egyes virtuális gépek állapotával.
3 | asr_updateproperties.ps1 | Ha a virtuális gépek replikálása/védelme megtörtént, a parancsfájl használatával frissítse a virtuális gép céljának tulajdonságait (számítási és hálózati tulajdonságok)
4 | asr_propertiescheck.ps1 | Annak ellenőrzése, hogy a tulajdonságok megfelelően frissültek-e
5 | asr_testmigration.ps1 |  Indítsa el a CSV-ben felsorolt virtuális gépek feladatátvételi tesztjét, a parancsfájl CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
6 | asr_cleanuptestmigration.ps1 | Ha manuálisan ellenőrzi azokat a virtuális gépeket, amelyek feladatátvétele sikertelen volt, ezt a parancsfájlt használhatja a feladatátvételi teszt virtuális gépek karbantartásához.
7 | asr_migration.ps1 | Végezzen el nem tervezett feladatátvételt a CSV-fájlban felsorolt virtuális gépeken, a szkript CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel. A parancsfájl nem állítja le a helyszíni virtuális gépeket a feladatátvétel elindítása előtt, az alkalmazás konzisztenciája érdekében ajánlott manuálisan leállítani a virtuális gépeket a parancsfájl végrehajtása előtt.
8 | asr_completemigration.ps1 | A véglegesítő művelet végrehajtása a virtuális gépeken és a Azure Site Recovery entitások törlése
9 | asr_postmigration.ps1 | Ha hálózati biztonsági csoportokat kíván hozzárendelni a hálózati adapterek feladatátvétele után, ezt a parancsfájlt használhatja. Hozzárendel egy NSG a célként megadott virtuális gépen lévő egyik hálózati adapterhez.

## <a name="how-to-migrate-to-managed-disks"></a>Migrálás a Managed Disks szolgáltatásba
Alapértelmezés szerint a parancsfájl áttelepíti a virtuális gépeket a felügyelt lemezekre az Azure-ban. Ha a megadott Storage-fiók egy Premium Storage-fiók, a prémium szintű felügyelt lemezek az áttelepítés után jönnek létre. A cache Storage-fiók továbbra is szabványos fiók lehet. Ha a célként megadott Storage-fiók egy szabványos Storage-fiók, a standard lemezeket az áttelepítés után hozza létre a rendszer. 

## <a name="next-steps"></a>Következő lépések

[További](../site-recovery/migrate-tutorial-on-premises-azure.md) információ a kiszolgálók Azure-ba való áttelepítéséről Azure site Recovery használatával
