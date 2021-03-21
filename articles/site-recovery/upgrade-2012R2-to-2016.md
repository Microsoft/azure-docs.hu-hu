---
title: A Windows Server/System Center VMM 2012 R2 frissítése a Windows Server 2016-Azure Site Recovery
description: Ismerje meg, hogyan frissítheti a Windows Server 2012 R2 rendszerű gazdagépeket & SCVMM 2012 R2-re, amelyek Azure Site Recovery-vel vannak konfigurálva a Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b9869ae7dfbf5afd6b8d3b870a2ad4e56fd54c1a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91250068"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>A Windows Server Server/System Center 2012 R2 VMM frissítése a Windows Serverre/VMM 2016 

Ez a cikk bemutatja, hogyan frissítheti a Windows Server 2012 R2 rendszerű gazdagépeket & SCVMM 2012 R2-re, amelyek Azure Site Recovery-vel vannak konfigurálva a Windows Server 2016 & SCVMM 2016

Site Recovery az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához járul hozzá. A szolgáltatás biztosítja, hogy a virtuális gépek számítási feladatai a vártnál továbbra is rendelkezésre álljanak, és váratlan kimaradások történnek.

> [!IMPORTANT]
> Ha olyan Windows Server 2012 R2 rendszerű gazdagépeket frissít, amelyek már konfigurálva vannak a Azure Site Recoveryrel való replikálásra, a jelen dokumentumban leírt lépéseket kell követnie. A frissítéshez kiválasztott más elérési utak nem támogatott állapotokat eredményezhetnek, és a replikáció megszakítását vagy feladatátvételi képességet okozhatnak.


Ebből a cikkből megtudhatja, hogyan frissítheti az alábbi konfigurációkat a környezetében:

> [!div class="checklist"]
> * **A SCVMM által nem felügyelt Windows Server 2012 R2 rendszerű gazdagépek** 
> * **Windows Server 2012 R2 rendszerű, önálló SCVMM 2012 R2-kiszolgáló által felügyelt gazdagépek** 
> * **Windows Server 2012 R2 rendszerű gazdagépek, amelyeket a magasan elérhető SCVMM 2012 R2-kiszolgáló felügyel**


## <a name="prerequisites--factors-to-consider"></a>Az előfeltételek & Megfontolandó tényezők

A frissítés előtt vegye figyelembe a következőket:

- Ha olyan Windows Server 2012 R2 rendszerű gazdagépeket használ, amelyeket nem a SCVMM felügyel, és ez egy önálló környezet beállítása, akkor a replikáció megszakítása szükséges, ha megpróbálja végrehajtani a frissítést.
- Ha úgy döntött, hogy a "*nem tárolja a kulcsokat az elosztott kulcs kezelése alatt Active Directoryban*", a SCVMM 2012 R2 telepítése során, a frissítések nem fognak sikeresen befejeződik.

- Ha a System Center 2012 R2 VMM használja, 

    - Keresse meg az adatbázis adatait a VMM: **VMM-konzol**  ->  **beállításai**  ->  **általános**  ->  **adatbázis-kapcsolatok**
    - A System Center Virtual Machine Manager ügynök szolgáltatáshoz használt szolgáltatásfiókok keresése
    - Győződjön meg arról, hogy rendelkezik a VMM-adatbázis biztonsági másolatával.
    - Jegyezze fel az érintett SCVMM-kiszolgálók adatbázisának nevét. Ezt úgy teheti meg, hogy a **VMM-konzol**  ->  **beállításai**  ->  **általános**  ->  **adatbázis-kapcsolatban** navigál
    - Jegyezze fel az elsődleges és a helyreállítási VMM-kiszolgálók VMM-AZONOSÍTÓját. A VMM azonosítója a "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup" beállításjegyzékből érhető el.
    - Győződjön meg arról, hogy a fürthöz hozzáadott új SCVMMs ugyanazokkal a névvel rendelkezik, mint korábban. 

- Ha mindkét oldalon a SCVMMs által felügyelt két hely között replikálódik, győződjön meg arról, hogy az elsődleges oldal frissítése előtt először frissítse a helyreállítási oldalt.
  > [!WARNING]
  > A SCVMM 2012 R2 frissítése közben az elosztott kulcskezelő területen válassza a **titkosítási kulcsok Active Directoryban való tárolását**. Gondosan válassza ki a szolgáltatásfiók és az elosztott kulcsok felügyeletének beállításait. A kiválasztástól függően előfordulhat, hogy a titkosított, például a sablonok jelszavai nem lesznek elérhetők a frissítés után, és potenciálisan befolyásolhatják a replikálást Azure Site Recovery

> [!IMPORTANT]
> Tekintse meg az [Előfeltételek](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations) részletes SCVMM dokumentációját.

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>A SCVMM által nem felügyelt Windows Server 2012 R2 rendszerű gazdagépek 
Az alább említett lépések listája a [Hyper-V-gazdagépekről az Azure-ba történő](./hyper-v-azure-architecture.md) felhasználói konfigurációra vonatkozik, amelyet a jelen [oktatóanyag](./hyper-v-prepare-on-premises-tutorial.md) követ

> [!WARNING]
> Az előfeltételekben említetteknek megfelelően ezek a lépések csak fürtözött környezeti forgatókönyvekre vonatkoznak, és nem önálló Hyper-V-gazdagép-konfigurációra.

1. A [működés közbeni fürt frissítésének](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) végrehajtásához kövesse a lépéseket. a működés közbeni fürt frissítési folyamatának végrehajtásához.
2. A fürtben bevezetett minden új Windows Server 2016-gazdagép esetében távolítsa el a Windows Server 2012 R2 rendszerű gazdagép hivatkozását a Azure Site Recoveryról a következő, említett lépések [itt] című témakörben leírtak szerint. Ennek a gazdagépnek kell lennie, amelyet úgy döntött, hogy kiüríti & kizárását a fürtből.
3. Miután az *Update-VMVersion* parancs végrehajtása megtörtént az összes virtuális gépen, a frissítések befejeződtek. 
4. Az [itt](./hyper-v-azure-tutorial.md#set-up-the-source-environment) említett lépéseket követve regisztrálja az új Windows Server 2016-gazdagépet Azure site Recovery. Vegye figyelembe, hogy a Hyper-V-hely már aktív, és csak regisztrálnia kell az új gazdagépet a fürtben. 
5.  Nyissa meg Azure Portal és ellenőrizze a replikált állapotot a Recovery Serviceson belül.

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Önálló SCVMM 2012 R2-kiszolgáló által felügyelt Windows Server 2012 R2 rendszerű gazdagépek frissítése
A Windows Server 2012 R2 rendszerű gazdagépek frissítése előtt frissítenie kell a SCVMM 2012 R2-t a SCVMM 2016-ra. Kövesse az alábbi lépéseket:

**Önálló SCVMM 2012 R2 frissítése SCVMM 2016-re**

1.  Az ASR-szolgáltató eltávolításához navigáljon a Vezérlőpult – > programok – > programok és szolgáltatások – >Microsoft Azure Site Recovery, és kattintson az Eltávolítás elemre.
2. [A SCVMM adatbázisának megőrzése és az operációs rendszer frissítése](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. A **Programok eltávolítása** területen válassza a **VMM**  >  **Eltávolítás** elemet. b. Válassza a **szolgáltatások eltávolítása** lehetőséget, majd válassza a V **mm felügyeleti kiszolgáló és a VMM-konzol** elemet. c. Az **adatbázis beállításai** területen válassza az **adatbázis megőrzése** lehetőséget. d. Tekintse át az összegzést, és kattintson az **Eltávolítás** gombra.

4. [A VMM 2016 telepítése](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Indítsa el a SCVMM, és győződjön meg arról, hogy az egyes gazdagépek állapota a **hálók** lapon található. A legutóbbi állapot lekéréséhez kattintson a **frissítés** gombra. A "figyelmet igényel" állapotnak kell megjelennie. 
17. Telepítse a legújabb [Microsoft Azure site Recovery szolgáltatót](https://aka.ms/downloaddra) a SCVMM.
16. Telepítse a legújabb [Microsoft Azure Recovery Service-(MARS-) ügynököt](https://aka.ms/latestmarsagent) a fürt minden egyes gazdagépén. A frissítéssel biztosíthatja, hogy a SCVMM képes legyen sikeresen lekérdezni a gazdagépeket.

**Windows Server 2012 R2 rendszerű gazdagépek frissítése Windows Server 2016-re**

1. Kövesse az [itt](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) leírt lépéseket a működés közbeni fürt frissítési folyamatának végrehajtásához. 
2. Miután hozzáadta az új gazdagépet a fürthöz, frissítse a gazdagépet a SCVMM-konzolról, és telepítse a VMM-ügynököt erre a frissített gazdagépre.
3. Az *Update-VMVersion* végrehajtása a virtuális gépek virtuálisgép-verzióinak frissítéséhez. 
4.  Nyissa meg Azure Portal és ellenőrizze a Recovery Services-tárolóban lévő virtuális gépek replikált állapotának állapotát. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>A Windows Server 2012 R2 rendszerű gazdagépek frissítését a SCVMM 2012 R2-kiszolgáló felügyeli.
A Windows Server 2012 R2 rendszerű gazdagépek frissítése előtt frissítenie kell a SCVMM 2012 R2-t a SCVMM 2016-ra. A következő frissítési módok támogatottak a Azure Site Recovery-Mixed módban konfigurált SCVMM 2012 R2-kiszolgálók frissítése közben, további VMM-kiszolgálók nélkül & vegyes mód további VMM-kiszolgálókkal.

**A SCVMM 2012 R2 frissítése a SCVMM 2016-re**

1.  Az ASR-szolgáltató eltávolításához navigáljon a Vezérlőpult – > programok – > programok és szolgáltatások – >Microsoft Azure Site Recovery, és kattintson az Eltávolítás elemre.
2. Kövesse az [itt](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) említett lépéseket a végrehajtani kívánt verziófrissítési mód alapján.
3. Indítsa el a SCVMM konzolt, és az egyes állomások állapotát a **hálók** lapon. A legutóbbi állapot lekéréséhez kattintson a **frissítés** gombra. A "figyelmet igényel" állapotnak kell megjelennie.
4. Telepítse a legújabb [Microsoft Azure site Recovery szolgáltatót](https://aka.ms/downloaddra) a SCVMM.
5. Frissítse a legújabb [Microsoft Azure Recovery Service-(MARS-) ügynököt](https://aka.ms/latestmarsagent) a fürt minden egyes állomásán. A frissítéssel ellenőrizheti, hogy az SC VMM képes-e sikeresen lekérdezni a gazdagépeket.


**Windows Server 2012 R2 rendszerű gazdagépek frissítése Windows Server 2016-re**

1. Kövesse az [itt](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) leírt lépéseket a működés közbeni fürt frissítési folyamatának végrehajtásához.
2. Miután hozzáadta az új gazdagépet a fürthöz, frissítse a gazdagépet a SCVMM-konzolról, és telepítse a VMM-ügynököt erre a frissített gazdagépre.
3. Az *Update-VMVersion* végrehajtása a virtuális gépek virtuálisgép-verzióinak frissítéséhez. 
4.  Nyissa meg Azure Portal és ellenőrizze a Recovery Services-tárolóban lévő virtuális gépek replikált állapotának állapotát. 

## <a name="next-steps"></a>Következő lépések
A gazdagépek frissítését követően [feladatátvételi tesztet](tutorial-dr-drill-azure.md) hajthat végre a replikáció és a vész-helyreállítási állapot ellenőrzéséhez.

