---
title: Feladatok automatizálása SQL Agent-feladatokkal az Azure SQL felügyelt példányában
description: A Transact-SQL (T-SQL) parancsfájlok futtatásának automatizálási lehetőségei az Azure SQL felügyelt példányain
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608073"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Felügyeleti feladatok automatizálása SQL Agent-feladatok használatával az Azure SQL felügyelt példányában
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A SQL Server és az [SQL felügyelt példány](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) [SQL Server Agent](/sql/ssms/agent/sql-server-agent) használatával létrehozhat és ütemezhet olyan feladatokat, amelyek egy vagy több adatbázison rendszeres időközönként végrehajthatók a Transact-SQL (T-SQL) lekérdezések futtatásához és a karbantartási feladatok végrehajtásához. Ez a cikk bevezette az SQL Agent SQL-ügynök felügyelt példányát.

> [!Note]
> Az SQL Agent Azure SQL Database vagy az Azure szinapszis Analyticsben nem érhető el. Ehelyett [rugalmas feladatokkal ajánlott a feladatok automatizálása](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>SQL Agent-feladatok korlátai az Azure SQL felügyelt példányában

Érdemes megjegyezni, hogy a SQL Server és az SQL felügyelt példányának részeként elérhető SQL-ügynök közötti különbségek megtalálhatók. A SQL Server és az SQL felügyelt példány közötti támogatott funkciókkal kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányának T-SQL-különbségei SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

A SQL Serverban elérhető SQL Agent-funkciók némelyike nem támogatott az SQL felügyelt példányaiban:

- Az SQL-ügynök beállításai csak olvashatók. 
    - A rendszer tárolt eljárása `sp_set_agent_properties` nem támogatott az SQL felügyelt példányain.
- Az SQL Agent engedélyezése/letiltása jelenleg nem támogatott az SQL felügyelt példányain. Az SQL Agent folyamatosan fut.
- Az értesítések részlegesen támogatottak:
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások nem támogatottak.
- A proxyk nem támogatottak.
- Az Eseménynapló nem támogatott.
- Az inaktív PROCESSZORok alapján végzett feladatütemezés-trigger nem támogatott.

## <a name="when-to-use-sql-agent-jobs"></a>Mikor kell SQL Agent-feladatokat használni 

Az SQL Agent-feladatok használata több esetben is lehetséges:

- Automatizálja a felügyeleti feladatokat, és ütemezze őket a hétköznapok futtatásához, órák után stb.
  - Üzembe helyezhet sémamódosításokat, kezelheti a hitelesítő adatokat, teljesítményadatokat vagy bérlői (ügyfél-) telemetriát gyűjthet.
  - Frissítse a hivatkozási adatokat (az összes adatbázissal közös információ), töltse be az adatokat az Azure Blob Storage-ból.
  - Gyakori karbantartási feladatok, például a DBCC CHECKDB UTASÍTÁST, amelyekkel biztosítható az adatok integritása vagy az index karbantartása a lekérdezési teljesítmény javítása érdekében. Úgy konfigurálhatja a feladatokat, hogy a rendszer egy adott adatbázis-gyűjteményen rendszeres időközönként hajtsa őket végre, például csúcsidőn kívül.
  - A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
- Adatokat gyűjthet jelentéskészítéshez
  - Adatbázisokból származó összesített adatok egyetlen célként megadott táblába.
  - Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.
- Adatáthelyezés
  - Olyan feladatokat hozhat létre, amelyek replikálják az adatbázisaiban végrehajtott módosításokat más adatbázisokra, vagy a távoli adatbázisokban végrehajtott frissítéseket gyűjtik, és módosításokat alkalmaznak az adatbázisban.
  - SQL Server Integration Services (SSIS) használatával olyan feladatokat hozhat létre, amelyek a vagy az adatbázisaiba töltik be az adatait.

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>SQL Agent-feladatok az Azure SQL felügyelt példányában

Az SQL-ügynök feladatait az SQL Agent szolgáltatás hajtja végre, amely továbbra is használható a SQL Server és az SQL felügyelt példányában a feladatok automatizálásához. 

Az SQL-ügynök feladatai a T-SQL-parancsfájlok megadott sorozata az adatbázison. A feladatok használatával olyan felügyeleti feladatokat határozhat meg, amelyek egy vagy több alkalommal futtathatók, és a sikerhez vagy a meghibásodáshoz is megfigyelhetők. 

A feladatok futhatnak egy helyi kiszolgálón vagy több távoli kiszolgálón is. Az SQL Agent-feladatok egy belső adatbázismotor-összetevő, amely az SQL felügyelt példányának szolgáltatásán belül fut.

Az SQL Agent feladatainak számos alapvető fogalma van:

- A feladatok **végrehajtásához** szükséges lépések egy vagy több lépésből állnak. Minden feladat lépésnél megadhatja az újrapróbálkozási stratégiát, valamint azt a műveletet, amely a feladat lépéseinek sikeres vagy sikertelen végrehajtása esetén fordulhat elő.
- Az **ütemezett** feladatok határozzák meg, hogy mikor kell végrehajtani a feladatot.
- Az **értesítések** segítségével meghatározhatja azokat a szabályokat, amelyek segítségével a rendszer e-mailben értesíti a kezelőket a feladatok befejezését követően.

### <a name="sql-agent-job-steps"></a>SQL Agent-feladatok lépései

Az SQL Agent-feladatok lépései az SQL Agent által végrehajtandó műveletek sorozatából állnak. Minden lépésnél a következő lépésnek kell megjelennie, ha a lépés sikeres vagy sikertelen, hiba esetén az újrapróbálkozások száma.

Az SQL-ügynök lehetővé teszi, hogy különböző típusú feladatokat hozzon létre, például a Transact-SQL-feladatok lépéseit, amelyek egyetlen Transact-SQL-köteget hajtanak végre az adatbázison, vagy az operációs rendszert [futtató, az](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) egyéni operációsrendszer-parancsfájlt végrehajtó operációsrendszer-parancsot, illetve PowerShell-lépéseket, amelyek lehetővé teszik az adatok SSIS-futtatókörnyezettel való betöltését [, illetve az](../managed-instance/replication-transactional-overview.md) adatbázisból

> [!Note]
> Az Azure SSIS Integration Runtime az Azure SQL felügyelt példány által üzemeltetett SSISDB való használatával kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányának használata SQL Server Integration Services (SSIS) segítségével Azure Data Factory](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

A [tranzakciós replikáció](../managed-instance/replication-transactional-overview.md) replikálhatja a táblák módosításait az Azure SQL felügyelt példányainak más adatbázisaiba, Azure SQL Database vagy SQL Server. További információ: [replikáció konfigurálása az Azure SQL felügyelt példányában](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

A más típusú feladatok lépései jelenleg nem támogatottak az SQL felügyelt példányain, beleértve a következőket:

- A replikációs feladatok egyesítése lépés nem támogatott.
- A várólista-olvasó nem támogatott.
- A Analysis Services nem támogatottak
 
### <a name="sql-agent-job-schedules"></a>SQL Agent-feladatok ütemezett adatai

Az ütemterv meghatározza, hogy mikor fusson a feladatok. Ugyanazon az ütemterven több feladatot is futtathat, és több ütemező is vonatkozhat ugyanarra a feladatokra.

Az ütemtervek a következő feltételeket határozzák meg a feladatok futásának idejére:

- Amikor SQL Server Agent elindul. A feladatot minden feladatátvétel után aktiválja a rendszer.
- Egy alkalommal, egy adott dátummal és időponttal, amely bizonyos feladatok késleltetett végrehajtásához hasznos.
- Ismétlődő ütemterv szerint.

> [!Note]
> A felügyelt SQL-példány jelenleg nem teszi lehetővé a feladatok elindítását, ha a processzor üresjáratban van.

### <a name="sql-agent-job-notifications"></a>SQL Agent-feladatok értesítései

Az SQL Agent-feladatok lehetővé teszik, hogy értesítéseket kapjon a feladat sikeres befejeződése vagy meghibásodása esetén. Értesítéseket e-mailben kaphat.

Ha még nincs engedélyezve, először konfigurálnia kell [a Database mail funkciót](/sql/relational-databases/database-mail/database-mail) az Azure SQL felügyelt példányán:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Példaként adja meg az e-mail értesítések küldéséhez használni kívánt e-mail-fiókot. Rendelje hozzá a fiókot a nevű e-mail-profilhoz `AzureManagedInstance_dbmail_profile` . Ha SQL Agent-feladatokkal szeretné elküldeni az e-mailt a felügyelt példányban, olyan profilt kell meghívnia, amelyet hívni kell `AzureManagedInstance_dbmail_profile` . Ellenkező esetben az SQL felügyelt példánya nem tud e-maileket küldeni az SQL Agent használatával. Tekintse meg a következő mintát:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Tesztelje a Database Mail konfiguráció T-SQL használatával a [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) rendszer tárolt eljárásával:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Értesítheti a kezelőt arról, hogy valami történt az SQL Agent-feladatokkal. Az operátor az SQL által felügyelt példányok egy vagy több példányának fenntartásáért felelős személy kapcsolattartási adatait határozza meg. Egyes esetekben az operátori feladatok egy személyhez vannak rendelve.

Az SQL felügyelt példányában vagy SQL Server több példányban lévő rendszerekben számos személy megoszthatja az operátori feladatokat. Az operátor nem tartalmaz biztonsági információt, és nem határoz meg rendszerbiztonsági tag. Ideális esetben az operátor nem olyan személy, akinek a feladatai változhatnak, de egy e-mail-terjesztési csoport.   

A következő példában látható módon [hozhat létre kezelőket](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) SQL Server Management Studio (SSMS) vagy a Transact-SQL parancsfájl használatával:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Erősítse meg az e-mailek sikerességét vagy sikertelenségét a SSMS [Database mail-naplóján](/sql/relational-databases/database-mail/database-mail-log-and-audits) keresztül.

Ezután módosíthatja az [SQL-ügynök feladatait](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) , és hozzárendelheti azokat az operátorokat, amelyek e-mailben értesítést kapnak, ha a feladatok a SSMS vagy a következő Transact-SQL-szkripttel fejeződik be, sikertelenek vagy sikeresek:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL Agent-feladatok előzményei

Az Azure SQL felügyelt példánya jelenleg nem teszi lehetővé az SQL-ügynökök tulajdonságainak módosítását, mivel azokat a beállításjegyzékben tárolt értékekben tárolja a rendszer. Ez azt jelenti, hogy a feladatok előzményeinek megtartására vonatkozó adatmegőrzési házirend beállításához szükséges beállítások az alapértelmezett 1000 összes rekord és a maximális 100-előzmény rekordok száma szerint vannak rögzítve.

### <a name="sql-agent-fixed-database-role-membership"></a>SQL Agent rögzített adatbázis-szerepkör tagsága

Ha a nem sysadmin bejelentkezésekhez kapcsolódó felhasználók hozzá vannak adva a msdb rendszeradatbázis három SQL Agent rögzített adatbázis-szerepköréhez, létezik egy olyan probléma, amelyben a bejelentkezési adatok működéséhez meg kell adni explicit végrehajtási engedélyeket. A probléma előfordulásakor a "végrehajtás engedély megtagadva az objektumon <object_name> (Microsoft SQL Server, hiba: 229)" hibaüzenet jelenik meg. 

Miután hozzáadta a felhasználókat egy SQL Agent rögzített adatbázis-szerepkörhöz (SQLAgentUserRole, SQLAgentReaderRole vagy Sqlagentoperatorrole indíthatják) a msdb-ben, a szerepkörökhöz hozzáadott összes felhasználói bejelentkezéshez hajtsa végre az alábbi T-SQL-szkriptet, hogy explicit módon adjon végrehajtási engedélyeket a felsorolt rendszer által tárolt eljárásoknak. Ez a példa azt feltételezi, hogy a Felhasználónév és a bejelentkezési név azonos. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Tudjon meg többet

- [Mi az az Azure SQL felügyelt példánya?](../managed-instance/sql-managed-instance-paas-overview.md)
- [A Azure SQL Database & SQL felügyelt példányának újdonságai](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Az Azure SQL felügyelt példányának T-SQL-különbségei SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Szolgáltatások összehasonlítása: Azure SQL Database és az Azure SQL felügyelt példánya](../../azure-sql/database/features-comparison.md)
