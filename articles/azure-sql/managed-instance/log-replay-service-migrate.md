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
ms.date: 02/17/2021
ms.openlocfilehash: 07da1d5dbfd6384751e01f5becccd7b7b4c97e99
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095227"
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

> [!NOTE]
> Az adatbázisok SQL Serverról SQL-re felügyelt példányra történő migrálása ajánlott automatizált módon az Azure DMS használatával történik. Ez a szolgáltatás ugyanazt a LRS Cloud Service-t használja a háttérben, ha a napló szállítása nem helyreállítási módban történik. Érdemes manuálisan használni a LRS-t a Migrálás megszervezéséhez olyan esetekben, amikor az Azure DMS nem támogatja teljes mértékben a forgatókönyveit.

## <a name="how-does-it-work"></a>Hogyan működik?

Az adatbázisok felhőbe való áttelepítésére szolgáló egyéni megoldás LRS használatával történő áttelepítéséhez a diagramon több, az alábbi táblázatban ismertetett lépéseket kell elvégeznie.

Az áttelepítés a teljes adatbázis biztonsági mentését vonja maga után SQL Server és másolja a biztonságimásolat-fájlokat az Azure Blob Storageba. A LRS az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti közvetítő tárolóként használatos.

A LRS figyeli az Azure-Blob Storage a teljes biztonsági mentés visszaállítása után hozzáadott új különbözeti vagy naplózott biztonsági másolatok esetében, és automatikusan visszaállítja a hozzáadott új fájlokat. Az SQL felügyelt példányon visszaállított biztonságimásolat-fájlok állapota figyelhető a szolgáltatás használatával, és szükség esetén a folyamat is megszakítható. Az áttelepítési folyamat során visszaállított adatbázisok visszaállítási módban lesznek, és nem használhatók olvasásra vagy írásra, amíg a folyamat be nem fejeződik.

A LRS az automatikus kiegészítés vagy a folyamatos mód használatával indítható el. Ha az automatikus kiegészítési módban elindult, az áttelepítés automatikusan megtörténik, amikor a rendszer visszaállította az utolsó biztonságimásolat-fájlt. A folyamatos módban való indításkor a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat, és az áttelepítés csak a manuális átváltás végezhető el. Az átváltás utolsó lépéseként elérhetővé válik az adatbázisok olvasási és írási használatra az SQL felügyelt példányain. 

  ![A log Replay szolgáltatás összehangolása című témakör ismerteti az SQL felügyelt példányát](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Művelet | Részletek |
| :----------------------------- | :------------------------- |
| **1. másolja az adatbázis biztonsági másolatait SQL Serverból az Azure Blob Storageba**. | – A teljes, különbözeti és naplózott biztonsági másolatok másolása SQL Serverról az Azure Blob Storage-tárolóba a [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) vagy a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával. <br />– Több adatbázis áttelepítése esetén külön mappa szükséges az egyes adatbázisokhoz. |
| **2. Indítsa el a LRS szolgáltatást a felhőben**. | – A szolgáltatás választható parancsmagokkal indítható el: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start parancsmagok](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /><br />– Az indítás után a szolgáltatás biztonsági mentést készít az Azure Blob Storage tárolóból, és megkezdi a SQLManaged-példányon való visszaállítását. <br /> – Ha a rendszer az összes eredetileg feltöltött biztonsági mentést visszaállítja, a szolgáltatás figyeli a mappába feltöltött összes új fájlt, és folyamatosan alkalmazza a naplókat a LSN lánc alapján, amíg a szolgáltatás le nem áll. |
| **2,1. figyelje a művelet előrehaladását**. | – A visszaállítási művelet előrehaladását a választható vagy a parancsmagok segítségével figyelheti: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show parancsmagok](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. szükség esetén Stop\abort a műveletet**. | – Ha az áttelepítési folyamatot meg kell szakítani, a művelet a választható parancsmagokkal állítható le: <br /> PowerShell [leállítása – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) parancsmagok. <br /><br />– Ez az SQL felügyelt példányon visszaállított adatbázis törlését eredményezi. <br />-Ha leállt, a LRS-adatbázis nem folytatható. Az áttelepítési folyamatot teljesen újra kell indítani. |
| **3. Ha elkészült, átváltás a felhőbe**. | -Ha az összes biztonsági mentést visszaállította az SQL mnaged-példányra, hajtsa végre a átváltás a LRS befejezési műveletnek egy választható API-hívással vagy parancsmagokkal való elindításával: <br />A PowerShell [kész – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) parancsmagok. <br /><br />– Ennek hatására a rendszer leállítja a LRS szolgáltatást, és a felügyelt példányon található adatbázis helyreállítva lesz. <br />– Az alkalmazás kapcsolódási karakterláncának átirányítása SQL Serverról SQL felügyelt példányra. <br />– A művelet befejezéséről szóló adatbázis a felhőben található R/W műveletekhez érhető el. |

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

## <a name="best-practices"></a>Ajánlott eljárások

Az ajánlott eljárások a következők:
- [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) futtatásával ellenőrizheti, hogy az adatbázisok nem lesznek áttelepítve az SQL felügyelt példányára. 
- Egyetlen fájl helyett a teljes és a különbözeti biztonsági mentéseket több fájlba is eloszthatja.
- A biztonsági másolatok tömörítésének engedélyezése.
- A parancsfájlokat a Cloud Shell használatával hajthatja végre, mivel a rendszer mindig frissíti a legújabb parancsmagokat.
- Tervezze meg az áttelepítés befejezését 47 órán belül, mivel a LRS szolgáltatás elindult.

> [!IMPORTANT]
> - Az LRS használatával visszaállított adatbázis nem használható, amíg az áttelepítési folyamat be nem fejeződik. Ennek az az oka, hogy az alapul szolgáló technológia nem helyreállítási módban naplózza a szállítást.
> - A LRS nem támogatja a naplózási szolgáltatás KÉSZENLÉTi módját, mert az SQL felügyelt példánya és a legújabb, piaci SQL Server verziója közötti különbségek miatt nem támogatott.

## <a name="steps-to-execute"></a>Végrehajtandó lépések

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Biztonsági másolatok másolása SQL Serverról az Azure-ba Blob Storage

A következő két módszer használható a biztonsági másolatok másolására a blob Storage-ba az adatbázisok áttelepítése felügyelt példányra az LRS használatával:
- SQL Server natív [biztonsági mentés használata URL-](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) funkciókra.
- A biztonsági másolatok másolása blob-tárolóba a [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)vagy a [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer)használatával. 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Azure Blob-és SAS-hitelesítési jogkivonat létrehozása

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
7. Győződjön meg arról, hogy csak az olvasási és a lista engedélyei vannak kijelölve
8. Kattintson a Létrehozás gombra
9. Másolja a jogkivonatot az URI-ban a kódban használt "SV =" kezdetű értékre.

> [!IMPORTANT]
> Az Azure Blob Storage SAS-jogkivonatára vonatkozó engedélyeket csak olvasási és listázási jogosultsággal kell elvégeznie. Ha más engedélyek is megadják az SAS hitelesítési jogkivonatot, a LRS szolgáltatás elindítása sikertelen lesz. Ezek a biztonsági követelmények a tervek szerint vannak kialakítva.

## <a name="log-in-to-azure-and-select-subscription"></a>Jelentkezzen be az Azure-ba, és válassza az előfizetés lehetőséget

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

LRS elindítása automatikus kiegészítési módban – CLI példa:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS elindítása folyamatos módban

LRS elindítása folyamatos módban – PowerShell-példa:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

LRS elindítása folyamatos módban – CLI-példa:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> A LRS elindítása után a rendszer a következő 47 órán belül megállítja a rendszerfelügyelt szoftverek javítását. Ezen ablak elküldése után a következő automatizált szoftverfrissítés automatikusan leállítja a folyamatban lévő LRS. Ebben az esetben az áttelepítés nem folytatható, és a rendszer újból el kell indítania a semmiből. 

## <a name="monitor-the-migration-progress"></a>Az áttelepítési folyamat figyelése

Az áttelepítési művelet előrehaladásának figyeléséhez használja a következő PowerShell-parancsot:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az áttelepítési művelet előrehaladásának figyeléséhez használja a következő CLI-parancsot:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Az áttelepítés leállítása

Ha le kell állítania az áttelepítést, használja a következő parancsmagokat. Az áttelepítés leállítása törli az adatbázis visszaállítása az SQL felügyelt példányon, amely miatt nem lehet folytatni az áttelepítést.

Az áttelepítési folyamat stop\abort használja a következő PowerShell-parancsot:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az áttelepítési folyamat stop\abort használja az alábbi CLI-parancsot:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Az áttelepítés befejezése (folyamatos mód)

Ha a LRS folyamatos módban van elindítva, miután biztosította, hogy az összes biztonsági másolat vissza lett állítva, a átváltás kezdeményezése befejezi az áttelepítést. A átváltás befejezésekor a rendszer áttelepíti az adatbázist, és készen áll az olvasási és írási hozzáférésre.

Az áttelepítési folyamat LRS folyamatos módban való végrehajtásához használja a következő PowerShell-parancsot:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Az áttelepítési folyamat LRS folyamatos módban való végrehajtásához használja az alábbi CLI-parancsot:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Következő lépések
- További információ az [SQL Server migrálása SQL felügyelt példányra](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- További információ a [SQL Server és az Azure SQL felügyelt példányai közötti különbségekről](transact-sql-tsql-differences-sql-server.md).
- További információ az [Azure-ba migrált](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)számítási feladatokkal kapcsolatos ajánlott eljárásokról.
