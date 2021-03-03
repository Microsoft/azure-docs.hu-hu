---
title: Adatbázisok migrálása az SQL felügyelt példányára a log Replay szolgáltatással
description: Ismerje meg, hogyan migrálhat adatbázisokat SQL Serverról SQL felügyelt példányra a log Replay szolgáltatás használatával
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661358"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Adatbázisok migrálása SQL Serverról SQL felügyelt példányra a log Replay szolgáltatás használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan konfigurálni az adatbázis-áttelepítést SQL Server 2008-2019-ről SQL felügyelt példányra a log Replay szolgáltatás (LRS) használatával. Ez egy felhőalapú szolgáltatás, amely a SQL Server naplózási technológia alapján van engedélyezve a felügyelt példányok számára. A LRS olyan esetekben kell használni, amikor az Azure adatáttelepítési szolgáltatás (DMS) nem használható, ha további szabályozásra van szükség, vagy ha kevés a tolerancia az állásidőnél.

## <a name="when-to-use-log-replay-service"></a>Mikor kell használni a log Replay szolgáltatást

Abban az esetben, ha az [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) -t nem lehet áttelepítésre használni, a LRS Cloud Service közvetlenül használható a PowerShell, a CLI-parancsmagok vagy az API segítségével, hogy manuálisan felépítse és összehangolja az adatbázisok áttelepítését az SQL felügyelt példányára. 

A következő esetekben érdemes megfontolni a LRS Cloud Service használatát:
- További szabályozásra van szükség az adatbázis-áttelepítési projekthez
- A rendszer az áttelepítési átváltás kevés tűréshatárt tartalmaz
- A DMS-végrehajtható fájl nem telepíthető a környezetben
- A DMS-végrehajtható fájlhoz nem tartozik fájlok hozzáférése az adatbázis biztonsági mentéséhez
- Nem érhető el hozzáférés a gazda operációs rendszerhez, vagy nincs rendszergazdai jogosultsága
- Nem lehet megnyitni a hálózati portokat a környezetből az Azure-ba

> [!NOTE]
> Az adatbázisok SQL Serverról SQL-re felügyelt példányra történő migrálása ajánlott automatizált módon az Azure DMS használatával történik. Ez a szolgáltatás ugyanazt a LRS Cloud Service-t használja a háttérben, ha a napló szállítása nem helyreállítási módban történik. Érdemes manuálisan használni a LRS-t a Migrálás megszervezéséhez olyan esetekben, amikor az Azure DMS nem támogatja teljes mértékben a forgatókönyveit.

## <a name="how-does-it-work"></a>Hogyan működik?

A LRS használatával az adatbázisok felhőbe való áttelepítésére szolgáló egyéni megoldás létrehozásához több, a diagramon látható és az alábbi táblázatban leírt lépések szükségesek.

Az áttelepítés a SQL Server teljes biztonsági mentését teszi lehetővé, és az ELLENŐRZŐÖSSZEG engedélyezve van, és a biztonságimásolat-fájlok másolása az Azure Blob Storageba. A LRS az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti közvetítő tárolóként használatos.

A LRS figyeli az Azure-Blob Storage a teljes biztonsági mentés visszaállítása után hozzáadott új különbözeti vagy naplózott biztonsági másolatok esetében, és automatikusan visszaállítja a hozzáadott új fájlokat. Az SQL felügyelt példányon visszaállított biztonságimásolat-fájlok állapota figyelhető a szolgáltatás használatával, és szükség esetén a folyamat is megszakítható.

A LRS nem igényel konkrét biztonságimásolat-fájl elnevezési konvenciót, mivel az Azure Blob Storageba helyezett összes fájlt megvizsgálja, és a biztonsági mentési láncot csak a fájlok fejlécének olvasásával készíti el. Az adatbázisok "visszaállítási" állapotban vannak az áttelepítési folyamat során, mivel azok nem [helyreállítási](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) módban lettek visszaállítva, és nem használhatók olvasásra vagy írásra, amíg az áttelepítési folyamat teljesen be nem fejeződik. 

Több adatbázis áttelepítésekor az egyes adatbázisok biztonsági másolatait külön mappába kell helyezni az Azure Blob Storage-ban. A LRS külön kell elindítani az egyes adatbázisokhoz, és különböző elérési utakat kell megadni az Azure Blob Storage-mappák elkülönítéséhez. 

A LRS az automatikus kiegészítés vagy a folyamatos mód használatával indítható el. Ha az automatikus kiegészítési módban elindult, az áttelepítés automatikusan megtörténik, amikor a rendszer visszaállította az utolsó biztonságimásolat-fájl nevét. A folyamatos módban való indításkor a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat, és az áttelepítés csak a manuális átváltás végezhető el. Azt javasoljuk, hogy a manuális átváltás csak a végső log-tail biztonsági mentés után legyen végrehajtva, és az SQL felügyelt példányon visszaállítottként jelenjen meg. Az utolsó átváltás lépéssel az adatbázis online állapotba kerül, és olvasási és írási használatra elérhető az SQL felügyelt példányain.

Miután leállította az LRS-t, automatikusan az automatikus kiegészítésben vagy manuálisan a átváltás-on, a visszaállítási folyamat nem folytatható olyan adatbázis esetében, amely az SQL felügyelt példányon online állapotú. Ha az áttelepítést automatikus kiegészítéssel, vagy manuálisan a átváltás-n keresztül szeretné visszaállítani a további biztonságimásolat-fájlokat, az adatbázist törölni kell, és a teljes biztonsági mentési láncot teljesen vissza kell állítani a LRS újraindításával.

  ![A log Replay szolgáltatás összehangolása című témakör ismerteti az SQL felügyelt példányát](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Művelet | Részletek |
| :----------------------------- | :------------------------- |
| **1. másolja az adatbázis biztonsági másolatait SQL Serverból az Azure Blob Storageba**. | – A teljes, különbözeti és naplózott biztonsági másolatok másolása SQL Serverról az Azure Blob Storage-tárolóba a [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)vagy a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával. <br />– Bármilyen fájlnevet használhat, mivel a LRS nem igényel egyedi fájlnevek elnevezési konvenciót.<br />– Több adatbázis áttelepítése esetén külön mappa szükséges az egyes adatbázisokhoz. |
| **2. Indítsa el a LRS szolgáltatást a felhőben**. | – A szolgáltatás választható parancsmagokkal indítható el: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start parancsmagok](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> – A LRS külön kell elindítania minden egyes adatbázishoz, amely az Azure Blob Storage egy másik biztonsági mentési mappájára mutat. <br />– Az indítás után a szolgáltatás biztonsági mentést készít az Azure Blob Storage tárolóból, és megkezdi az SQL felügyelt példányon való visszaállítását.<br /> – Ha a LRS folyamatos módban lett elindítva, az összes eredetileg feltöltött biztonsági másolat visszaállítása után a szolgáltatás figyeli a mappába feltöltött összes új fájlt, és folyamatosan alkalmazza a naplókat a LSN lánc alapján, amíg a szolgáltatás le nem áll. |
| **2,1. figyelje a művelet előrehaladását**. | – A visszaállítási művelet előrehaladását a választható vagy a parancsmagok segítségével figyelheti: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show parancsmagok](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. szükség esetén Stop\abort a műveletet**. | – Ha az áttelepítési folyamatot meg kell szakítani, a művelet a választható parancsmagokkal állítható le: <br /> PowerShell [leállítása – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) parancsmagok. <br /><br />– Ez azt eredményezi, hogy a rendszer törli az SQL felügyelt példányon visszaállított adatbázis törlését. <br />-Ha leállt, a LRS nem folytatható egy adatbázis esetében. Az áttelepítési folyamatot teljesen újra kell indítani. |
| **3. Ha elkészült, átváltás a felhőbe**. | – Miután az összes biztonsági mentést visszaállította az SQL felügyelt példányára, végezze el a átváltás a LRS befejezési műveletnek a választható parancsmagokkal történő kezdeményezésével: <br />A PowerShell [kész – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) parancsmagok. <br /><br />– Ennek hatására a rendszer leállítja a LRS szolgáltatást, és az SQL felügyelt példányon az olvasási és írási használatra az adatbázis online állapotba kerül.<br /> – Az alkalmazás kapcsolódási karakterláncának átirányítása SQL Serverról SQL felügyelt példányra. |

## <a name="requirements-for-getting-started"></a>Az első lépésekhez szükséges követelmények

### <a name="sql-server-side"></a>SQL Server oldalon
- SQL Server 2008-2019
- Adatbázisok teljes biztonsági mentése (egy vagy több fájl)
- Különbözeti biztonsági másolat (egy vagy több fájl)
- Napló biztonsági mentése (nincs felosztva a tranzakciós naplófájlra)
- Az **ellenőrzőösszeget engedélyezni** kell a biztonsági mentésekhez (kötelező)

### <a name="azure-side"></a>Azure-oldal
- PowerShell az. SQL modul verziója 2.16.0 vagy újabb verzió (az Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/)[telepítése](https://www.powershellgallery.com/packages/Az.Sql/)vagy használata)
- CLI-verzió 2.19.0 vagy újabb ([telepítés](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Azure Blob Storage-tároló kiépítve
- SAS biztonsági jogkivonat csak **olvasási** és **listázási** engedélyekkel a blob Storage-tárolóhoz

### <a name="migrating-multiple-databases"></a>Több adatbázis áttelepítése
- A különböző adatbázisok biztonsági mentési fájljait külön mappákba kell helyezni az Azure Blob Storage-ban.
- A LRS külön kell elindítani az Azure Blob Storage megfelelő mappájára mutató összes adatbázishoz.
- A LRS legfeljebb 100 egyidejű visszaállítási folyamatot tud támogatni egy SQL-alapú felügyelt példányon.

### <a name="azure-rbac-permissions-required"></a>Azure RBAC-engedélyek szükségesek
A LRS a megadott ügyfeleken keresztüli végrehajtásához a következő Azure-szerepkörök egyikére van szükség:
- Előfizetés tulajdonosi szerepköre, vagy
- [Felügyelt példány közreműködői](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) szerepköre, vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Ajánlott eljárások

Az ajánlott eljárások a következők:
- [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) futtatásával ellenőrizheti, hogy az adatbázisok készen állnak-e az SQL felügyelt példányra való áttelepítésre. 
- Egyetlen fájl helyett a teljes és a különbözeti biztonsági mentéseket több fájlba is eloszthatja.
- A biztonsági másolatok tömörítésének engedélyezése.
- A parancsfájlokat a Cloud Shell használatával hajthatja végre, mivel a rendszer mindig frissíti a legújabb parancsmagokat.
- Tervezze meg az áttelepítés befejezését 47 órán belül, mivel a LRS szolgáltatás elindult. Ez a türelmi időszak megakadályozza a rendszer által felügyelt szoftverek javítását a LRS elindítása után.

> [!IMPORTANT]
> - Az LRS használatával visszaállított adatbázis nem használható, amíg az áttelepítési folyamat be nem fejeződik. Ennek az az oka, hogy a rendszer az alapul szolgáló technológiát nem helyreállítási módban állítja vissza.
> - A LRS az SQL felügyelt példány és a piacon futó SQL Server-kiszolgálók közötti különbség miatt nem támogatja az adatbázisokhoz való olvasási hozzáférést engedélyező KÉSZENLÉTi visszaállítási módot.
> - Ha az áttelepítést az automatikus kiegészítésen keresztül vagy manuális átváltás végezte el, az áttelepítési folyamat véglegesítve lesz, mivel a LRS nem támogatja a visszaállítás folytatását.

## <a name="steps-to-execute"></a>Végrehajtandó lépések

### <a name="make-backups-on-the-sql-server"></a>Biztonsági másolatok készítése a SQL Server

A SQL Server biztonsági mentése a következő két lehetőség valamelyikével végezhető el:

- Készítsen biztonsági másolatot a helyi lemezes tárhelyre, majd töltse fel a fájlokat az Azure Blob Storageba, ha a környezete korlátozza a közvetlen biztonsági mentést az Azure Blob Storageba.
- Ha a környezet és a biztonsági eljárások lehetővé teszik, a biztonsági mentést közvetlenül az Azure-ba Blob Storage a "TO URL" lehetőséggel a T-SQL-ben. 

Állítsa be azokat az adatbázisokat, amelyeket át szeretne telepíteni a teljes helyreállítási módba, hogy engedélyezze a naplók biztonsági mentését.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Az adatbázis teljes, diff és log biztonsági mentésének manuális megadásához a helyi tárolóban használja az alábbi, példaként megadott T-SQL-parancsfájlokat. Győződjön meg arról, hogy az ELLENŐRZŐÖSSZEG beállítás engedélyezve van, mivel ez a LRS kötelező követelménye.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

A helyi tárolóba biztonsági másolattal elkészített fájlokat fel kell tölteni az Azure-Blob Storageba. Ha a vállalati házirend lehetővé teszi, hogy a biztonsági mentések közvetlenül az Azure-Blob Storage is elérhetők legyenek, a következő oktatóanyagban van dokumentálva: az [Azure Blob Storage szolgáltatás használata a SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). Ha ezt az alternatív módszert használja, ügyeljen arra, hogy az összes biztonsági mentés engedélyezve legyen az ELLENŐRZŐÖSSZEG beállítással.

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Biztonsági másolatok másolása SQL Serverról az Azure-ba Blob Storage

A következő módszerek egyike használható a biztonsági másolatok a blob Storage-ba történő feltöltésére az adatbázisok áttelepítése felügyelt példányra az LRS használatával:
- SQL Server natív [biztonsági mentés használata URL-](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) funkciókra.
- A [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)vagy a [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) használatával tölthet fel biztonsági másolatokat egy blob-tárolóba.
- A Azure Portal Storage Explorer használata.

### <a name="create-azure-blob-and-sas-authentication-token"></a>Azure Blob-és SAS-hitelesítési jogkivonat létrehozása

Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti biztonsági mentési fájlok köztes tárolóként használatos. Az Azure Blob Storage-tároló létrehozásához kövesse az alábbi lépéseket:

1. [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Crete a blob tároló](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) a Storage-fiókon belül

A blob-tároló létrehozása után csak az alábbi lépéseket követve hozhat létre SAS-hitelesítési jogkivonatot az olvasási és listázási engedélyekkel:

1. Hozzáférés a Storage-fiókhoz Azure Portal használatával
2. Navigáljon Storage Explorer
3. BLOB-tárolók kibontása
4. Kattintson a jobb gombbal a blob-tárolóra
5. Válassza a közös hozzáférési aláírás beolvasása lehetőséget
6. Válassza ki a jogkivonat lejárati időkeretét. Győződjön meg arról, hogy a jogkivonat az áttelepítés időtartamára érvényes.
    - Vegye figyelembe, hogy a jogkivonat időzónája és az SQL felügyelt példánya eltérő lehet. Győződjön meg arról, hogy az SAS-token rendelkezik a megfelelő időtartammal az időzónák figyelembe vétele érdekében. Ha lehetséges, állítsa az időzónát a tervezett áttelepítési időszak korábbi és későbbi időpontjára.
8. Győződjön meg arról, hogy csak az olvasási és a lista engedélyei vannak kijelölve
9. Kattintson a Létrehozás gombra
10. Másolja a tokent a kérdőjel "?" és újabb verziók után. Az SAS-token általában "SV = 2020-10" kezdetű az URI-ban a kódban való használatra.

> [!IMPORTANT]
> - Az Azure Blob Storage SAS-jogkivonatára vonatkozó engedélyeket csak olvasási és listázási jogosultsággal kell elvégeznie. Ha más engedélyek is megadják az SAS hitelesítési jogkivonatot, a LRS szolgáltatás elindítása sikertelen lesz. Ezek a biztonsági követelmények a tervek szerint vannak kialakítva.
> - A jogkivonatnak rendelkeznie kell a megfelelő időtartammal. Ügyeljen arra, hogy a jogkivonat és a felügyelt példány közötti időzónák figyelembe legyenek véve.
> - Győződjön meg róla, hogy a tokent az "SV = 2020-10..." kezdettől kezdve a karakterlánc végéig másolja.

### <a name="log-in-to-azure-and-select-subscription"></a>Jelentkezzen be az Azure-ba, és válassza az előfizetés lehetőséget

Az Azure-ba való bejelentkezéshez használja a következő PowerShell-parancsmagot:

```powershell
Login-AzAccount
```

Válassza ki a megfelelő előfizetést, ahol az SQL felügyelt példánya a következő PowerShell-parancsmaggal rendelkezik:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Az áttelepítés elindítása

A rendszer elindítja az áttelepítést a LRS szolgáltatás elindításával. A szolgáltatás az automatikus kiegészítés vagy a folyamatos mód használatával indítható el. Ha az automatikus kiegészítési módban elindult, az áttelepítés automatikusan megtörténik, amikor a rendszer visszaállította az utolsó biztonságimásolat-fájlt. Ehhez a beállításhoz a Start parancsnak kell megadnia az utolsó biztonságimásolat-fájl fájlnevét. Ha a LRS folyamatos módban van elindítva, a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat, és az áttelepítés csak a manuális átváltás végezhető el. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS indítása automatikus kiegészítési módban

A LRS szolgáltatás automatikus kiegészítési módban való indításához használja a következő PowerShell-vagy CLI-parancsokat. Adja meg az utolsó biztonságimásolat-fájl nevét a-LastBackupName paraméterrel. A legutóbbi biztonságimásolat-fájl nevének visszaállítása után a szolgáltatás automatikusan kezdeményez egy átváltás.

LRS elindítása automatikus kiegészítési módban – PowerShell-példa:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

LRS elindítása automatikus kiegészítési módban – CLI példa:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS elindítása folyamatos módban

LRS elindítása folyamatos módban – PowerShell-példa:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

LRS elindítása folyamatos módban – CLI-példa:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> A LRS elindítása után a rendszer a következő 47 órán belül megállítja a rendszerfelügyelt szoftverek javítását. Ezen ablak elküldése után a következő automatizált szoftverfrissítés automatikusan leállítja a folyamatban lévő LRS. Ebben az esetben az áttelepítés nem folytatható, és a rendszer újból el kell indítania a semmiből. 

## <a name="monitor-the-migration-progress"></a>Az áttelepítési folyamat figyelése

Az áttelepítési művelet előrehaladásának figyeléséhez használja a következő PowerShell-parancsot:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az áttelepítési művelet előrehaladásának figyeléséhez használja a következő CLI-parancsot:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Az áttelepítés leállítása

Ha le kell állítania az áttelepítést, használja a következő parancsmagokat. Az áttelepítés leállítása törli az adatbázis visszaállítása az SQL felügyelt példányon, amely miatt nem lehet folytatni az áttelepítést.

Az áttelepítési folyamat stop\abort használja a következő PowerShell-parancsot:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az áttelepítési folyamat stop\abort használja az alábbi CLI-parancsot:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Az áttelepítés befejezése (folyamatos mód)

Ha a LRS folyamatos módban van elindítva, miután biztosította, hogy az összes biztonsági másolat vissza lett állítva, a átváltás kezdeményezése befejezi az áttelepítést. A átváltás befejezésekor a rendszer áttelepíti az adatbázist, és készen áll az olvasási és írási hozzáférésre.

Az áttelepítési folyamat LRS folyamatos módban való végrehajtásához használja a következő PowerShell-parancsot:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Az áttelepítési folyamat LRS folyamatos módban való végrehajtásához használja az alábbi CLI-parancsot:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="troubleshooting"></a>Hibaelhárítás

Miután elindította a LRS, használja a figyelési parancsmagokat (Get-azsqlinstancedatabaselogreplay vagy az_sql_midb_log_replay_show) a művelet állapotának megtekintéséhez. Ha egy kis idő elteltével nem sikerül elindítani a LRS, tekintse meg a leggyakoribb problémákat:
- Az adatbázis biztonsági mentése a SQL Server az **ellenőrzőösszeg** beállítással történt?
- Az SAS-jogkivonatra vonatkozó engedélyek csak a LRS szolgáltatáshoz vannak **beolvasva** és **listázva** ?
- A (z) "?" kérdőjel után a LRS SAS-tokenje a következőhöz hasonló tartalommal kezdődik: "SV = 2020-02-10..."? 
- Az SAS- **jogkivonat érvényességi** ideje érvényes az áttelepítés elkezdésének és befejezésének időablakára? Vegye figyelembe, hogy az SQL felügyelt példányhoz és az SAS-tokenhez használt különböző **időzónák** miatt eltérések lehetnek. Próbálja meg újragenerálni az SAS-jogkivonatot, és az aktuális dátum előtt és után kiterjesztheti az időablak token érvényességét.
- Az adatbázis neve, az erőforráscsoport neve és a felügyelt példány neve helyesen van-e írva?
- Ha a LRS automatikus kiegészítés módban lett elindítva, érvényes fájlnév volt a megadott utolsó biztonságimásolat-fájlhoz?

## <a name="next-steps"></a>Következő lépések
- További információ az [SQL Server migrálása SQL felügyelt példányra](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- További információ a [SQL Server és az Azure SQL felügyelt példányai közötti különbségekről](transact-sql-tsql-differences-sql-server.md).
- További információ az [Azure-ba migrált](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)számítási feladatokkal kapcsolatos ajánlott eljárásokról.
