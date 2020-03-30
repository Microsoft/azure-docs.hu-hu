---
title: Architektúra-VMware/fizikai vészhelyreállítás egy másodlagos helyre az Azure Site Recovery szolgáltatással
description: Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépek vagy fizikai Windows/Linux-kiszolgálók vész-helyreállítási során használt összetevőkről és architektúráról egy másodlagos VMware-webhelyre az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954577"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>VMware/fizikai kiszolgáló replikációjának architektúrája egy másodlagos helyszíni helyre

Ez a cikk a vész-helyreállítási replikáció, a feladatátvétel és a helyszíni VM-virtuális gépek (VM-ek) vagy fizikai Windows/Linux-kiszolgálók azure [Site Recovery](site-recovery-overview.md)használatával egy másodlagos VMware-helyre történő beállításakor használt architektúrát és folyamatokat ismerteti.


## <a name="architectural-components"></a>Az architektúra összetevői

**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | A forgatókönyv üzembe helyezéséhez használja az InMage Scout segédprogramot. | Az InMage Scout beszerzéséhez Azure-előfizetésre van szüksége.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket az üzembe helyezés előkészítéséhez.
**Folyamatkiszolgáló** | Az elsődleges helyen található | Helyezze üzembe a folyamatkiszolgálót, amely kezeli az adatok gyorsítótárazását, tömörítését és optimalizálását.<br/><br/> Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**Konfigurációs kiszolgáló** | A másodlagos helyen található | A konfigurációs kiszolgáló végzi az üzemelő példány felügyeleti webhelyen vagy a vContinuum-konzolban végzett felügyeletét, konfigurálását és megfigyelését.
**vContinuum-kiszolgáló** | Választható. Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót. | Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet.
**Fő célkiszolgáló** | A másodlagos helyen található | A fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat.<br/><br/> Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani.<br/><br/> Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. A Unified Agent ügynök nincs telepítve ezen a kiszolgálón.
**VMware ESX/ESXi- és vCenter-kiszolgáló** |  A virtuális gépek ESX-/ESXi-gazdagépeken futnak. A gazdagépeket egy vCenter-kiszolgáló felügyeli | A VMware virtuális gépek replikálásához VMware-infrastruktúrára van szükség.
**Virtuális gépek/fizikai kiszolgálók** |  A replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon telepített Unified Agent. | Ez az ügynök valósítja meg az összetevők közötti kommunikációt.

## <a name="replication-process"></a>Replikációs folyamat

1. Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt.
2. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak.
3. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

**6. ábra: VMware és VMware közötti replikáció**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>További lépések

[Állítsa be](vmware-physical-secondary-disaster-recovery.md) a Virtuálisgép-virtuális gépek és a fizikai kiszolgálók vész-helyreállítását egy másodlagos helyre.
