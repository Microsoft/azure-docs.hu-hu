---
title: VMware/fizikai vész-helyreállítás méretezése Azure Site Recovery
description: Megtudhatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba nagy számú helyszíni VMware virtuális gép vagy fizikai kiszolgáló esetén Azure Site Recovery használatával.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 869702c9eaac4a38f6b0e64311c772892ddd6d90
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580448"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>A vész-helyreállítási szolgáltatás beállítása a VMware virtuális gépek/fizikai kiszolgálók esetében

Ez a cikk azt ismerteti, hogyan állítható be vész-helyreállítás az Azure-ban nagy számokra (> 1000) a helyszíni VMware virtuális gépekre vagy az éles környezetben működő fizikai kiszolgálókra a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="define-your-bcdr-strategy"></a>A BCDR stratégia meghatározása

Az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégia részeként meg kell határoznia a helyreállítási pontok célkitűzéseit (RPO) és a helyreállítási idő célkitűzéseit (RTOs) az üzleti alkalmazásaihoz és a munkaterhelésekhez. A RTO méri az időtartamot és a szolgáltatási szintet, amelyen belül egy üzleti alkalmazást vagy folyamatot vissza kell állítani és elérhetőnek kell lennie a folytonossági problémák elkerülése érdekében.
- Site Recovery folyamatos replikálást biztosít a VMware virtuális gépek és a fizikai kiszolgálók számára, valamint egy [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) -t a RTO.
- A VMware virtuális gépek nagy méretű vész-helyreállításának megtervezése és a szükséges Azure-erőforrások megállapítása során megadhat egy RTO értéket, amelyet a rendszer a kapacitás számításához fog használni.


## <a name="best-practices"></a>Ajánlott eljárások

A nagy léptékű vész-helyreállítással kapcsolatos általános ajánlott eljárások. Ezeket az ajánlott eljárásokat részletesebben a dokumentum következő részeiben tárgyaljuk.

- A **megcélzott követelmények meghatározása**: a vész-helyreállítás beállítása előtt mérje fel az Azure kapacitás-és erőforrás-szükségleteit.
- **Site Recovery-összetevők megtervezése**: állapítsa meg, hogy milyen site Recovery-összetevőket (konfigurációs kiszolgálót, folyamat-kiszolgálókat) kell teljesítenie a becsült kapacitásnak.
- **Egy vagy több kibővíthető folyamat kiszolgálójának beállítása**: ne használja a konfigurációs kiszolgálón alapértelmezés szerint futó folyamatot. 
- **A legújabb frissítések futtatása**: az site Recovery-csapat rendszeresen felszabadítja site Recovery összetevők új verzióit, és meg kell győződnie arról, hogy a legújabb verziókat futtatja. Ezzel a megoldással a frissítések [újdonságait](site-recovery-whats-new.md) követheti nyomon, valamint [engedélyezheti és telepítheti a frissítéseket](service-updates-how-to.md) .
- **Proaktív figyelés**: a vész-helyreállítás és a használat során proaktív módon figyelnie kell a replikált gépek állapotát és állapotát, valamint az infrastruktúra-erőforrásokat.
- Vész- **helyreállítási részletezések**: a vész-helyreállítási gyakorlatokat rendszeresen futtatni kell. Ezek nem befolyásolják az éles környezetet, de segítenek biztosítani, hogy az Azure-ba történő feladatátvétel szükség esetén is megfelelően működjön.



## <a name="gather-capacity-planning-information"></a>Kapacitás-tervezési információk összegyűjtése

Gyűjtsön információkat a helyszíni környezetről, hogy felmérje és megbecsülje a cél (Azure) kapacitás szükségleteit.
- VMware esetén futtassa a VMware virtuális gépek Deployment Plannerét ehhez.
- Fizikai kiszolgálók esetében manuálisan Gyűjtse össze az adatokat.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>A VMware virtuális gépekhez tartozó Deployment Planner futtatása

A Deployment Planner segítségével információkat gyűjthet a VMware helyszíni környezetéről.

- A Deployment Planner futtatása olyan időszakban, amely a virtuális gépek jellemző adatforgalmát jelképezi. Ez pontosabb becsléseket és javaslatokat fog eredményezni.
- Azt javasoljuk, hogy futtassa a Deployment Planner a konfigurációs kiszolgáló gépén, mert a Planner kiszámítja az átviteli sebességet a kiszolgálóról, amelyen fut. [További](site-recovery-vmware-deployment-planner-run.md#get-throughput) információ az átviteli sebesség méréséről.
- Ha még nem rendelkezik beállított konfigurációs kiszolgálóval:
    - Site Recovery-összetevők [áttekintése](vmware-physical-azure-config-process-server-overview.md) .
    - [Hozzon létre egy konfigurációs kiszolgálót](vmware-azure-deploy-configuration-server.md)a Deployment Planner futtatásához.

Ezután futtassa a Plannert a következő módon:

1. A Deployment Planner [megismerése](site-recovery-deployment-planner.md) . A legújabb verziót letöltheti a portálról, vagy [közvetlenül is letöltheti](https://aka.ms/asr-deployment-planner).
2. Tekintse át az [előfeltételeket](site-recovery-deployment-planner.md#prerequisites) és a [legújabb frissítéseket](site-recovery-deployment-planner-history.md) a Deployment Planner, majd [töltse le és bontsa](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) ki az eszközt.
3. [Futtassa a Deployment Planner](site-recovery-vmware-deployment-planner-run.md) a konfigurációs kiszolgálón.
4. [Jelentés létrehozása](site-recovery-vmware-deployment-planner-run.md#generate-report) a becslések és javaslatok összefoglalásához.
5. Elemezze a [jelentés javaslatait](site-recovery-vmware-deployment-planner-analyze-report.md) és a [költségbecslést](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Alapértelmezés szerint az eszköz a profilhoz van konfigurálva, és a jelentést legfeljebb 1000 virtuális gépre állítja elő. Ezt a korlátot módosíthatja úgy, hogy növeli a MaxVMsSupported kulcs értékét a ASRDeploymentPlanner.exe.config fájlban.

## <a name="plan-target-azure-requirements-and-capacity"></a>A cél (Azure) követelményeinek és kapacitásának megtervezése

Az összegyűjtött becslések és javaslatok használatával megtervezheti a cél erőforrásait és kapacitását. Ha a VMware virtuális gépekhez Deployment Planner futtatott, a [jelentéssel kapcsolatos javaslatok](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) közül számos segítséget használhat.

- **Kompatibilis virtuális gépek**: ezt a számot használhatja azon virtuális gépek számának azonosítására, amelyek készen állnak az Azure-ba való vész-helyreállításra. A hálózati sávszélességgel és az Azure-magokkal kapcsolatos javaslatok ezen a számon alapulnak.
- **Szükséges hálózati sávszélesség**: figyelje meg a kompatibilis virtuális gépek különbözeti replikálásához szükséges sávszélességet. 
    - A Planner futtatásakor percek alatt megadhatja a kívánt RPO. A javaslatok a RPO 100%-os és az idő 90%-ának megfelelő sávszélességet mutatják be. 
    - A hálózati sávszélességgel kapcsolatos javaslatok figyelembe veszik a Plannerben ajánlott konfigurációs kiszolgálók és feldolgozható kiszolgálók teljes számához szükséges sávszélességet.
- **Szükséges Azure-magok**: figyelje meg, hogy a cél Azure-régióban milyen magok szükségesek a kompatibilis virtuális gépek száma alapján. Ha nem rendelkezik elegendő maggal, a feladatátvétel Site Recovery nem fogja tudni létrehozni a szükséges Azure-beli virtuális gépeket.
- **Ajánlott virtuálisgép-köteg mérete**: az ajánlott batch-méret a Batch kezdeti replikációjának a 72 órán belüli befejezésére szolgál, a RPO pedig 100%. Az óra értéke módosítható.

Ezekkel a javaslatokkal megtervezheti az Azure-erőforrásokat, a hálózati sávszélességet és a virtuális gépek kötegelt feldolgozását.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure-előfizetések és kvóták megtervezése

Azt szeretnénk, hogy a cél előfizetésben rendelkezésre álló kvóták elegendőek legyenek a feladatátvétel kezeléséhez.

**Feladat** | **Részletek** | **Művelet**
--- | --- | ---
**Magok keresése** | Ha a rendelkezésre álló kvóta magjai nem egyeznek meg a feladatátvétel időpontjában, vagy nem lépik túl a cél teljes számát, a feladatátvétel sikertelen lesz. | VMware virtuális gépek esetén győződjön meg arról, hogy elegendő mag található a cél előfizetésben az Deployment Planner Core javaslat teljesítéséhez.<br/><br/> Fizikai kiszolgálók esetén győződjön meg arról, hogy az Azure magok megfelelnek a manuális becsléseknek.<br/><br/> A kvóták ellenőrzését a Azure Portal > **előfizetésben** kattintson a **használat + kvóták** elemre.<br/><br/> [További](../azure-portal/supportability/resource-manager-core-quotas-request.md) információ a kvóták növeléséről.
**Feladatátvételi korlátok keresése** | A feladatátvételek száma nem haladhatja meg az Site Recovery feladatátvételi korlátot. |  Ha a feladatátvétel túllépi a korlátozásokat, előfizetéseket adhat hozzá, és több előfizetésre is felveheti a feladatokat, vagy növelheti az előfizetések kvótáját. 


### <a name="failover-limits"></a>Feladatátvételi korlátok

A határértékek egy órán belül Site Recovery által támogatott feladatátvételek számát jelzik, feltéve, hogy a gépen három lemez van.

Mit jelent a megfelelés? Azure-beli virtuális gép indításához az Azure-nak egyes illesztőprogramoknak rendszerindítási indítási állapotban kell lennie, és az olyan szolgáltatásokat, mint a DHCP, az automatikus indítást kell beállítani.
- Azok a gépek, amelyek megfelelnek a követelményeknek, már rendelkeznek ezekkel a beállításokkal.
- A Windows rendszert futtató gépek esetében proaktív módon ellenőrizhető a megfelelőség, és szükség esetén megfelelővé teheti azokat. [További információk](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- A Linux rendszerű gépeket csak a feladatátvétel időpontjában kell megfelelni.

**A gép megfelel az Azure-nak?** | **Azure-beli virtuális gépek korlátai (felügyelt lemez feladatátvétele)**
--- | --- 
Yes | 2000
No | 1000

- A korlátok azt feltételezik, hogy az előfizetéshez tartozó cél régióban minimális egyéb feladatok vannak folyamatban.
- Egyes Azure-régiók kisebbek, és lehet, hogy valamivel kisebbek a korlátai.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Az infrastruktúra és a virtuális gépek kapcsolatának megtervezése

Az Azure-ba történő feladatátvétel után a munkaterhelésnek a helyszíni működéshez kell működnie, és lehetővé kell tenni a felhasználók számára az Azure-beli virtuális gépeken futó számítási feladatok elérését.

- [További](site-recovery-active-directory.md#test-failover-considerations) információ a Active Directory vagy a DNS helyszíni infrastruktúrájának az Azure-ba történő feladatátvételéről.
- [További](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) információ az Azure-beli virtuális gépekhez a feladatátvételt követően való kapcsolódás előkészítéséről.



## <a name="plan-for-source-capacity-and-requirements"></a>A források kapacitásának és követelményeinek megtervezése

Fontos, hogy elegendő konfigurációs kiszolgálóval és kibővíthető folyamat-kiszolgálókkal rendelkezzen a kapacitási követelmények kielégítése érdekében. A nagyméretű üzembe helyezés megkezdése után kiindulhat egyetlen konfigurációs kiszolgálóval, és egyetlen kibővíthető folyamat-kiszolgálóval. Az előírt korlátok elérése után további kiszolgálókat adhat hozzá.

>[!NOTE]
> A VMware virtuális gépek esetében a Deployment Planner javaslatokat tesz a szükséges konfigurációs és feldolgozási kiszolgálókkal kapcsolatban. Javasoljuk, hogy az Deployment Planner javaslat követése helyett a következő eljárásokban szereplő táblázatokat használja. 


## <a name="set-up-a-configuration-server"></a>Konfigurációs kiszolgáló beállítása
 
A konfigurációs kiszolgáló kapacitását a replikálást végző gépek száma, és nem az adatforgalom aránya befolyásolja. Ha szeretné kideríteni, hogy szüksége van-e további konfigurációs kiszolgálókra, használja ezeket a meghatározott virtuálisgép-korlátokat.

**CPU** | **Memória** | **Lemez gyorsítótára** | **Replikált számítógép korlátja**
 --- | --- | --- | ---
8 vCPU<br> 2 szoftvercsatorna * 4 mag @ 2,5 GHz | 16 GB | 600 GB | Akár 550 gép<br> Feltételezi, hogy mindegyik gépen három lemez 100 GB.

- Ezek a korlátok egy OVF-sablonnal beállított konfigurációs kiszolgálón alapulnak.
- A korlátok feltételezik, hogy nem használja a konfigurációs kiszolgálón alapértelmezés szerint futó folyamatot.

Ha új konfigurációs kiszolgálót kell felvennie, kövesse az alábbi utasításokat:

- [Állítson be egy konfigurációs kiszolgálót](vmware-azure-deploy-configuration-server.md) a VMWare virtuális gép vész-helyreállítási OVF-sablon használatával.
- [Állítson be manuálisan egy konfigurációs kiszolgálót](physical-azure-set-up-source.md) fizikai kiszolgálókon, illetve olyan VMware-alapú központi telepítések esetén, amelyek nem HASZNÁLHATnak OVF-sablont.

A konfigurációs kiszolgáló beállítása során vegye figyelembe a következőket:

- A konfigurációs kiszolgáló beállításakor fontos figyelembe venni azt az előfizetést és tárat, amelyben a tároló található, mivel ezek a telepítés után nem módosíthatók. Ha módosítania kell a tárolót, a konfigurációs kiszolgálót fel kell vennie a tárolóból, majd újra regisztrálnia kell. Ezzel leállítja a virtuális gépek replikálását a tárolóban.
- Ha több hálózati adapterrel rendelkező konfigurációs kiszolgálót szeretne beállítani, ezt a beállítás során kell végrehajtania. Ezt a konfigurációs kiszolgáló a tárolóban való regisztrálása után nem végezheti el.

## <a name="set-up-a-process-server"></a>Folyamat-kiszolgáló beállítása

A feldolgozási kiszolgáló kapacitását az adatváltozások aránya befolyásolja, nem pedig a replikáláshoz engedélyezett gépek száma.

- Nagyméretű központi telepítések esetén mindig rendelkeznie kell legalább egy kibővíthető folyamat-kiszolgálóval.
- Annak megállapításához, hogy szükség van-e további kiszolgálókra, használja a következő táblázatot.
- Javasoljuk, hogy adjon hozzá egy kiszolgálót a legmagasabb specifikációval. 


**CPU** | **Memória** | **Lemez gyorsítótára** | **Adatforgalom aránya**
 --- | --- | --- | --- 
12 vCPU<br> 2 szoftvercsatorna * 6 mag @ 2,5 GHz | 24 GB | 1 GB | Akár 2 TB naponta

Állítsa be a Process Servert a következőképpen:

1. Tekintse át az [előfeltételeket](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Telepítse a kiszolgálót a [portálon](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)vagy a [parancssorból](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurálja a replikált gépeket az új kiszolgáló használatára. Ha már rendelkezik replikálást végző gépekkel:
    - A folyamat-kiszolgáló teljes számítási feladatait [áthelyezheti](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) az új Process Serverre.
    - Azt is megteheti, hogy adott virtuális gépeket [helyez át](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) az új Process Serverre.



## <a name="enable-large-scale-replication"></a>Nagyméretű replikáció engedélyezése

A kapacitás megtervezése és a szükséges összetevők és infrastruktúra üzembe helyezése után engedélyezze a replikációt nagy számú virtuális gép számára.

1. A gépeket kötegekre rendezheti. Engedélyezheti a virtuális gépek replikálását egy kötegen belül, majd áthelyezheti a következő kötegbe.

    - VMware virtuális gépek esetén a Deployment Planner jelentésben a virtuálisgép- [köteg ajánlott mérete](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) használható.
    - A fizikai gépek esetében javasoljuk, hogy azonosítsa a kötegeket a hasonló méretű és adatmennyiségű, valamint az elérhető hálózati átviteli sebességű gépek alapján. A cél az, hogy a Batch-gépek képesek legyenek a kezdeti replikálásuk befejezésére az adott idő körül.
    
2. Ha a gép lemezének megváltozása magas, vagy meghaladja az üzembe helyezési thePlanner korlátait, áthelyezheti a nem kritikus fontosságú fájlokat, amelyeket nem kell replikálni (például naplózási memóriaképek vagy ideiglenes fájlok) a gépről. A VMware virtuális gépek esetében áthelyezheti ezeket a fájlokat egy különálló lemezre, majd [kihagyhatja a lemezt](vmware-azure-exclude-disk.md) a replikációból.
3. A replikáció engedélyezése előtt győződjön meg arról, hogy a gépek teljesítik a [replikálási követelményeket](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfiguráljon egy replikációs házirendet a [VMWare virtuális gépek](vmware-azure-set-up-replication.md#create-a-policy) vagy a [fizikai kiszolgálók](physical-azure-disaster-recovery.md#create-a-replication-policy)számára.
5. Engedélyezze a replikációt [VMWare virtuális gépek](vmware-azure-enable-replication.md) vagy [fizikai kiszolgálók](physical-azure-disaster-recovery.md#enable-replication)számára. Ez elindítja a kiválasztott gépek kezdeti replikálását.

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése

Miután elindította a virtuális gépek első kötegének replikációját, a következőképpen indíthatja el a központi telepítés figyelését:  

1. Rendeljen hozzá egy vész-helyreállítási rendszergazdát a replikált gépek állapotának figyeléséhez.
2. A replikált elemek és az infrastruktúra [eseményeinek figyelése](site-recovery-monitor-and-troubleshoot.md) .
3. A kibővíthető folyamat-kiszolgálók [állapotának figyelése](vmware-physical-azure-monitor-process-server.md) .
4. Regisztráljon az eseményekre vonatkozó [e-mail-értesítések](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) beszerzéséhez a könnyebb monitorozás érdekében.
5. Végezzen rendszeres vész- [helyreállítási gyakorlatokat](site-recovery-test-failover-to-azure.md), és győződjön meg róla, hogy minden a vártnak megfelelően működik-e.


## <a name="plan-for-large-scale-failovers"></a>Nagy méretű feladatátvételek tervezése

Vészhelyzet esetén előfordulhat, hogy nagy számú gépet/munkaterhelést kell felvennie az Azure-ba. Készítse elő az ilyen típusú eseményt az alábbiak szerint.

A feladatátvételt előre is előkészítheti a következőképpen:

- [Készítse elő az infrastruktúrát és a virtuális gépeket](#plan-infrastructure-and-vm-connectivity) , hogy a számítási feladatok elérhetők legyenek a feladatátvétel után, és hogy a felhasználók hozzáférhessenek az Azure-beli virtuális gépekhez.
- Jegyezze fel a jelen dokumentum korábbi, [feladatátvételi korlátait](#failover-limits) . Győződjön meg arról, hogy a feladatátvételek a fenti korlátok között esnek.
- Futtasson rendszeres vész- [helyreállítási gyakorlatokat](site-recovery-test-failover-to-azure.md). A részletezés a következő műveletekhez nyújt segítséget:
    - A feladatátvétel előtt megkeresheti az üzemelő példányok hiányosságait.
    - Az alkalmazások végpontok közötti RTO becslése.
    - A számítási feladatok végpontok közötti RPO becslése.
    - Azonosítsa az IP-címtartomány ütközéseit.
    - A részletezések futtatásakor azt javasoljuk, hogy ne használjon éles hálózatokat a részletezéshez, és törölje a feladatátvételi teszteket minden részletezés után.

Nagyméretű feladatátvétel futtatásához a következőket javasoljuk:

1. Helyreállítási tervek létrehozása a számítási feladatok feladatátvételéhez.
    - Minden helyreállítási terv akár 100 gép feladatátvételét is elindíthatja.
    - [További](recovery-plan-overview.md) információ a helyreállítási tervekről.
2. Vegyen fel Azure Automation runbook-szkripteket helyreállítási tervekhez, és automatizálja az Azure-beli manuális feladatokat. A tipikus feladatok közé tartozik a terheléselosztó konfigurálása, a DNS frissítése stb. [További információ](site-recovery-runbook-automation.md)
2. A feladatátvétel előtt készítse elő a Windows rendszerű gépeket, hogy azok megfeleljenek az Azure-környezetnek. A [feladatátvételi korlátok](#plan-azure-subscriptions-and-quotas) magasabbak azoknál a gépeknél, amelyek megfelelnek a követelményeknek. [További](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) információ a runbookok.
4.  Indítsa el a feladatátvételt a [Start-AzRecoveryServicesAsrPlannedFailoverJob PowerShell-](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) parancsmaggal és egy helyreállítási tervvel együtt.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Site Recovery monitorozása](site-recovery-monitor-and-troubleshoot.md)
