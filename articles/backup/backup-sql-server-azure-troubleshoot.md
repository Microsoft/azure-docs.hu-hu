---
title: SQL Server adatbázis biztonsági mentésének hibáinak megoldása
description: Hibaelhárítási információk az Azure-beli virtuális gépeken futó SQL Server adatbázisok biztonsági mentéséhez Azure Backup-mel.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 1e4ee2bdcd0826b655aa71d83674ff1e0c06a8cb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549898"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>SQL Server adatbázis biztonsági mentésének hibáinak megoldása Azure Backup használatával

Ez a cikk az Azure Virtual Machines szolgáltatásban futó SQL Server adatbázisok hibaelhárítási információit tartalmazza.

További információ a biztonsági mentési folyamatról és a korlátozásokról: [SQL Server biztonsági mentés az Azure-beli virtuális gépeken](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>Engedélyek SQL Server

Ha egy SQL Server adatbázis védelmét szeretné konfigurálni egy virtuális gépen, telepítenie kell a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Ha a **UserErrorSQLNoSysadminMembership** hibaüzenetet kap, az azt jelenti, hogy az SQL Server-példány nem rendelkezik a szükséges biztonsági mentési engedélyekkel. A hiba elhárításához kövesse a [virtuális gép engedélyeinek beállítása](backup-azure-sql-database.md#set-vm-permissions)című témakör lépéseit.

## <a name="troubleshoot-discover-and-configure-issues"></a>Felderítési és konfigurálási problémák hibaelhárítása

Recovery Services-tároló létrehozása és konfigurálása után az adatbázisok felfedése és a biztonsági mentés konfigurálása két lépésből álló folyamat.<br>

![Biztonsági mentési cél – SQL Server az Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql.png)

A biztonsági mentési konfiguráció során, ha az SQL-alapú virtuális gép és annak példányai nem láthatók a **virtuális gépek felderítési** adatbázisaiban, és a **biztonsági mentés konfigurálása** (lásd a fenti képen), ügyeljen a következőre:

### <a name="step-1-discovery-dbs-in-vms"></a>1. lépés: a virtuális gépeken futó felderítési adatbázisok

- Ha a virtuális gép nem szerepel a felderített virtuális gépek listájában, és nem regisztrált az SQL Backup szolgáltatásban egy másik tárolóban, kövesse a [felderítési SQL Server biztonsági mentési](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) lépéseit.

### <a name="step-2-configure-backup"></a>2. lépés: biztonsági mentés konfigurálása

- Ha az a tároló, amelyben az SQL virtuális gép regisztrálva van az adatbázisok védelméhez használt tárban, kövesse a [biztonsági mentés konfigurálása](./backup-sql-server-database-azure-vms.md#configure-backup) lépéseit.

Ha az SQL virtuális gépet regisztrálni kell az új tárolóban, akkor azt törölni kell a régi tárból.  Egy SQL virtuális gép tárolóból való regisztrációjának megszüntetéséhez az összes védett adatforrást le kell állítani, és ezután törölheti a biztonsági másolatok adatait. A biztonsági másolatba mentett adathalmazok törlése romboló művelet.  Miután áttekintette az összes óvintézkedést az SQL virtuális gép regisztrációjának megszüntetéséhez, regisztrálja ugyanezt a virtuális gépet egy új tárolóval, és próbálkozzon újra a biztonsági mentési művelettel.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Biztonsági mentési és helyreállítási problémák elhárítása  

Időnként véletlenszerű hibák fordulnak elő a biztonsági mentési és visszaállítási műveletekben, vagy a műveletek elakadnak. Ennek oka lehet a virtuális gépen futó víruskereső program. Ajánlott eljárásként a következő lépéseket javasoljuk:

1. A következő mappák kizárása a víruskeresésből:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    Cserélje le a `C:\` betűt a *rendszermeghajtó* betűjelére.

1. Zárja ki a virtuális gépen belül futó következő három folyamatot víruskereső vizsgálatból:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. Az SQL Emellett néhány útmutatót is biztosít a víruskereső programokkal való együttműködéshez. További részletekért tekintse meg [ezt a cikket](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) .

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Hibás példány egy olyan virtuális gépen, amelyen több SQL Server példány található

Az SQL virtuális gépekre csak akkor állítható vissza, ha a virtuális gépen futó összes SQL-példány kifogástalan állapotú. Ha egy vagy több példány "hibás", a virtuális gép nem jelenik meg visszaállítási célként. Így előfordulhat, hogy ez lehet az oka annak, hogy egy többpéldányos virtuális gép ne jelenjen meg a "kiszolgáló" legördülő menüben a visszaállítási művelet során.

A virtuális gépen található összes SQL-példány biztonsági mentési készültségét a **biztonsági mentés konfigurálása** területen ellenőrizheti:

![Biztonsági mentés készültségének ellenőrzése](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Ha a kifogástalan SQL-példányok visszaállítását szeretné elindítani, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az SQL virtuális gépre, és nyissa meg a következőt: `C:\Program Files\Azure Workload Backup\bin` .
1. Hozzon létre egy nevű JSON `ExtensionSettingsOverrides.json` -fájlt (ha még nem létezik). Ha a fájl már megtalálható a virtuális gépen, folytassa a használatát.
1. Adja hozzá a következő tartalmat a JSON-fájlhoz, és mentse a fájlt:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Indítsa el az adatbázis **újrafelderítése** műveletet az érintett kiszolgálón a Azure Portal (a biztonsági mentés felkészültségének helyét is megtekintheti). A virtuális gép a visszaállítási műveletek céljaként fog megjelenni.

    ![Adatbázisok újbóli felderítése](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. A visszaállítási művelet befejezése után távolítsa el a *whitelistedInstancesForInquiry* bejegyzést a ExtensionSettingsOverrides.jsfájlból.

## <a name="error-messages"></a>Hibaüzenetek

### <a name="backup-type-unsupported"></a>A biztonsági mentés típusa nem támogatott

| Súlyosság | Leírás | Lehetséges okok | Javasolt művelet |
|---|---|---|---|
| Figyelmeztetés | Az adatbázis jelenlegi beállításai nem támogatják a társított szabályzatban található egyes biztonsági mentési típusokat. | <li>A Master adatbázison csak a teljes adatbázis biztonsági mentési művelete hajtható végre. A különbözeti biztonsági mentés és a tranzakciónapló biztonsági mentése nem lehetséges. </li> <li>Az egyszerű helyreállítási modellben lévő adatbázisok nem teszik lehetővé a tranzakciónaplók biztonsági mentését.</li> | Módosítsa az adatbázis beállításait, hogy a szabályzat összes biztonsági mentési típusa támogatott legyen. Vagy módosítsa úgy a jelenlegi szabályzatot, hogy csak a támogatott biztonsági mentési típusokat tartalmazza. Ellenkező esetben a rendszer kihagyja a nem támogatott biztonsági mentési típusokat az ütemezett biztonsági mentés során, vagy a biztonsági mentési feladat sikertelen lesz az igény szerinti biztonsági mentés során.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ez az SQL-adatbázis nem támogatja a kért típusú biztonsági mentést. | Akkor következik be, amikor az adatbázis-helyreállítási modell nem engedélyezi a kért biztonsági mentési típust. A hiba a következő helyzetekben fordulhat elő: <br/><ul><li>Az egyszerű helyreállítási modellt használó adatbázisok nem engedélyezik a napló biztonsági mentését.</li><li>A Master adatbázis esetében nem engedélyezett a különbözeti és a napló biztonsági mentése.</li></ul>További részletekért tekintse meg a [SQL Server Recovery models](/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációját. | Ha a napló biztonsági mentése nem sikerül az adatbázishoz az egyszerű helyreállítási modellben, próbálja ki a következő lehetőségek egyikét:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a naplók biztonsági mentését.</li><li>A [SQL Server dokumentációjának](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) használatával megváltoztathatja az adatbázis-helyreállítási modellt teljes vagy tömegesen naplózott értékre. </li><li> Ha nem szeretné módosítani a helyreállítási modellt, és rendelkezik egy szabványos házirenddel a nem módosítható adatbázisok biztonsági mentésére, hagyja figyelmen kívül a hibát. A teljes és a különbözeti biztonsági mentések ütemezés szerint fognak működni. A rendszer kihagyja a naplók biztonsági másolatait, ami ebben az esetben várható.</li></ul>Ha ez egy Master adatbázis, és a különbözeti vagy a napló biztonsági mentését konfigurálta, használja a következő lépések egyikét:<ul><li>A portál használatával módosíthatja a biztonsági mentési szabályzat ütemezését a Master adatbázisra vonatkozóan, a teljes értékre.</li><li>Ha olyan általános házirenddel rendelkezik, amely nem módosítható több adatbázis biztonsági mentésére van beállítva, hagyja figyelmen kívül a hibát. A teljes biztonsági mentés ütemterv szerint fog működni. A különbözeti vagy a napló biztonsági mentése nem történik meg, ami ebben az esetben várható.</li></ul> |
| A művelet megszakadt, mert egy ütköző művelet már futott ugyanazon az adatbázison. | A párhuzamosan futó [biztonsági mentési és visszaállítási korlátozásokról a blogbejegyzésben](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) talál további információt.| [A biztonsági mentési feladatok figyeléséhez használja a SQL Server Management Studio (SSMS)](manage-monitor-sql-database-backup.md)szolgáltatást. Az ütköző művelet sikertelensége után indítsa újra a műveletet.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem létezik. | Az adatbázist törölték vagy átnevezték. | Ellenőrizze, hogy a rendszer véletlenül törölte vagy átnevezte-e az adatbázist.<br/><br/> Ha az adatbázist véletlenül törölték, a biztonsági mentések folytatásához állítsa vissza az adatbázist az eredeti helyére.<br/><br/> Ha törölte az adatbázist, és nem igényel jövőbeli biztonsági mentést, akkor a Recovery Services-tárolóban válassza a biztonsági mentés **leállítása** a biztonsági **mentési adat megőrzése** vagy a **biztonsági mentési adat törlése** lehetőséget. További információ: a [biztonsági másolatok SQL Server adatbázisainak kezelése és figyelése](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A naplólánc megszakadt. | Az adatbázis vagy a virtuális gép biztonsági mentést készít egy másik biztonsági mentési megoldással, amely levágja a naplózási láncot.|<ul><li>Ellenőrizze, hogy van-e használatban másik biztonsági mentési megoldás vagy parancsfájl. Ha igen, állítsa le a másik biztonsági mentési megoldást. </li><li>Ha a biztonsági másolat egy igény szerinti napló biztonsági másolata volt, indítson el egy teljes biztonsági mentést egy új naplózási lánc elindításához. Ütemezett naplók biztonsági mentése esetén nincs szükség beavatkozásra, mert a Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentést a probléma megoldásához.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Azure Backup nem tud csatlakozni az SQL-példányhoz. | Azure Backup nem tud csatlakozni a SQL Server példányhoz. | A Azure Portali hiba menüben található további részletek segítségével Szűkítse le a kiváltó okokat. A hiba elhárításához tekintse meg az [SQL biztonsági mentéssel kapcsolatos hibaelhárítást](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) .<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem engedélyezik a távoli kapcsolatokat, módosítsa a beállításokat. A beállítások módosításával kapcsolatos információkért tekintse meg a következő cikkeket:<ul><li>[MSSQLSERVER_ – 1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Ha bejelentkezési problémák léptek fel, a következő hivatkozásokat használhatja a kijavításához:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az adatforrás első teljes biztonsági mentése hiányzik. | Hiányzik az adatbázis teljes biztonsági mentése. A log és a különbözeti biztonsági mentések a szülők teljes biztonsági mentésére szolgálnak, ezért ügyeljen arra, hogy teljes biztonsági mentést készítsen a különböző biztonsági mentések elindítása előtt. | Igény szerinti teljes biztonsági mentés indítása.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A biztonsági mentés nem végezhető el, mert az adatforrás tranzakciónaplója megtelt. | Az adatbázis tranzakciós naplózási területe megtelt. | A probléma megoldásához tekintse meg a [SQL Server dokumentációját](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Már létezik ilyen nevű adatbázis a célhelyen | A cél-visszaállítás célhelyének már van azonos nevű adatbázisa.  | <ul><li>Módosítsa a céladatbázis nevét.</li><li>Vagy a visszaállítás lapon használja a kényszerített felülírás lehetőséget.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A visszaállítás nem sikerült, mert az adatbázist nem lehetett offline állapotba helyezni. | A visszaállítás során a célként megadott adatbázist offline állapotba kell állítani. Azure Backup nem tud offline állapotba hozni ezeket az adatfájlokat. | A Azure Portali hiba menüben található további részletek segítségével Szűkítse le a kiváltó okokat. További információért tekintse meg [az SQL Server dokumentációját](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Hibaüzenet |Lehetséges okok  |Javasolt művelet  |
|---------|---------|---------|
|Bemeneti/kimeneti hiba történt a művelet során. Ellenőrizze, hogy van-e általános IO-hiba a virtuális gépen.   |   Hozzáférési engedélyek vagy lemezterület-korlátozások a célhelyen.       |  A virtuális gépen előforduló általános IO-hibák keresése. Győződjön meg arról, hogy a cél meghajtó/hálózati megosztás a gépen: <li> olvasási/írási engedéllyel rendelkezik a fiók NT AUTHORITY\SYSTEM a gépen. <li> elegendő lemezterülettel rendelkezik a művelet sikeres befejezéséhez.<br> További információ: [visszaállítás fájlként](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem található az ujjlenyomattal rendelkező kiszolgálói tanúsítvány a célhelyen. | A célként megadott példány főadatbázisa nem rendelkezik érvényes titkosítási ujjlenyomattal. | Importálja a forrás példányon használt érvényes tanúsítvány-ujjlenyomatot a cél példányra. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A helyreállításhoz használt naplóalapú biztonsági mentés tömegesen naplózott módosításokat tartalmaz. Nem használható az SQL-irányelvek alapján tetszőleges időpontban történő leállításra. | Ha egy adatbázis tömegesen naplózott helyreállítási módban van, a tömegesen naplózott tranzakció és a következő naplózási tranzakció közötti adatmennyiség nem állítható helyre. | Válasszon egy másik időpontot a helyreállításhoz. [További információk](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Always On rendelkezésre állási csoport biztonsági mentési beállítása nem teljesíthető, mert a rendelkezésre állási csoport egyes csomópontjai nincsenek regisztrálva. | A biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva vagy nem érhetők el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági másolatainak végrehajtásához szükséges összes csomópont regisztrálva és kifogástalan állapotban van, majd próbálja megismételni a műveletet.</li><li>Módosítsa a SQL Server always on rendelkezésre állási csoport biztonsági mentési beállításait.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Server rendszerű virtuális gép le van állítva, és nem érhető el Azure Backup szolgáltatás számára. | A virtuális gép le van állítva. | Győződjön meg arról, hogy a SQL Server példány fut. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Azure Backup szolgáltatás az Azure VM Guest agentet használja a biztonsági mentéshez, de a vendég ügynök nem érhető el a célkiszolgálón. | A vendég ügynök nincs engedélyezve vagy sérült. | [Telepítse manuálisan a virtuális gép vendég ügynökét](../virtual-machines/extensions/agent-windows.md) . |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az automatikus védelem szándéka el lett távolítva vagy nem érvényes. | Ha engedélyezi az automatikus védelmet egy SQL Server-példányon, **konfigurálja a biztonsági mentési** feladatok futtatását az adott példány összes adatbázisához. Ha letiltja az automatikus védelmet a feladatok futása közben, akkor a **folyamatban** lévő feladatok megszakadnak ezzel a hibakódtal. | Engedélyezze újra az automatikus védelmet az összes fennmaradó adatbázis védelme érdekében. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet le van tiltva, mert elérte a 24 órán belül engedélyezett műveletek számának korlátját. | Ha elérte a maximális megengedett korlátot egy 24 órás span művelethez, ez a hiba jelenik meg. <br> Például: ha elérte a naponta indítható biztonsági mentési feladatok konfigurálásának korlátját, és a biztonsági mentést egy új elemen próbálja meg konfigurálni, akkor ez a hiba jelenik meg. | A művelet 24 óra elteltével történő újrapróbálkozása általában megoldja ezt a problémát. Ha azonban nem szűnik meg a probléma, segítségért forduljon a Microsoft támogatási szolgálatához.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet le van tiltva, mert a tároló elérte a maximális korlátot az ilyen műveletekhez, amely 24 órás időtartam alatt engedélyezett. | Ha elérte a maximális megengedett korlátot egy 24 órás span művelethez, ez a hiba jelenik meg. Ez a hiba általában akkor jelenik meg, ha olyan méretezési műveleteket végeznek, mint például a házirend módosítása vagy az automatikus védelem. A CloudDosAbsoluteLimitReached eltérően nem sokat tehet az állapot megoldásához. Valójában Azure Backup szolgáltatás a szóban forgó összes elemre vonatkozóan belső műveletet hajt végre.<br> Például: Ha nagy számú adatforrással védett egy szabályzat, és megpróbálja módosítani ezt a házirendet, a rendszer elindítja a védelmi feladatok konfigurálását az egyes védett elemekhez, és esetenként az ilyen műveletek esetében a maximálisan megengedett határértéket is elérheti naponta.| A Azure Backup szolgáltatás 24 óra elteltével automatikusan újrapróbálkozik a művelettel.

### <a name="workloadextensionnotreachable"></a>WorkloadExtensionNotReachable

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A AzureBackup munkaterhelés-bővítmény művelete sikertelen volt. | A virtuális gép leáll (vagy) a virtuális gép internetkapcsolattal kapcsolatos problémák miatt nem tud kapcsolatba lépni Azure Backup szolgáltatással.| – Győződjön meg arról, hogy a virtuális gép működik, és hogy rendelkezik internetkapcsolattal.<br>- [A bővítmény újbóli regisztrálása a SQL Server VMon](https://docs.microsoft.com/azure/backup/manage-monitor-sql-database-backup#re-register-extension-on-the-sql-server-vm).


### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
Az internetkapcsolattal kapcsolatos problémák miatt a virtuális gép nem tud kapcsolatba lépni Azure Backup szolgáltatással. | A virtuális gépnek kimenő kapcsolatra van szüksége Azure Backup szolgáltatáshoz, az Azure Storage-hoz vagy Azure Active Directory-szolgáltatásokhoz.| – Ha a NSG-t használja a kapcsolat korlátozására, akkor a *AzureBackup* szolgáltatás címkét kell használnia, hogy lehetővé tegye a kimenő hozzáférést Azure Backup szolgáltatáshoz, és Hasonlóképpen az Azure ad (*AzureActiveDirectory*) és az Azure Storage (*Storage*) szolgáltatásokhoz. A hozzáférés engedélyezéséhez kövesse az alábbi [lépéseket](./backup-sql-server-database-azure-vms.md#nsg-tags) .<br>– Győződjön meg arról, hogy a DNS feloldja az Azure-végpontokat.<br>– Ellenőrizze, hogy a virtuális gép az internet-hozzáférést blokkoló terheléselosztó mögött van-e. Ha nyilvános IP-címet rendel a virtuális gépekhez, a felderítés működni fog.<br>– Győződjön meg arról, hogy nincs olyan tűzfal/víruskereső/proxy, amely blokkolja a fenti három cél szolgáltatás hívásait.

## <a name="re-registration-failures"></a>Ismételt regisztrálási hibák

A következő tünetek közül egyet vagy többet kell megvizsgálnia az ismételt regisztrálási művelet elindítása előtt:

- Az összes művelet (például a biztonsági mentés, a visszaállítás és a biztonsági mentés konfigurálása) a virtuális gépen meghiúsul a következő hibakódok egyikével: **[WorkloadExtensionNotReachable](#workloadextensionnotreachable)**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Ha a biztonsági mentési elem **biztonsági mentési állapota** területen **nem érhető el**, akkor zárja ki az összes többi olyan okot, amely ugyanazokat az állapotot eredményezheti:

  - Nincs engedélye a biztonsági mentéssel kapcsolatos műveletek elvégzésére a virtuális gépen.
  - A virtuális gép leállítása, így a biztonsági mentések nem hajthatók végre.
  - [Hálózati problémák](#usererrorvminternetconnectivityissue)

   ![virtuális gép újbóli regisztrálása](./media/backup-azure-sql-database/re-register-vm.png)

- Always On rendelkezésre állási csoport esetén a biztonsági mentések sikertelenek voltak, miután módosította a biztonsági mentési beállításokat vagy feladatátvételt követően.

Ezek a tünetek a következő okok valamelyike miatt merülhetnek fel:

- A bővítmény törölve lett vagy el lett távolítva a portálról.
- A rendszer eltávolít egy bővítményt a virtuális gépen a **Vezérlőpultról** a **program eltávolítása vagy módosítása** lehetőség alatt.
- A virtuális gép időben vissza lett állítva a helyi lemez visszaállítása révén.
- A virtuális gép leállt egy hosszabb ideig, így lejárt a bővítmény konfigurációja.
- A virtuális gép törölve lett, és egy másik virtuális gép lett létrehozva ugyanazzal a névvel és ugyanabban az erőforráscsoporthoz, mint a törölt virtuális gép.
- A rendelkezésre állási csoport csomópontjainak egyike nem kapta meg a teljes biztonsági mentési konfigurációt. Ez akkor fordulhat elő, ha a rendelkezésre állási csoport regisztrálva van a tárolóban, vagy új csomópont van hozzáadva.

Az előző forgatókönyvekben azt javasoljuk, hogy a virtuális gépen újra regisztrálja a műveletet. A feladat PowerShellben történő végrehajtásával kapcsolatban [itt](./backup-azure-sql-automation.md#enable-backup) talál útmutatást.

## <a name="size-limit-for-files"></a>Fájlok maximális mérete

A fájlok teljes mérete nem csak a fájlok számától, hanem a nevükön és az elérési utakon is függ. Az egyes adatbázisfájlok esetében szerezze be a logikai fájl nevét és a fizikai elérési utat. Ezt az SQL-lekérdezést használhatja:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Most rendezze a következő formátumot:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Bemutatunk egy példát:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Ha a tartalom karakterláncának mérete meghaladja a 20 000 bájtot, az adatbázisfájlok tárolása másképp történik. A helyreállítás során nem fogja tudni beállítani a célfájl elérési útját a visszaállításhoz. A fájlok a SQL Server által megadott alapértelmezett SQL-elérési útra lesznek visszaállítva.

### <a name="override-the-default-target-restore-file-path"></a>Az alapértelmezett cél-visszaállítási fájl elérési útjának felülbírálása

A visszaállítási művelet során felülbírálhatja a cél-visszaállítási fájl elérési útját úgy, hogy egy olyan JSON-fájlt helyez el, amely tartalmazza az adatbázisfájl hozzárendelését a cél-visszaállítási útvonalra. Hozzon létre egy `database_name.json` fájlt, és helyezze el a helyet `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

A fájl tartalmának a következő formátumúnak kell lennie:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Bemutatunk egy példát:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Az előző tartalomban az adatbázisfájl logikai nevét a következő SQL-lekérdezéssel kérheti le:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Ezt a fájlt a visszaállítási művelet elindítása előtt kell elhelyezni.

## <a name="next-steps"></a>Következő lépések

A SQL Server virtuális gépek (nyilvános előzetes verzió) Azure Backupával kapcsolatos további információkért lásd: [Azure Backup SQL virtuális gépekhez](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
