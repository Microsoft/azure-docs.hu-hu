---
title: Adatbázisok áttelepítése SQL Managed Instance Log Replay service használatával
description: Megtudhatja, hogyan mihet át adatbázisokat a SQL Server-SQL Managed Instance a Log Replay Service használatával
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484030"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Adatbázisok áttelepítése a SQL Server-SQL Managed Instance Log Replay service (előzetes verzió) használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan konfigurálhatja manuálisan az adatbázis migrálását SQL Server 2008.2019. Azure SQL Managed Instance., a jelenleg nyilvános előzetes verzióban elérhető Log Replay Service (LRS) használatával. Az LRS egy felhőalapú szolgáltatás, amely SQL Managed Instance, és SQL Server technológián alapul. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) és LRS ugyanazt a mögöttes migrálási technológiát és API-kat használják. Az LRS engedélyezésével tovább engedélyezünk összetett egyéni migrálásokat és hibrid architektúrát a helyszíni környezetek és SQL Server SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Mikor kell használni a Log Replay szolgáltatást?

Ha nem használhatja az Azure Database Migration Service-t a migráláshoz, az LRS-t közvetlenül a PowerShell-sel, Azure CLI-parancsmagokkal vagy API-kkal használhatja az adatbázis-migrálások manuális felépítéséhez és vezényl SQL Managed Instance. 

A következő esetekben érdemes lehet LRS-t használni:
- Jobban szabályozni kell az adatbázis-migrálási projektet.
- Az átállásos migrálásban az állásidő nem túl nagy tűréshatár.
- A Database Migration Service végrehajtható fájl nem telepíthető a környezetben.
- A Database Migration Service végrehajtható fájl nem rendelkezik fájleléréssel az adatbázis biztonsági másolataihoz.
- Nem érhető el a gazda operációs rendszer, vagy nincsenek rendszergazdai jogosultságok.
- Nem nyithat meg hálózati portokat a környezetből az Azure-ba.
- Hálózati szabályozás vagy proxyblokkolási problémák a környezetben.
- A biztonsági másolatok tárolása közvetlenül a Azure Blob Storage a `TO URL` beállításon keresztül.
- Különbségi biztonsági mentést kell használnia.

> [!NOTE]
> Javasoljuk, hogy automatizálja az adatbázisok migrálását a SQL Server-SQL Managed Instance-Database Migration Service. Ez a szolgáltatás ugyanazt az LRS felhőszolgáltatást használja a háttérkiszolgálón, és a naplók szállítási módját `NORECOVERY` használja. Fontolja meg manuálisan az LRS-t a migrálások vezényléhez, ha Database Migration Service nem támogatja teljes mértékben az Ön forgatókönyvét.

## <a name="how-it-works"></a>Működés

Az adatbázisok felhőbe való áttelepítése LRS-sel való egyéni megoldás létrehozásához több vezénylési lépés szükséges, ahogy azt a szakasz későbbi részében látható diagram és táblázat is mutatja.

Az áttelepítés során teljes adatbázis-biztonsági mentést kell készíteni SQL Server engedélyezett `CHECKSUM` Azure Blob Storage. Az LRS a biztonsági mentési fájlok visszaállítására használható Blob Storage-SQL Managed Instance. Blob Storage egy köztes tároló a SQL Server és SQL Managed Instance.

Az LRS Blob Storage a teljes biztonsági mentés visszaállítása után hozzáadott új különbözeti vagy naplómentéseket. Az LRS ezután automatikusan visszaállítja ezeket az új fájlokat. A szolgáltatással figyelheti a biztonsági mentési fájlok visszaállításának folyamatát a SQL Managed Instance, és szükség esetén leállíthatja a folyamatot.

Az LRS nem igényel meghatározott elnevezési konvenciót a biztonsági mentési fájlokhoz. Megvizsgálja az összes fájlt, amely a Blob Storage, és úgy készíti el a biztonsági mentési láncot, hogy csak a fájlfejléceket olvassa be. Az adatbázisok "visszaállítás" állapotban vannak az áttelepítési folyamat során. Az adatbázisok [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) módban állnak vissza, így a migrálási folyamat befejezéséig nem használhatók olvasásra vagy írásra. 

Több adatbázis áttelepítése esetén a következőt kell:
 
- Az egyes adatbázisok biztonsági másolatai külön mappába kerülnek a Blob Storage.
- Minden adatbázis esetében külön indítsa el az LRS-t.
- Adjon meg különböző elérési utakat a Blob Storage elválasztani. 

Az LRS-t automatikus kiegészítési vagy folyamatos *módban* *is elindíthatja.* Amikor automatikus kiegészítési módban indítja el, az áttelepítés automatikusan befejeződik, amikor a megadott biztonsági mentési fájlok utolsó visszaállítása megtörténik. Amikor folyamatos módban indítja el az LRS-t, a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonsági mentési fájlokat, és az áttelepítés csak manuális átálláskor fog befejeződni. 

Azt javasoljuk, hogy manuálisan állítsa át az adatokat, miután a napló vége biztonsági mentést készült, és a rendszer visszaállítottként jelenik meg a SQL Managed Instance. Az utolsó átváltási lépés online állapotba teszi az adatbázist, és elérhetővé válik az olvasási és írási SQL Managed Instance.

Az LRS automatikus vagy automatikus kiegészítésen keresztüli, vagy az átállításon keresztüli manuális leállítása után nem folytathatja a visszaállítási folyamatot a szolgáltatásban online állapotba SQL Managed Instance. Ha további biztonsági mentési fájlokat szeretne visszaállítani az áttelepítés automatikus kiegészítéssel vagy átállással végzett befejezése után, törölnie kell az adatbázist. Az LRS újraindításával a teljes biztonsági mentési láncot is vissza kell állítania az nulláról.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="A Log Replay Service vezénylési lépéseit a SQL Managed Instance." border="false":::
    
| Művelet | Részletek |
| :----------------------------- | :------------------------- |
| **1. Adatbázis biztonsági másolatának másolása a SQL Server-Blob Storage.** | Teljes, különbségi és naplómentés másolása a SQL Server egy Blob Storage-tárolóba az [Azcopy](../../storage/common/storage-use-azcopy-v10.md) vagy a [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/) <br /><br />Használjon bármilyen fájlnevet. Az LRS nem igényel konkrét fájlelnevezési konvenciót.<br /><br />Több adatbázis áttelepítésekor minden adatbázishoz külön mappára van szükség. |
| **2. Indítsa el az LRS-t a felhőben.** | A szolgáltatást a következő parancsmagokkal újraindíthatja: PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) vagy Azure CLI ( az_sql_midb_log_replay_start[parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Az LRS-t minden olyan adatbázis esetében külön indítsa el, amely egy biztonsági mentési mappára mutat a Blob Storage. <br /><br /> Miután elindítja a szolgáltatást, biztonsági másolatokat készít a Blob Storage tárolóból, és elkezdi visszaállítani őket a SQL Managed Instance.<br /><br /> Ha az LRS-t folyamatos módban indította el, az összes eredetileg feltöltött biztonsági másolat visszaállítása után a szolgáltatás figyelni fogja a mappába feltöltött új fájlokat. A szolgáltatás folyamatosan alkalmazza a naplókat a naplóütemezési szám (LSN) lánc alapján, amíg le nem áll. |
| **2.1. Figyelje a művelet előrehaladását.** | A visszaállítási művelet előrehaladását a következő parancsmagokkal figyelheti: PowerShell ([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) vagy Azure CLI ( az_sql_midb_log_replay_show[parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. Szükség esetén állítsa le a műveletet.** | Ha le kell állítania a migrálási folyamatot, több parancsmagot is választhat: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) vagy Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> A művelet leállításával törli a visszaállítás alatt SQL Managed Instance. Miután leállít egy műveletet, nem folytathatja az LRS-t egy adatbázishoz. A migrálási folyamatot az nulláról kell újraindítani. |
| **3. Ha** elkészült, át kell állnia a felhőbe. | Állítsa le az alkalmazást és a számítási feladatot. Az utolsó naplószéles biztonsági mentést töltse fel a Azure Blob Storage.<br /><br /> Az átváltás befejezéséhez indítson el egy LRS-műveletet a következő parancsmagokkal: `complete` PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) vagy Azure CLI [az_sql_midb_log_replay_complete.](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) Ez a művelet leállítja az LRS-t, és az adatbázis online állapotba hozása olvasási és írási használatra a SQL Managed Instance.<br /><br /> Az alkalmazás kapcsolati sztringet a SQL Server a SQL Managed Instance. Ezt a lépést önnek kell összehangolnia az alkalmazásban manuális kapcsolati sztring-módosítással vagy automatikusan (például ha az alkalmazás be tudja olvasni a kapcsolati sztringet egy tulajdonságból vagy egy adatbázisból). |

## <a name="requirements-for-getting-started"></a>Az első lépések követelményei

### <a name="sql-server-side"></a>SQL Server oldal
- SQL Server 2008-2019
- Adatbázisok teljes biztonsági mentése (egy vagy több fájl)
- Különbségi biztonsági mentés (egy vagy több fájl)
- Naplók biztonsági mentése (tranzakciós naplófájlok felosztása nélkül)
- `CHECKSUM` biztonsági mentések engedélyezése (kötelező)

### <a name="azure-side"></a>Azure-oldal
- A PowerShell Az.SQL-modul 2.16.0-s vagy újabb[verziója](https://www.powershellgallery.com/packages/Az.Sql/) (telepítve vagy elérhető a [Azure Cloud Shell](/azure/cloud-shell/))
- Az Azure CLI 2.19.0-s vagy újabb verziója[(telepített )](/cli/azure/install-azure-cli)
- Azure Blob Storage kiépítve
- Közös hozzáférésű jogosultság signature (SAS) biztonsági jogkivonat olvasási és listás engedélyekkel a Blob Storage tárolóhoz

### <a name="migration-of-multiple-databases"></a>Több adatbázis migrálása
A különböző adatbázisok biztonsági mentési fájljait külön mappákba kell Blob Storage.

Minden adatbázis esetében külön indítsa el az LRS-t úgy, hogy a megfelelő mappára mutat a Blob Storage. Az LRS legfeljebb 100 egyidejű visszaállítási folyamatot támogat egyetlen felügyelt példányonként.

### <a name="azure-rbac-permissions"></a>Azure RBAC-engedélyek
Az LRS a megadott ügyfeleken való futtatásához az alábbi Azure-szerepkörök valamelyike szükséges:
- Előfizetés tulajdonosa szerepkör
- [Felügyelt példány közreműködője](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) szerepkör
- Egyéni szerepkör a következő engedéllyel: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Ajánlott eljárások

A következő ajánlott eljárásokat javasoljuk:
- Futtassa [Data Migration Assistant,](/sql/dma/dma-overview) hogy ellenőrizze, hogy az adatbázisok készen állnak-e a SQL Managed Instance. 
- A teljes és különbségi biztonsági mentéseket több fájlra osztja szét egyetlen fájl használata helyett.
- Engedélyezze a biztonsági mentés tömörítését.
- A Cloud Shell parancsprogramokat futtathat, mivel az mindig a legújabb kiadott parancsmagok használatára frissül.
- A migrálás befejezését az LRS elindítani követő 47 órán belül tervezze meg. Ez egy türelmi időszak, amely megakadályozza a rendszer által felügyelt szoftverjavítások telepítését.

> [!IMPORTANT]
> - A migrálási folyamat befejezéséig nem használhatja az LRS-sel visszaállított adatbázist. 
> - Az LRS nem támogatja az adatbázisokhoz való csak olvasási hozzáférést a migrálás során.
> - A migrálás befejezése után a migrálási folyamat végleges lesz, mert az LRS nem támogatja a visszaállítási folyamat visszaállítását.

## <a name="steps-to-execute"></a>A végrehajtás lépései

### <a name="make-backups-of-sql-server"></a>Biztonsági másolat készítése SQL Server

A biztonsági másolatokat SQL Server alábbi lehetőségek egyikével készítse el:

- Biztonsági másolat készítése helyi lemezes tárhelyre, majd fájlok feltöltése a Azure Blob Storage, ha a környezet a közvetlen biztonsági mentéseket Blob Storage.
- Ha a környezet és a biztonsági eljárások ezt lehetővé teszik Blob Storage T-SQL kapcsolóval közvetlenül a biztonsági Blob Storage `TO URL` is. 

Állítsa be a teljes helyreállítási módba átemelni kívánt adatbázisokat a naplók biztonsági mentésének engedélyezése érdekében.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Az adatbázis teljes, különbségi és naplóbeli biztonsági mentésének manuális kezű készítéséhez használja a következő T-SQL-példaszk szkripteket. Győződjön meg `CHECKSUM` arról, hogy a beállítás engedélyezve van, mert az LRS-hez kötelező.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Azure Blob Storage a rendszer köztes tárolóként használja a biztonsági mentési fájlokhoz a SQL Server és SQL Managed Instance. Új tárfiók és blobtároló létrehozásához kövesse az alábbi lépéseket:

1. [Hozzon létre egy tárfiókot.](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása a](../../storage/blobs/storage-quickstart-blobs-portal.md) tárfiókban.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Biztonsági másolatok másolása SQL Server Blob Storage

Az adatbázisok felügyelt példányra LRS használatával való áttelepítésekor a következő megközelítésekkel tölthet fel biztonsági másolatokat a Blob Storage:
- Natív SQL SERVER [URL-címre funkció](/sql/relational-databases/backup-restore/sql-server-backup-to-url) használata
- Biztonsági másolatok [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) blobtárolóba való feltöltése az [Azcopy](../../storage/common/storage-use-azcopy-v10.md) vagy a Azure Storage Explorer használatával
- Az Storage Explorer használata a Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Biztonsági másolatok készítése SQL Server a Blob Storage
Ha a vállalati és hálózati házirendek ezt lehetővé teszik, alternatív megoldásként a biztonsági mentéseket közvetlenül a SQL Server-ból a Blob Storage-be is biztonsági másolatot készít a SQL Server BIZTONSÁGI MENTÉS [URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url) lehetőség használatával. Ha ezt a lehetőséget választja, nem kell biztonsági másolatot készítenie a helyi tárolón, és feltöltenie azokat a Blob Storage.

Első lépésként ehhez a művelethez létre kell hoznia egy SAS-hitelesítési jogkivonatot a Blob Storage majd importálni kell a jogkivonatot a SQL Server. A második lépés a biztonsági mentések készítése a `TO URL` T-SQL lehetőséggel. Győződjön meg arról, hogy az összes biztonsági mentés engedélyezve `CHEKSUM` van.

Referenciaként a következő mintakód biztonsági másolatokat készít a Blob Storage. Ez a példa nem tartalmazza az SAS-jogkivonat importálásának utasításait. Részletes utasításokat, például az SAS-jogkivonat létrehozásáról és importálását SQL Server a Use Azure Blob Storage with SQL Server (A [sas-token](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)Azure Blob Storage használatával) SQL Server. 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Sas-Blob Storage létrehozása LRS-hez

Azure Blob Storage a rendszer köztes tárolóként használja a biztonsági mentési fájlokhoz a SQL Server és SQL Managed Instance. Létre kell hoznia egy SAS-hitelesítési jogkivonatot, amely csak lista- és olvasási engedélyekkel rendelkezik az LRS-hez. A jogkivonat lehetővé teszi, hogy az LRS hozzáférjen Blob Storage és a biztonsági mentési fájlok használatával visszaállítsa azokat a SQL Managed Instance. 

A jogkivonat létrehozásához kövesse az alábbi lépéseket:

1. Nyissa Storage Explorer a Azure Portal.
2. **Bontsa ki a Blobtárolók bontsa ki a et.**
3. Kattintson a jobb gombbal a blobtárolóra, és válassza a **Közös hozzáférésű jogosultság aláírása lehetőséget.**

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Képernyőkép az S A S hitelesítési jogkivonat létrehozásához szükséges beállításokról.":::

4. Válassza ki a jogkivonat lejáratának időkeretét. Győződjön meg arról, hogy a jogkivonat érvényes a migrálás idejére.
5. Válassza ki a jogkivonat időzónát: UTC vagy helyi idő szerint.
    
   > [!IMPORTANT]
   > Előfordulhat, hogy a jogkivonat és a felügyelt példány időzónája nem egyező. Győződjön meg arról, hogy az SAS-jogkivonat érvényessége megfelelő, figyelembe véve az időzónákat. Ha lehetséges, állítsa az időzónát a tervezett migrálási időszak korábbi és későbbi időpontára.
6. Válassza **a Csak** olvasási és a Csak **listás** engedélyek lehetőséget.

   > [!IMPORTANT]
   > Ne válasszon ki más engedélyeket. Ha így van, az LRS nem indul el. Ez a biztonsági követelmény a tervezésből áll.
7. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Képernyőkép az S A S jogkivonat lejáratának, időzónának és engedélyeinek kiválasztásáról, valamint a Létrehozás gombról.":::

Az SAS-hitelesítés a megadott érvényességgel jön létre. Szüksége lesz a jogkivonat URI-verziójára, ahogyan az alábbi képernyőképen látható.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Képernyőkép egy S A S jogkivonat U R I-verziójáról.":::

### <a name="copy-parameters-from-the-sas-token"></a>Paraméterek másolása az SAS-jogkivonatból

Mielőtt az SAS-jogkivonattal elindítja az LRS-t, meg kell értenie annak szerkezetét. A generált SAS-jogkivonat URI-ját két, kérdőjellel () elválasztott rész `?` tartalmazza, ahogyan az alábbi példában látható:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Példa U I-re egy generált S A S jogkivonatra a Log Replay Service-hez." border="false":::

Az első rész, amely a kérdőjel () jellel kezdődik, az LRS bemeneteként megadottként megadott `https://` `?` `StorageContainerURI` paraméterhez lesz használva. LRS-információkat biztosít arról a mappáról, ahol az adatbázis biztonsági másolati fájljai tárolva vannak.

A második rész, amely a kérdőjel ( ) után kezdődik, és egészen a sztring végéig `?` tart, a `StorageContainerSasToken` paraméter. Ez a tényleges aláírt hitelesítési jogkivonat, amely a megadott időtartamra érvényes. Ennek a résznek nem feltétlenül kell azzal `sp=` kezdenie, ahogyan az a példában látható. Az ön esete eltérhet.

Másolja a paramétereket az alábbiak szerint:

1. Másolja ki a jogkivonat első részét, kezdve egészen a kérdőjelig `https://` ( `?` ). Ezt használhatja paraméterként `StorageContainerUri` a PowerShellben vagy az Azure CLI-ban az LRS indításhoz.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="A jogkivonat első részének másolását bemutató képernyőkép.":::

2. Másolja a jogkivonat második részét a kérdőjeltől () egészen a sztring `?` végéig. Ezt használhatja paraméterként `StorageContainerSasToken` a PowerShellben vagy az Azure CLI-ban az LRS indításhoz.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="A jogkivonat második részének másolását bemutató képernyőkép.":::
   
> [!NOTE]
> Ne jelölje meg a kérdőjelet, ha a jogkivonat bármelyik részét átmásolja.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Jelentkezzen be az Azure-ba, és válasszon ki egy előfizetést

Jelentkezzen be az Azure-ba a következő PowerShell-parancsmag használatával:

```powershell
Login-AzAccount
```

A következő PowerShell-parancsmag használatával válassza ki a megfelelő előfizetést, amelyben a felügyelt példány található:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Az áttelepítés elindítása

A migrálást az LRS-sel indítja el. A szolgáltatást automatikus kiegészítési vagy folyamatos módban is elindíthatja. 

Ha automatikus kiegészítési módot használ, az áttelepítés automatikusan befejeződik, amikor a megadott biztonsági mentési fájlok utolsó visszaállítása megtörténik. Ehhez a beállításhoz a start parancsra van szükség az utolsó biztonsági mentési fájl fájlnevének megadásához. 

A folyamatos mód használata esetén a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonsági mentési fájlokat. A migrálás csak manuális átállás esetén fog befejeződni. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS automatikus kitöltési módban való elindítani

Az LRS automatikus kiegészítési módban való indításhoz használja a következő PowerShell- vagy Azure CLI-parancsokat. A paraméterrel adja meg az utolsó biztonsági mentési fájl `-LastBackupName` nevét. A megadott biztonsági mentési fájlok utolsó visszaállításakor a szolgáltatás automatikusan kezdeményezi az átváltást.

Az alábbi példa az LRS automatikus kiegészítési módban való indítását példázhatja a PowerShell használatával:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Az alábbi példában az LRS automatikus kiegészítési módban való indítása az Azure CLI használatával történik:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS in continuous mode (LRS elindítani folyamatos módban)

Példa LRS folyamatos módban való indításra a PowerShell használatával:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Az alábbi példában az LRS folyamatos módban való indítása az Azure CLI használatával történik:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

A PowerShell- és a CLI-ügyfelek szinkron módon indítják el az LRS-t folyamatos módban. Ez azt jelenti, hogy az ügyfelek megvárják, amíg az API válasza jelzi a feladat sikeres vagy sikertelen indítását. 

A várakozás során a parancs nem ad vissza vezérlést a parancssorhoz. Ha szkriptet futtat a migrálási folyamathoz, és az LRS start parancsára van szüksége, hogy azonnal vissza tudja adni a vezérlést a szkript többi részének folytatásához, a PowerShellt háttérbeli feladatként futtathatja a `-AsJob` kapcsolóval. Például:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Amikor elindít egy háttér feladatot, a feladatobjektum azonnal visszatér, még akkor is, ha a feladat hosszabb ideig tart. A feladat futása közben megszakítás nélkül dolgozhat a munkamenetben. A PowerShell háttérként való futtatásával kapcsolatos részletekért tekintse meg a [PowerShell Start-Job dokumentációját.](/powershell/module/microsoft.powershell.core/start-job#description)

Hasonlóképpen, ha Linuxon is elindítani egy Azure CLI-parancsot háttérfolyamatként, használja az LRS indítási parancsának végén látható és `&` () parancsot:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Az LRS elkezdését követően a rendszer által felügyelt szoftverjavítások 47 órán át leállnak. Az ablak után a következő automatizált szoftverjavítás automatikusan leállítja az LRS-t. Ha ez történik, nem folytathatja a migrálást, és újra kell indítania az egészet. 

## <a name="monitor-the-migration-progress"></a>A migrálási folyamat figyelése

A PowerShellen keresztüli migrálás előrehaladásának figyelése érdekében használja a következő parancsot:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

A migrálás azure cli-s használatával való nyomon követéséhez használja a következő parancsot:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>A migrálás leállítása

Ha le kell állítania az áttelepítést, használja a következő parancsmagokat. A migrálás leállítása törli a visszaállítási adatbázist a SQL Managed Instance, így a migrálás nem lehetséges.

Az áttelepítési folyamat PowerShellen keresztüli leállításához használja a következő parancsot:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

A migrálási folyamat Az Azure CLI használatával való leállításához használja a következő parancsot:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Az áttelepítés befejezése (folyamatos mód)

Ha az LRS-t folyamatos módban indította el, miután az összes biztonsági másolat vissza lett állítani, az átállás kezdeményezése befejezi a migrálást. Az átváltás után az adatbázis migrálva lesz, és készen áll az olvasási és írási hozzáférésre.

A powershelles LRS folyamatos módban való áttelepítési folyamatának befejezéséhez használja a következő parancsot:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

A migrálási folyamat folyamatos LRS módban, az Azure CLI használatával való befejezéséhez használja a következő parancsot:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funkcionális korlátozások

Az LRS funkcionális korlátozásai:
- A visszaállított adatbázis nem használható csak olvasási hozzáféréshez a migrálási folyamat során.
- A rendszer által felügyelt szoftverjavítások 47 órán át le vannak tiltva az LRS elindítani. Az időkeret lejárta után a következő szoftverfrissítés leállítja az LRS-t. Ezután újra kell indítania az LRS-t az nulláról.
- Az LRS megköveteli, hogy SQL Server adatbázisról biztonsági mentése engedélyezve `CHECKSUM` legyen.
- Az LRS által használt SAS-jogkivonatot a teljes Azure Blob Storage kell generálni, és csak olvasási és listához szükséges engedélyekkel kell rendelkeznie.
- A különböző adatbázisok biztonsági mentési fájljait külön mappákba kell helyezni a Blob Storage.
- Az LRS-t minden olyan adatbázis esetében külön kell elindítani, amely külön mappákra mutat a biztonsági mentési Blob Storage.
- Az LRS legfeljebb 100 egyidejű visszaállítási folyamatot támogat egyetlen felügyelt példányonként.

## <a name="troubleshooting"></a>Hibaelhárítás

Az LRS elkezdése után a figyelési parancsmag ( vagy ) használatával tekintse meg `get-azsqlinstancedatabaselogreplay` `az_sql_midb_log_replay_show` a művelet állapotát. Ha az LRS egy idő után nem indul el, és hibaüzenetet kap, ellenőrizze a leggyakoribb problémákat:

- A SQL Managed Instance adatbázisnak ugyanaz a neve, mint annak, amelyről az áttelepítést SQL Server? Oldja fel az ütközést az adatbázisok egyikének a másiknak ananétiával.
- Az adatbázis biztonsági mentése a SQL Server a `CHECKSUM` kapcsolóval történt?
- Az SAS-jogkivonat engedélyei csak az LRS-hez vannak beolvasva és listába sorolva?
- A kérdőjel () után kimásolódta az LRS SAS-jogkivonatát a következő `?` tartalommal: `sv=2020-02-10...` ? Â 
- Érvényes-e az SAS-jogkivonat érvényességi ideje a migrálás elkezdését és befejezését jelző időszakra? A szolgáltatáshoz és az SAS-jogkivonathoz használt különböző időzónák SQL Managed Instance eltérést is előfordulhatnak. Próbálja meg újragenerálni az SAS-jogkivonatot, és kiterjesztve az aktuális dátum előtti és utáni időtartomány jogkivonat-érvényességét.
- Helyesen írta be az adatbázis nevét, az erőforráscsoport nevét és a felügyelt példány nevét?
- Ha automatikus kiegészítési módban indította el az LRS-t, az érvényes fájlnév volt az utolsó megadott biztonsági mentési fájlhoz?

## <a name="next-steps"></a>Következő lépések
- További információ a felügyelt [SQL SQL Server példányra való áttelepítésről.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- További információ a [és a SQL Server közötti SQL Managed Instance.](transact-sql-tsql-differences-sql-server.md)
- További információ az Azure-ba migrált számítási feladatok költségeinek és [méretének ajánlott eljárásait.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
