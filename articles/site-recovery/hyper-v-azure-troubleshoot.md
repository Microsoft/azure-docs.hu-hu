---
title: A Hyper-V vész-helyreállítás hibáinak elhárítása Azure Site Recovery
description: Ismerteti, hogyan lehet elhárítani a Hyper-V-ről az Azure-ba történő helyreállítási problémákat a Azure Site Recovery használatával
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96750802"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>A Hyper-V-ről az Azure-ba végzett replikáció és feladatátvétel hibaelhárítása

Ez a cikk a helyszíni Hyper-V virtuális gépek Azure-ba történő replikálásakor előforduló gyakori problémákat ismerteti [Azure site Recovery](site-recovery-overview.md)használatával.

## <a name="enable-protection-issues"></a>Védelmi problémák engedélyezése

Ha problémákat tapasztal a Hyper-V virtuális gépek védelmének engedélyezésekor, tekintse át a következő javaslatokat:

1. Győződjön meg arról, hogy a Hyper-V-gazdagépek és virtuális gépek megfelelnek az összes [követelménynek és előfeltételnek](hyper-v-azure-support-matrix.md).
2. Ha a Hyper-V-kiszolgálók System Center Virtual Machine Manager (VMM) felhőkben találhatók, ellenőrizze, hogy előkészítette-e a [VMM-kiszolgálót](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Győződjön meg arról, hogy a Hyper-V Virtuálisgép-kezelő szolgáltatás a Hyper-V-gazdagépeken fut.
4. Keressen olyan problémákat, amelyek megjelennek a Hyper-V-VMMS\Admin a virtuális gépre való bejelentkezéskor. Ez a napló az **alkalmazások és szolgáltatások naplójában** található a  >  **Microsoft**  >  **Windowsban**.
5. A vendég virtuális gépen ellenőrizze, hogy a WMI engedélyezve van-e és elérhető-e.
   - [Ismerje meg az](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) alapszintű WMI-tesztelést.
   - [Hibakeresés](/windows/win32/wmisdk/wmi-troubleshooting) WMI.
   - WMI-parancsfájlokkal és-szolgáltatásokkal kapcsolatos problémák [elhárítása](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) .
6. A vendég virtuális gépen ellenőrizze, hogy az integrációs szolgáltatások legújabb verziója fut-e.
    - [Győződjön meg arról,](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) hogy a legújabb verzióval rendelkezik.
    - [Tartsa](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) meg Az integrációs szolgáltatások naprakészek.

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>A védelem nem engedélyezhető, mert a virtuális gép nem nagyon elérhető (hibakód: 70094)

Amikor engedélyezi egy gép replikálását, és hibát észlel, hogy a replikáció nem engedélyezhető, mert a gép nem érhető el, a probléma megoldásához próbálja meg az alábbi lépéseket:

- Indítsa újra a VMM szolgáltatást a VMM-kiszolgálón.
- Távolítsa el a virtuális gépet a fürtből, és adja hozzá újra.

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>A VSS-író NTDS-írója a 11. állapottal meghiúsult, és az író-specifikus hibakód 0x800423F4

A replikáció engedélyezésekor előfordulhat, hogy egy hibaüzenet jelenik meg, amely tájékoztatja, hogy nem sikerült engedélyezni a replikációt az AST NTDS-ben. A probléma egyik lehetséges oka, hogy a virtuális gép operációs rendszere a Windows Server 2012, és nem Windows Server 2012 R2. A probléma megoldásához próbálkozzon az alábbi lépésekkel:

- Frissítsen a Windows Server R2-re a 4072650-es verzióra.
- Győződjön meg arról, hogy a Hyper-V-gazdagép is Windows 2016 vagy újabb.

## <a name="replication-issues"></a>Replikációs problémák

A kezdeti és a folyamatban lévő replikációval kapcsolatos problémákat a következő módon háríthatja el:

1. Győződjön meg arról, hogy a Site Recovery szolgáltatások [legújabb verzióját](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) futtatja.
2. Annak ellenőrzése, hogy a replikáció szüneteltetve van-e:
   - A Hyper-V kezelőjének konzolján keresse meg a virtuális gép állapota állapotot.
   - Ha ez kritikus fontosságú, kattintson a jobb gombbal a virtuális gépre > **replikáció**  >  **nézet replikálási állapota** elemre.
   - Ha a replikálás szüneteltetve van, kattintson a **replikáció folytatása** gombra.
3. Győződjön meg arról, hogy a szükséges szolgáltatások futnak. Ha nem, indítsa újra őket.
    - Ha VMM nélkül replikálja a Hyper-V-t, ellenőrizze, hogy a szolgáltatások futnak-e a Hyper-V-gazdagépen:
        - Virtuálisgép-kezelő szolgáltatás
        - Microsoft Azure Recovery Services-ügynök szolgáltatás
        - Microsoft Azure Site Recovery szolgáltatás
        - WMI Provider Host szolgáltatás
    - Ha a VMM-ben végzi a replikálást a környezetben, ellenőrizze, hogy a szolgáltatások futnak-e:
        - A Hyper-V gazdagépen győződjön meg arról, hogy fut a Virtuálisgép-kezelő szolgáltatás, a Microsoft Azure Recovery Services ügynök és a WMI-szolgáltató gazda szolgáltatása.
        - A VMM-kiszolgálón ellenőrizze, hogy fut-e a System Center Virtual Machine Manager szolgáltatás.
4. Ellenőrizze a Hyper-V-kiszolgáló és az Azure közti kapcsolatot. A kapcsolat kereséséhez nyissa meg a Feladatkezelő eszközt a Hyper V-gazdagépen. A **teljesítmény** lapon kattintson a **erőforrás-figyelő megnyitása** lehetőségre. A **hálózat** lapon > **folyamat hálózati tevékenységgel** területen győződjön meg arról, hogy cbengine.exe aktívan küld-e nagy mennyiségű (MBS) adatokat.
5. Ellenőrizze, hogy a Hyper-V-gazdagépek csatlakozni tudnak-e az Azure Storage-blob URL-címéhez. Ellenőrizze, hogy a gazdagépek csatlakozhatnak-e, majd jelölje be a **cbengine.exe**. A **TCP-kapcsolatok** megtekintése a gazdagép és az Azure Storage blob közötti kapcsolat ellenőrzéséhez.
6. Tekintse át a teljesítménnyel kapcsolatos problémákat az alább leírtak szerint.
    
### <a name="performance-issues"></a>Teljesítményproblémák

A hálózati sávszélesség korlátozásai befolyásolhatják a replikálást. Hibák elhárítása az alábbiak szerint:

1. [Ellenőrizze](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) , hogy vannak-e sávszélesség-vagy szabályozási korlátozások a környezetben.
2. Futtassa a [Deployment Planner Profilert](hyper-v-deployment-planner-run.md).
3. A Profiler futtatása után kövesse a [sávszélességgel](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és a [tárolással](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) kapcsolatos javaslatokat.
4. Az adatváltozások [korlátozásának](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)ellenõrzése. Ha magas adatváltozást tapasztal egy virtuális gépen, tegye a következőket:
   - Ellenőrizze, hogy a virtuális gép meg van-e jelölve újraszinkronizálásra.
   - A forgalom forrásának vizsgálatához kövesse az [alábbi lépéseket](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) .
   - A forgalom akkor fordulhat elő, ha a HRL-naplófájlok mérete meghaladja a rendelkezésre álló lemezterület 50%-át. Ha ez a probléma, több tárolóhelyet kell kiépíteni minden olyan virtuális géphez, amelyen a probléma bekövetkezik.
   - Győződjön meg arról, hogy a replikáció nincs szüneteltetve. Ha igen, akkor folytatja a HRL-fájl módosításainak írását, ami hozzájárulhat a méret növeléséhez.
 

## <a name="critical-replication-state-issues"></a>Kritikus replikációs állapottal kapcsolatos problémák

1. A replikálás állapotának ellenőrzéséhez kapcsolódjon a helyszíni Hyper-V Manager-konzolhoz, válassza ki a virtuális gépet, és ellenőrizze az állapotot.

    ![Replikálás állapota](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. A részletek megtekintéséhez kattintson a **replikációs állapot megtekintése** elemre:

    - Ha a replikálás szüneteltetve van, kattintson a jobb gombbal a virtuális gépre **, > a replikálás**  >  **folytatását**.
    - Ha egy Site Recoveryban konfigurált Hyper-V-gazdagépen lévő virtuális gép egy másik Hyper-V-gazdagépre, vagy egy önálló gépre kerül át, a virtuális gép replikálását nem érinti. Csak győződjön meg róla, hogy az új Hyper-V-gazdagép megfelel az összes előfeltételnek, és Site Recovery van konfigurálva.

## <a name="app-consistent-snapshot-issues"></a>Alkalmazás-konzisztens pillanatképekkel kapcsolatos problémák

Az alkalmazás-konzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időponthoz tartozó pillanatképe. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen lévő alkalmazások konzisztens állapotban legyenek a pillanatkép készítésekor.  Ez a szakasz az esetlegesen felmerülő gyakori problémákat ismerteti.

### <a name="vss-failing-inside-the-vm"></a>VSS-hiba a virtuális gépen belül

1. Győződjön meg arról, hogy az integrációs szolgáltatások legújabb verziója telepítve van és fut.  Ellenőrizze, hogy elérhető-e frissítés a következő parancs futtatásával egy rendszergazda jogú PowerShell-parancssorból a Hyper-V-gazdagépen: **Get-VM | válassza a név, állapot, IntegrationServicesState** lehetőséget.
2. Győződjön meg arról, hogy a VSS-szolgáltatások futnak és kifogástalan állapotban vannak:
   - A szolgáltatások vizsgálatához jelentkezzen be a vendég virtuális gépre. Ezután nyisson meg egy rendszergazdai parancssort, és futtassa a következő parancsokat annak megadásához, hogy az összes VSS-író kifogástalan állapotú-e.
       - **Vssadmin-lista írói**
       - **Vssadmin-lista árnyékai**
       - **Vssadmin-szolgáltatók**
   - Keresse meg a kimenetet. Ha az írók hibás állapotban vannak, tegye a következőket:
       - Az alkalmazás eseménynaplójában keresse meg a virtuális gépen a VSS-művelet hibáit.
   - Próbálja meg újraindítani a sikertelen író által társított szolgáltatásokat:
     - Kötet árnyékmásolata
       - Azure Site Recovery VSS-szolgáltató
   - Ezt követően várjon néhány órát, és ellenőrizze, hogy az alkalmazás-konzisztens Pillanatképek generálása sikeresen megtörtént-e.
   - Végső megoldásként próbálja meg újraindítani a virtuális gépet. Ez feloldható lehet a nem válaszoló állapotú szolgáltatások feloldása.
3. Győződjön meg arról, hogy nincsenek dinamikus lemezek a virtuális gépen. Ez az alkalmazás-konzisztens Pillanatképek esetében nem támogatott. A Lemezkezelés (diskmgmt. msc) segítségével ellenőrizhető.

    ![Dinamikus lemez](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Győződjön meg arról, hogy nincs iSCSI-lemez csatlakoztatva a virtuális géphez. Ez a funkció nem támogatott.
5. Győződjön meg arról, hogy a biztonsági mentési szolgáltatás engedélyezve van. Ellenőrizze, hogy engedélyezve van-e a **Hyper-V beállítások**  >  **integrációs szolgáltatásaiban**.
6. Győződjön meg arról, hogy a VSS-pillanatképeket tartalmazó alkalmazások nem ütköznek. Ha egyszerre több alkalmazás is próbálkozik a VSS-Pillanatképek elvégzésével, az ütközések előfordulhatnak. Ha például egy biztonságimásolat-készítő alkalmazás VSS-pillanatképeket készít, amikor a replikációs házirend Site Recovery ütemezi a pillanatkép készítését.   
7. Ellenőrizze, hogy a virtuális gép magas adatváltozási arányt tapasztal-e:
    - A vendég virtuális gépek napi adatváltozási arányának mérésére a Hyper-V-gazdagépen lévő teljesítményszámlálók használatával van lehetőség. Az adatváltozások arányának méréséhez engedélyezze a következő számlálót. Az érték mintájának összesítése a virtuálisgép-lemezeken 5-15 percen át, a virtuális gép forgalmának beszerzése érdekében.
        - Kategória: "Hyper-V virtuális tárolóeszköz"
        - Számláló: "írási sebesség (bájt/mp)"</br>
        - Ez az adatváltozási arány magas szinten növekszik vagy marad, attól függően, hogy milyen elfoglalt a virtuális gép vagy alkalmazásai.
        - Az átlagos forrás lemez adatváltozása 2 MB/s a standard szintű tároláshoz a Site Recovery számára. [További információ](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Emellett [ellenőrizheti a tárolási skálázhatósági célokat](../storage/common/scalability-targets-standard-account.md)is.
8. Győződjön meg arról, hogy ha Linux-alapú kiszolgálót használ, akkor engedélyezte az alkalmazás-konzisztencia használatát. [További információ](./site-recovery-faq.md#replication)
9. Futtassa a [Deployment Planner](hyper-v-deployment-planner-run.md).
10. Tekintse át a [hálózati](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)javaslatokat.


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS-hiba a Hyper-V-gazdagépen belül

1. Tekintse meg a VSS-hibák és-javaslatok eseménynaplóit:
    - A Hyper-v-gazdagépen nyissa meg a Hyper-v rendszergazdai eseménynaplóját **Eseménynapló**  >  **alkalmazások és szolgáltatások naplófájlokban** a  >  **Microsoft**  >  **Windows**  >  **Hyper-v**  >  **rendszergazda**.
    - Ellenőrizze, hogy vannak-e olyan események, amelyek az alkalmazással konzisztens pillanatkép-hibákat jeleznek.
    - Tipikus hiba: "a Hyper-V nem tudta létrehozni a VSS pillanatkép-készletet a (z)" XYZ "virtuális géphez: az író nem átmeneti hibát észlelt. A VSS szolgáltatás újraindítása megoldhatja a problémákat, ha a szolgáltatás nem válaszol. "

2. Ha VSS-pillanatképeket szeretne létrehozni a virtuális géphez, győződjön meg arról, hogy a Hyper-V integrációs szolgáltatások telepítve vannak a virtuális gépen, és hogy a biztonsági mentési (VSS) integrációs szolgáltatás engedélyezve van.
    - Győződjön meg arról, hogy az Integration Services VSS szolgáltatás/démonok futnak a vendégen, és **OK** állapotban vannak.
    - Ezt egy emelt szintű PowerShell-munkamenetből is megtekintheti a Hyper-V-gazdagépen, a **Get-VMIntegrationService-VMName \<VMName> -Name VSS** paranccsal pedig bejelentkezhet a vendég virtuális gépre. [További információk](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Győződjön meg arról, hogy a virtuális gépen a biztonsági mentési/VSS-integrációs szolgáltatások futnak és kifogástalan állapotban vannak. Ha nem, indítsa újra ezeket a szolgáltatásokat és a Hyper-v Kötet árnyékmásolata kérelmező szolgáltatást a Hyper-V-gazdagép kiszolgálóján.

### <a name="common-errors"></a>Gyakori hibák

**Hibakód** | **Üzenetet** | **Részletek**
--- | --- | ---
**0x800700EA** | "A Hyper-V nem tudta létrehozni a VSS pillanatkép-készletet a virtuális géphez: további adatok érhetők el. (0x800700EA). A VSS-pillanatkép-készlet létrehozása sikertelen lehet, ha a biztonsági mentési művelet folyamatban van.<br/><br/> A virtuális gép replikálási művelete sikertelen volt: További információ érhető el. " | Ellenőrizze, hogy a virtuális gépen engedélyezve van-e a dinamikus lemez. Ez a funkció nem támogatott.
**0x80070032** | "A Hyper-V Kötet árnyékmásolata kérelmező nem tudott kapcsolódni a (z) <./VMname> virtuális géphez, mert a verzió nem egyezik a Hyper-V által várt verzióval | Ellenőrizze, hogy telepítve vannak-e a legújabb Windows-frissítések.<br/><br/> [Frissítsen](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) az integrációs szolgáltatások legújabb verziójára.



## <a name="collect-replication-logs"></a>Replikációs naplók gyűjtése

A rendszer minden Hyper-V replikációs eseményt naplóz a Hyper-V-VMMS\Admin-naplóban, amely az **alkalmazások és szolgáltatások naplóban** található a  >  **Microsoft**  >  **Windowsban**. Emellett engedélyezheti a Hyper-V Virtuálisgép-kezelő szolgáltatás analitikus naplóját is, az alábbiak szerint:

1. Az elemzési és hibakeresési naplók megtekinthetők legyenek a Eseménynaplóban. A naplók elérhetővé tételéhez **kattintson a** Eseménynapló az  >  **elemzési és hibakeresési naplók megjelenítése** elemre. Az analitikai napló a **Hyper-V-vmms** alatt jelenik meg.
2. A **műveletek** ablaktáblán kattintson a **napló engedélyezése** lehetőségre. 

    ![Napló engedélyezése](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Az engedélyezése után a **Teljesítményfigyelőben** az **Adatgyűjtő-csoportosítók** esemény- **nyomkövetési munkamenete** jelenik meg. 
4. Az összegyűjtött adatok megtekintéséhez állítsa le a nyomkövetési munkamenetet a napló letiltásával. Ezután mentse a naplót, majd nyissa meg újra a Eseménynaplóban, vagy használjon más eszközöket, hogy szükség szerint alakítsa át.


### <a name="event-log-locations"></a>Eseménynapló helyei

**Eseménynapló** | **Részletek** |
--- | ---
**Alkalmazások és szolgáltatások naplói/Microsoft/VirtualMachineManager/Server/admin** (VMM-kiszolgáló) | Naplók a VMM kapcsolatos problémák elhárításához.
**Alkalmazások és szolgáltatások naplói/MicrosoftAzureRecoveryServices/Replication** (Hyper-V-gazdagép) | Naplók Microsoft Azure Recovery Services ügynökkel kapcsolatos problémák elhárításához. 
**Alkalmazások és szolgáltatások naplói/Microsoft/Azure site Recovery/Provider/Operational** (Hyper-V-gazdagép)| A Microsoft Azure Site Recovery szolgáltatással kapcsolatos problémák elhárítására szolgáló naplók.
**Alkalmazások és szolgáltatások naplói/Microsoft/Windows/Hyper-v-vmms/admin** (Hyper-v-gazdagép) | A Hyper-V virtuális gépek kezelésével kapcsolatos problémák elhárítására szolgáló naplók.

### <a name="log-collection-for-advanced-troubleshooting"></a>A speciális hibaelhárítási naplók gyűjteménye

Ezek az eszközök a speciális hibaelhárításhoz nyújtanak segítséget:

-   A VMM esetében hajtsa végre Site Recovery naplózási gyűjteményt a [support Diagnostics platform (SDP) eszköz](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)használatával.
-   A Hyper-V VMM nélkül [töltse le ezt az eszközt](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), és futtassa a Hyper-v-gazdagépen a naplók összegyűjtéséhez.