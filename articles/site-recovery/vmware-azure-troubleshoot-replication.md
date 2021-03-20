---
title: A VMware virtuális gépeknek és fizikai kiszolgálóknak az Azure-ba való vész-helyreállításával kapcsolatos replikációs problémák elhárítása Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő vész-helyreállításával kapcsolatos gyakori replikációs problémákra vonatkozó hibaelhárítási információkat nyújt a Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336205"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációs problémáinak hibaelhárítása

Ez a cikk a helyszíni VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő replikálásával kapcsolatos gyakori problémákat és bizonyos hibákat ismerteti, amelyek a [site Recovery](site-recovery-overview.md)használatával történnek.

## <a name="step-1-monitor-process-server-health"></a>1. lépés: a folyamat-kiszolgáló állapotának figyelése

Site Recovery a [folyamat-kiszolgáló](vmware-physical-azure-config-process-server-overview.md#process-server) segítségével fogadja és optimalizálja a replikált fájlokat, és elküldi azt az Azure-nak.

Azt javasoljuk, hogy a portálon figyelje a folyamat-kiszolgálók állapotát, hogy biztosítsa a csatlakozást és a megfelelő működést, és hogy a replikálás a folyamat-kiszolgálóhoz társított forrásoldali gépek esetében is folyamatban van.

- [További információ a](vmware-physical-azure-monitor-process-server.md) folyamat-kiszolgálók figyeléséről.
- [Az ajánlott eljárások áttekintése](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- A folyamat-kiszolgáló állapotának [hibáinak megoldása](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) .

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2. lépés: kapcsolódási és replikálási problémák elhárítása

A kezdeti és a folyamatos replikálási hibákat gyakran a forráskiszolgáló és a Process Server, illetve a Process Server és az Azure közötti csatlakozási problémák okozzák.

A problémák megoldásához a [kapcsolat és a replikálás hibaelhárítása](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3. lépés: a replikáláshoz nem elérhető forrásoldali gépekkel kapcsolatos hibák megoldása

Ha a Site Recovery használatával szeretné engedélyezni a replikálást, a gép a következő okok egyike miatt nem érhető el:

* **Két azonos példánnyal rendelkező virtuális gép**: Ha a vCenter két virtuális gépnek ugyanaz a példány UUID azonosítója, akkor a konfigurációs kiszolgáló által felderített első virtuális gép megjelenik a Azure Portal. A probléma megoldásához győződjön meg arról, hogy egyetlen példány UUID-je sincs két virtuális gépen. Ez a forgatókönyv általában olyan példányokban látható, ahol a biztonsági mentési virtuális gép aktívvá válik, és bekerül a felderítési rekordba. Tekintse meg a következőt [: Azure site Recovery VMware – Azure: a feloldáshoz használt duplikált vagy elavult bejegyzések törlése](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) .
* **Helytelen vCenter felhasználói hitelesítő adatok**: Ügyeljen arra, hogy a konfigurációs kiszolgáló a OVF sablonnal vagy az egyesített telepítővel való beállításakor a megfelelő vCenter hitelesítő adatokat adja hozzá. A telepítés során hozzáadott hitelesítő adatok ellenőrzéséhez tekintse meg az [automatikus felderítés hitelesítő adatainak módosítása](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)című témakört.
* **vCenter nem megfelelő jogosultságok**: Ha a vCenter eléréséhez megadott engedélyek nem rendelkeznek a szükséges engedélyekkel, előfordulhat, hogy a virtuális gépek felderítése sikertelen. Győződjön meg arról, hogy a [fiók előkészítése automatikus felderítéshez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) című témakörben leírt engedélyek hozzáadódnak a vCenter felhasználói fiókhoz.
* **Azure site Recovery felügyeleti kiszolgálók**: Ha a virtuális gépet felügyeleti kiszolgálóként használják a következő szerepkörök közül egy vagy több alatt – a konfigurációs kiszolgáló/scale-out vagy fő célkiszolgáló, akkor nem fogja tudni kiválasztani a virtuális gépet a portálról. A felügyeleti kiszolgálók nem replikálhatók.
* **Már védett/átadott feladatátvétel Azure site Recovery-szolgáltatásokon keresztül**: Ha a virtuális gép már védett, vagy site Recoveryon keresztül átadja a feladatátvételt, a virtuális gép nem érhető el a portálon a védelem kiválasztásához. Győződjön meg arról, hogy a portálon keresett virtuális gépet más felhasználók vagy más előfizetések már nem védik.
* a **vCenter nincs csatlakoztatva**: Ellenőrizze, hogy a vCenter csatlakoztatott állapotban van-e. Az ellenőrzéshez nyissa meg a Recovery Services-tárolót > Site Recovery infrastruktúra > konfigurációs kiszolgálók > kattintson a megfelelő konfigurációs kiszolgálóra, > egy panel megnyílik a jobb oldalon a társított kiszolgálók részleteivel. Ellenőrizze, hogy a vCenter csatlakoztatva van-e. Ha "nincs csatlakoztatva" állapotú, oldja meg a problémát, majd frissítse a [konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép fel lesz sorolva a portálon.
* **ESXi** kikapcsolva: Ha ESXi-állomás, amelyben a virtuális gép található, kikapcsolt állapotban van, a virtuális gép nem lesz listázva, vagy nem lesz kiválasztható a Azure Portal. Kapcsolja be az ESXi-gazdagépet, [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép fel lesz sorolva a portálon.
* **Újraindítás függőben**: Ha a virtuális gépen újraindításra vár, nem fogja tudni kiválasztani a gépet Azure Portal. Győződjön meg arról, hogy elvégezte a függőben lévő újraindítási tevékenységeket, [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép fel lesz sorolva a portálon.
* Az **IP-cím nem található**: Ha a virtuális gépnek nincs érvényes IP-címe társítva, akkor nem fogja tudni kiválasztani a gépet Azure Portal. Győződjön meg arról, hogy érvényes IP-címet rendel a virtuális géphez, és [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép fel lesz sorolva a portálon.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>A védett virtuális gépek hibáinak megoldása a portálon

A Site Recovery alatt replikált virtuális gépek nem érhetők el a Azure Portalban, ha ismétlődő bejegyzések vannak a rendszerben. Az elavult bejegyzések törlésével és a probléma megoldásával kapcsolatos további információkért tekintse meg a [VMware – Azure Azure site Recovery: duplikált vagy elavult bejegyzések tisztítása](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)című témakört.

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nem érhető el összeomlás-konzisztens helyreállítási pont a virtuális géphez az utolsó "XXX" percben

A leggyakoribb problémák némelyike alább látható

### <a name="initial-replication-issues-error-78169"></a>Kezdeti replikálási problémák [78169-es hiba]

A fentiek alapján biztosíthatja, hogy a kapcsolat, a sávszélesség vagy az idő szinkronizálásával kapcsolatos problémák ne legyenek elérhetők, ügyeljen a következőkre:

- A víruskereső szoftver nem blokkolja a Azure Site Recovery. [További](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) információ a Azure site Recoveryhoz szükséges mappák kizárásáról.

### <a name="source-machines-with-high-churn-error-78188"></a>Nagy forgalmú forrásoldali gépek [Error 78188]

Lehetséges okok:
- A virtuális gép listázott lemezén lévő adatváltozási sebesség (bájt/s) nagyobb, mint a replikációs cél Storage-fióktípus [Azure site Recovery támogatott korlátai](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) .
- Az elváltozási arány hirtelen megszeghető, mivel a feltöltésre váró nagy mennyiségű adatok.

A hiba megoldása érdekében:
- Győződjön meg arról, hogy a célként megadott Storage-fiók típusa (standard vagy prémium) a forrásnál az adatforgalom arányára vonatkozó követelményként van kiépítve.
- Ha már rendelkezik egy prémium szintű felügyelt lemezre (asrseeddisk-típus), győződjön meg arról, hogy a lemez mérete támogatja a megfigyelt adatforgalom mértékét Site Recovery korlátként. Szükség esetén növelheti a asrseeddisk méretét. Kövesse az alábbi lépéseket:
    - Navigáljon az érintett replikált gép lemezek paneljére, és másolja a replika lemez nevét
    - Navigáljon a replika felügyelt lemezéhez
    - Előfordulhat, hogy megjelenik egy szalagcím az Áttekintés panelen, amely azt jelzi, hogy egy SAS URL-cím lett létrehozva. Kattintson erre a szalagcímre, és szakítsa meg az exportálást. Ha nem látja a szalagcímet, hagyja figyelmen kívül ezt a lépést.
    - Amint visszavonják a SAS URL-címét, lépjen a felügyelt lemez konfiguráció paneljére, és növelje a méretet, hogy a Azure Site Recovery támogassa a megfigyelt adatforgalom mértékét a forrásoldali lemezen
- Ha a megfigyelt forgalom ideiglenes, várjon néhány órát, amíg a függőben lévő adatok feltöltése megtörténik, és létre kell hozni a helyreállítási pontokat.
- Ha a lemez olyan nem kritikus fontosságú adatokhoz is tartalmaz, mint például az ideiglenes naplók, az adatok tesztelése stb., érdemes lehet máshová áthelyezni ezeket az adatfájlokat, vagy teljes mértékben kizárni ezt a lemezt
- Ha a probléma továbbra is fennáll, az Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview) segítségével tervezze meg a replikálást.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>A szívverés nélküli forrásoldali gépek [Error 78174]

Ez akkor fordul elő, ha a forrásszámítógépen Azure Site Recovery mobilitási ügynök nem kommunikál a konfigurációs kiszolgálóval (CS).

A probléma megoldásához kövesse az alábbi lépéseket a forrás virtuális gépről a konfigurációs kiszolgálóra való hálózati kapcsolat ellenőrzéséhez:

1. Ellenőrizze, hogy a forrásszámítógép fut-e.
2. Jelentkezzen be a forrásoldali gépre olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
3. Ellenőrizze, hogy a következő szolgáltatások futnak-e, és ha nem indítja újra a szolgáltatásokat:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. A forrásoldali gépen vizsgálja meg a naplófájlokat a következő helyen:

    *C:\Program Files (x86) \Microsoft Azure-hely Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Kiszolgáló feldolgozása szívverés nélkül [806-es hiba]
Ha a Process Server (PS) nem rendelkezik szívveréssel, akkor a következőt kell ellenőriznie:
1. A PS virtuális gép működése folyamatban van
2. A hiba részleteiért olvassa el a következő naplókat a PS-ben:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    és
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Fő célkiszolgáló szívverés nélkül [hiba 78022]

Ez akkor fordul elő, ha Azure Site Recovery mobilitási ügynök a fő célhelyen nem kommunikál a konfigurációs kiszolgálóval.

A probléma megoldásához kövesse az alábbi lépéseket a szolgáltatás állapotának ellenőrzéséhez:

1. Ellenőrizze, hogy a fő célként megadott virtuális gép fut-e.
2. Jelentkezzen be a fő célként megadott virtuális gépre olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
    - Ellenőrizze, hogy a svagents szolgáltatás fut-e. Ha fut, indítsa újra a szolgáltatást.
    - A hiba részleteinek megtekintéséhez keresse meg a naplófájlokat a következő helyen:

        *C:\Program Files (x86) \Microsoft Azure-hely Recovery\agent\svagents \* . log*
3. A fő cél a konfigurációs kiszolgálóval való regisztrálásához navigáljon a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a következő parancsot a parancssorban:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>78144-es AZONOSÍTÓJÚ hiba – nem érhető el alkalmazás-konzisztens helyreállítási pont a virtuális géphez az utolsó "XXX" percben

Fejlesztéseket hajtottak végre a mobilitási ügynök [9,23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher)  &  [9,27](site-recovery-whats-new.md#update-rollup-39) verziójában a VSS telepítési hibák viselkedésének kezeléséhez. Győződjön meg arról, hogy a legújabb verziók a VSS-hibák elhárításával kapcsolatos legjobb útmutatást biztosítják.

A leggyakoribb problémák némelyike alább látható

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1. ok: ismert probléma az SQL Server 2008/2008 R2-ben
**Javítás** : az SQL Server 2008/2008 R2 ismert hibája. Tekintse meg ezt a TUDÁSBÁZISCIKK [Azure site Recovery Agent vagy más, nem összetevővel rendelkező VSS biztonsági mentést, amely a SQL Server 2008 R2-t futtató kiszolgálókon meghiúsul.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2. ok: Azure Site Recovery feladatok meghiúsulnak a SQL Server példányok bármely verzióját üzemeltető kiszolgálókon AUTO_CLOSE-adatbázisok
**Javítási útmutató** [: tudásbáziscikk](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3. ok: ismert probléma SQL Server 2016 és 2017
**Javítási útmutató** [: tudásbáziscikk](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>4. ok: App-Consistency nincs engedélyezve a Linux-kiszolgálókon
**Javítás** : a Linux operációs rendszer Azure site Recovery támogatja az alkalmazások egyéni parancsfájljait az alkalmazás-konzisztencia számára. Az egyéni parancsfájl előtti és utáni beállításokat a Azure Site Recovery mobilitási ügynök fogja használni az alkalmazás-konzisztencia érdekében. Az [alábbi lépéseket](./site-recovery-faq.md#replication) követve engedélyezheti.

### <a name="more-causes-due-to-vss-related-issues"></a>További okok a VSS-vel kapcsolatos problémák miatt:

A további hibaelhárításhoz tekintse meg a forrás gépen található fájlokat a hiba pontos hibakódjának lekéréséhez:

*C:\Program Files (x86) \Microsoft Azure site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Hogyan lehet megtalálni a hibákat a fájlban?
Keresse meg a "vacpError" karakterláncot a vacp. log fájl egy szerkesztőben való megnyitásával

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

A fenti példában a **2147754994** a hibakód, amely az alább látható hibával kapcsolatos információkat mutatja

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>A VSS-író nincs telepítve – 2147221164-es hiba

*Javítás*: az alkalmazás konzisztencia-címkézésének létrehozásához Azure site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja. Telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha a VSS-szolgáltató szolgáltatás nincs telepítve, az alkalmazás konzisztencia-pillanatképének létrehozása sikertelen lesz, mert a 0x80040154 "osztály nincs regisztrálva" érték jelenik meg. </br>
[A VSS-író telepítésével kapcsolatos hibaelhárításról szóló cikkben](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures) olvashat

#### <a name="vss-writer-is-disabled---error-2147943458"></a>A VSS-író le van tiltva – 2147943458-es hiba

**Javítás**: az alkalmazás konzisztencia-címkézésének létrehozásához Azure site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja. Telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha a VSS-szolgáltató szolgáltatás le van tiltva, az alkalmazás konzisztencia-pillanatképének létrehozása meghiúsul a következő hibakóddal: "a megadott szolgáltatás le van tiltva, és nem indítható el (0x80070422)". </br>

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus** értékre van-e állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-szolgáltató NOT_REGISTERED – 2147754756-es hiba

**Javítás**: az alkalmazás konzisztencia-címkézésének létrehozásához Azure site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja.
Ellenőrizze, hogy telepítve van-e a Azure Site Recovery VSS-szolgáltató szolgáltatás. </br>

- Próbálja megismételni a szolgáltató telepítését a következő parancsok használatával:
- Meglévő szolgáltató eltávolítása: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Újratelepítés: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd

Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus** értékre van-e állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

## <a name="error-id-95001---insufficient-permissions-found"></a>95001-es AZONOSÍTÓJÚ hiba – nem megfelelő engedélyek találhatók

Ez a hiba akkor fordul elő, amikor megkísérli engedélyezni a replikációt, és az alkalmazás mappái nem rendelkeznek megfelelő engedélyekkel.

**Javítás**: a probléma megoldásához ellenőrizze, hogy a IUSR-felhasználó rendelkezik-e tulajdonosi szerepkörrel a lent említett mappákhoz.

- *C\ProgramData\Microsoft Azure site Recovery\private*
- A telepítési könyvtár. Ha például a telepítési könyvtár F meghajtó, akkor adja meg a megfelelő engedélyeket a következőhöz:
    - *F:\Program-fájlok (x86) \Microsoft Azure site Recovery\home\svsystems*
- A *\pushinstallsvc* mappája a telepítési könyvtárban. Ha például a telepítési könyvtár F meghajtó, adja meg a megfelelő engedélyeket a következőhöz:
    - *F:\Program-fájlok (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc*
- A *\etc* mappája a telepítési könyvtárban. Ha például a telepítési könyvtár F meghajtó, adja meg a megfelelő engedélyeket a következőhöz:
    - *F:\Program-fájlok (x86) \Microsoft Azure site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- Az alábbi elérési úton található összes elem –
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, tegye fel kérdéseit a [Microsoft Q&a Azure site Recovery kérdését](/answers/topics/azure-site-recovery.html). Aktív közösségünk van, és az egyik mérnökeink segítenek Önnek.
