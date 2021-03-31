---
title: Azure Site Recovery-tároló áthelyezése egy másik régióba
description: Útmutató Recovery Services-tároló (Azure Site Recovery) másik Azure-régióba való áthelyezéséhez
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422640"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Recovery Services-tároló áthelyezése és Azure Site Recovery konfigurálása egy másik Azure-régióba

A meglévő Azure-erőforrások egyik régióból a másikba való áthelyezéséhez különböző helyzetekre van szükség. Ilyenek például a kezelhetőség, az irányítási okok, vagy a vállalati fúziók és a beszerzések. Az Azure-beli virtuális gépek áthelyezésekor áthelyezett kapcsolódó erőforrások egyike a vész-helyreállítási konfiguráció. 

A meglévő vész-helyreállítási konfiguráció egyik régióból a másikba való áthelyezéséhez nincs első osztályú módszer. Ennek az az oka, hogy a célként megadott régiót a forrásoldali virtuálisgép-régió alapján konfigurálta. Ha úgy dönt, hogy megváltoztatja a forrás régiót, a célként megadott régió korábbi konfigurációit nem lehet újra felhasználni, és alaphelyzetbe kell állítani. Ez a cikk a vész-helyreállítási beállítások újrakonfigurálásának lépésenkénti folyamatát határozza meg, és egy másik régióba helyezi át.

Ebben a dokumentumban a következőket fogja megtekinteni:

> [!div class="checklist"]
> * Ellenőrizze az áthelyezés előfeltételeit.
> * Azonosítsa az Azure Site Recovery által használt erőforrásokat.
> * Tiltsa le a replikációt.
> * Törölje az erőforrásokat.
> * Site Recovery beállítása a virtuális gépek új forrásoldali régiója alapján.

> [!IMPORTANT]
> Jelenleg nincs olyan első osztályú módszer, amely egy Recovery Services-tárolót és a vész-helyreállítási konfigurációt egy másik régióba helyezi át. Ez a cikk végigvezeti a replikáció letiltásának és az új régióban való beállításának folyamatán.

## <a name="prerequisites"></a>Előfeltételek

- Mielőtt az Azure-beli virtuális gépeket más régióba helyezi, távolítsa el és törölje a vész-helyreállítási konfigurációt. 

  > [!NOTE]
  > Ha az Azure-beli virtuális gép új célcsoportja megegyezik a vész-helyreállítási célként megadott régióval, használhatja a meglévő replikációs konfigurációt, és áthelyezheti azt. Kövesse az [Azure IaaS-beli virtuális gépek áthelyezése másik Azure-régióba](azure-to-azure-tutorial-migrate.md)című témakör lépéseit.

- Győződjön meg arról, hogy tájékozott döntést hoz, és tájékoztatja az érintetteket. A virtuális gép nem lesz védve a katasztrófák ellen, amíg a virtuális gép áthelyezése be nem fejeződik.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Az Azure Site Recovery által használt erőforrások azonosítása
Javasoljuk, hogy ezt a lépést a következőre való továbblépés előtt végezze el. A virtuális gépek replikálásakor könnyebb azonosítani a kapcsolódó erőforrásokat.

Minden replikált Azure-beli virtuális gép esetében lépjen a **védett elemek**  >  **replikált** elemek  >  **tulajdonságaiba** , és azonosítsa a következő erőforrásokat:

- Cél-erőforráscsoport
- Gyorsítótáras Storage-fiók
- Cél Storage-fiók (nem felügyelt lemezes Azure-beli virtuális gép esetén) 
- Célként megadott hálózat


## <a name="disable-the-existing-disaster-recovery-configuration"></a>A vész-helyreállítási meglévő konfiguráció letiltása

1. Nyissa meg a Recovery Services-tárolót.
2. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre, és válassza a **replikáció letiltása** lehetőséget.
3. Ismételje meg ezt a lépést minden olyan virtuális gép esetében, amelyet át szeretne helyezni.

> [!NOTE]
> Nem távolítja el a mobilitási szolgáltatást a védett kiszolgálókról. Manuálisan kell eltávolítania. Ha azt tervezi, hogy ismét meg szeretné tenni a kiszolgálót, akkor kihagyhatja a mobilitási szolgáltatás eltávolítását.

## <a name="delete-the-resources"></a>Erőforrások törlése

1. Nyissa meg a Recovery Services-tárolót.
2. Válassza a **Törlés** elemet.
3. Törölje az összes [korábban azonosított](#identify-the-resources-that-were-used-by-azure-site-recovery)erőforrást.
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure-beli virtuális gépek áthelyezése az új célként megadott régióba

Kövesse az alábbi cikkekben ismertetett lépéseket az Azure-beli virtuális gépek célcsoportra helyezésének követelménye alapján:

- [Azure-beli virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)
- [Azure-beli virtuális gépek áthelyezése Availability Zonesba](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Site Recovery beállítása a virtuális gépek új forrásoldali régiója alapján

Állítsa be az új régióba áthelyezett Azure-beli virtuális gépek vész-helyreállítását az [Azure-beli virtuális gépek vész-helyreállításának beállítása](azure-to-azure-tutorial-enable-replication.md)című témakör lépéseit követve.
