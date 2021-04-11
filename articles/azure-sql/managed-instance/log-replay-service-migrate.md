---
title: Adatbázisok migrálása az SQL felügyelt példányára a log Replay szolgáltatással
description: Ismerje meg, hogyan telepíthet át adatbázisokat SQL Serverról SQL felügyelt példányra a log Replay szolgáltatás használatával
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 8e78db5b9d496c2ac13c9f1214b386770c11e21e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075900"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Adatbázisok migrálása SQL Serverról SQL felügyelt példányra a log Replay szolgáltatás (előzetes verzió) használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan konfigurálni az adatbázis-áttelepítést SQL Server 2008-2019-ről az Azure SQL felügyelt példányára a log Replay szolgáltatás (LRS) használatával, jelenleg nyilvános előzetes verzióban. A LRS egy felhőalapú szolgáltatás, amely engedélyezve van az SQL felügyelt példányán, és SQL Server naplózási technológián alapul. 

A [Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) és a LRS ugyanazt a mögöttes áttelepítési technológiát és ugyanazokat az API-kat használják. A LRS kiadásával továbbra is lehetővé tesszük az összetett egyéni áttelepítéseket és a hibrid architektúrát a helyszíni SQL Server és az SQL felügyelt példánya között.

## <a name="when-to-use-log-replay-service"></a>Mikor kell használni a log Replay szolgáltatást

Ha nem tudja használni a Azure Database Migration Servicet az áttelepítéshez, a LRS közvetlenül a PowerShell, az Azure CLI-parancsmagok vagy az API-k használatával manuálisan hozhatja létre és hangolhatja össze az SQL felügyelt példányának adatbázis-áttelepítését. 

A következő esetekben érdemes megfontolni a LRS használatát:
- Az adatbázis-áttelepítési projektnek további vezérlésre van szüksége.
- Az áttelepítési átváltás kevés az állásidő.
- A Database Migration Service végrehajtható fájl nem telepíthető a környezetében.
- Az Database Migration Service végrehajtható fájlnak nincs fájlhoz való hozzáférése az adatbázis biztonsági mentéséhez.
- Nem érhető el hozzáférés a gazda operációs rendszerhez, vagy nincsenek rendszergazdai jogosultságok.
- A hálózati portok nem nyithatók meg a környezetből az Azure-ba.
- Hálózati sávszélesség-szabályozás vagy proxy-blokkolási probléma a környezetben.
- A biztonsági mentéseket közvetlenül az Azure Blob Storage tárolja a `TO URL` kapcsolón keresztül.
- Eltérő biztonsági másolatokat kell használnia.

> [!NOTE]
> Javasoljuk, hogy Database Migration Service segítségével automatizálja az adatbázisok áttelepítését SQL Serverról SQL felügyelt példányra. Ez a szolgáltatás ugyanazt a LRS Cloud Service-t használja a háttérben, a log Shipping `NORECOVERY` módban. Ha a Database Migration Service nem támogatja teljes mértékben a forgatókönyveit, érdemes manuálisan a LRS használatával összehangolni az áttelepítést.

## <a name="how-it-works"></a>Működés

Az adatbázisok felhőbe való áttelepítéséhez a LRS használatával egyéni megoldást kell kiépíteni, a szakasz későbbi részében látható ábra és táblázat szerint.

Az áttelepítés a teljes adatbázis biztonsági mentését teszi lehetővé a SQL Servereken `CHECKSUM` , és a biztonságimásolat-fájlok másolása az Azure Blob Storageba. A LRS a Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. A Blob Storage a SQL Server és az SQL felügyelt példánya között található közbenső tároló.

A LRS figyeli Blob Storage a teljes biztonsági mentés visszaállítása után hozzáadott új különbözeti vagy naplók biztonsági mentéseket. A LRS ezután automatikusan visszaállítja ezeket az új fájlokat. A szolgáltatással figyelheti az SQL felügyelt példányon visszaállított biztonságimásolat-fájlok állapotát, és szükség esetén leállíthatja a folyamatot.

A LRS nem igényel konkrét elnevezési konvenciót a biztonságimásolat-fájlokhoz. Megvizsgálja a Blob Storage elhelyezett összes fájlt, és létrehozza a biztonsági mentési láncot csak a fájlok fejlécének olvasásával. Az adatbázisok "visszaállítási" állapotban vannak az áttelepítési folyamat során. Az adatbázisok nem [helyreállítási](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) módban vannak visszaállítva, így nem használhatók olvasásra és írásra, amíg az áttelepítési folyamat be nem fejeződik. 

Ha több adatbázist telepít át, a következőket kell tennie:
 
- Az egyes adatbázisok biztonsági másolatait a Blob Storage egy külön mappájába helyezheti.
- A LRS külön kell elindítani az egyes adatbázisokhoz.
- Különböző elérési utakat válasszon Blob Storage mappák elkülönítéséhez. 

A LRS-t *automatikus kiegészítéssel* vagy *folyamatos* módban is elindíthatja. Ha automatikus kiegészítés módban indítja el, az áttelepítés automatikusan megtörténik, amikor a rendszer visszaállította az utolsó megadott biztonságimásolat-fájlt. Ha folyamatos módban indítja el a LRS, a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat, és az áttelepítés csak a manuális átváltás végezhető el. 

Azt javasoljuk, hogy a végső biztonsági mentés után manuálisan csökkentse a visszaállítást, és az SQL felügyelt példányon visszaállítottként jelenjen meg. Az utolsó átváltás lépéssel az adatbázis online állapotba kerül, és olvasási és írási használatra elérhető az SQL felügyelt példányain.

A LRS leállítása után automatikusan, automatikus kiegészítéssel vagy manuálisan, a átváltás-on keresztül, nem folytathatja a visszaállítási folyamatot olyan adatbázis esetében, amely online állapotú felügyelt példányon. Ha az áttelepítés befejezése után további biztonsági másolatokat szeretne visszaállítani az automatikus kiegészítés vagy a átváltás használatával, törölnie kell az adatbázist. A LRS újraindításával is vissza kell állítania a teljes biztonsági mentési láncot.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Az SQL felügyelt példányának log Replay szolgáltatásához szükséges lépéseket ismertető diagram." border="false":::
    
| Művelet | Részletek |
| :----------------------------- | :------------------------- |
| **1. másolja az adatbázis biztonsági másolatait SQL Serverról blob Storagera**. | A [Azcopy](../../storage/common/storage-use-azcopy-v10.md) vagy [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával teljes, különbözeti és naplózott biztonsági másolatokat másolhat SQL Serverről egy blob Storage-tárolóba. <br /><br />Használjon fájlneveket. A LRS nem igényel konkrét fájl-elnevezési konvenciót.<br /><br />Több adatbázis áttelepítése esetén minden adatbázishoz külön mappára van szükség. |
| **2. Indítsa el a LRS a felhőben**. | A szolgáltatást a következő parancsmagok közül választhatja újra: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) vagy Azure CLI ([az_sql_midb_log_replay_start parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> A LRS külön kell elindítania minden olyan adatbázis esetében, amely Blob Storage biztonsági mentési mappájára mutat. <br /><br /> A szolgáltatás elindítása után a Blob Storage tárolóról készít biztonsági másolatokat, és megkezdi a visszaállítást az SQL felügyelt példányán.<br /><br /> Ha a LRS folyamatos módban indult el, az összes eredetileg feltöltött biztonsági mentés visszaállítása után a szolgáltatás a mappába feltöltött összes új fájlt megtekinti. A szolgáltatás a napló sorszáma (LSN) lánc alapján folyamatosan alkalmazza a naplókat a Leállításig. |
| **2,1. a művelet előrehaladásának figyelése**. | A visszaállítási művelet előrehaladását a következő parancsmagok közül választhatja: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) vagy Azure CLI ([az_sql_midb_log_replay_show parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. szükség esetén állítsa le a műveletet**. | Ha le kell állítania az áttelepítési folyamatot, a következő parancsmagok közül választhat: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) vagy Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> A művelet leállítása törli azt az adatbázist, amelyet a felügyelt SQL-példányon kíván visszaállítani. A művelet leállítása után nem folytathatja az adatbázisok LRS. Újra kell indítania az áttelepítési folyamatot. |
| **3. Kivágás a felhőbe, ha elkészült**. | Állítsa le az alkalmazást és a munkaterhelést. Végezze el az utolsó log-tail biztonsági mentést, és töltse fel az Azure Blob Storageba.<br /><br /> Hajtsa végre a átváltás egy LRS-művelet elindításával a `complete` következő parancsmagokkal: PowerShell ([Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) vagy Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Ez a művelet leállítja a LRS, és az SQL felügyelt példányon az olvasási és írási használatra az adatbázis online állapotba kerül.<br /><br /> Az alkalmazás kapcsolódási karakterláncának átirányítása SQL Serverról SQL felügyelt példányra. Ezt a lépést saját kezűleg kell összehangolni az alkalmazás manuális kapcsolódási karakterláncának módosításával, vagy automatikusan (például ha az alkalmazás képes például egy tulajdonság vagy egy adatbázis kapcsolódási karakterláncának beolvasására). |

## <a name="requirements-for-getting-started"></a>Az első lépésekhez szükséges követelmények

### <a name="sql-server-side"></a>SQL Server oldalon
- SQL Server 2008-2019
- Adatbázisok teljes biztonsági mentése (egy vagy több fájl)
- Különbözeti biztonsági másolat (egy vagy több fájl)
- Napló biztonsági mentése (nincs felosztva tranzakciós naplófájlra)
- `CHECKSUM` biztonsági mentések engedélyezve (kötelező)

### <a name="azure-side"></a>Azure-oldal
- PowerShell az. SQL modul verziója 2.16.0 vagy újabb ( [Azure Cloud Shell](/azure/cloud-shell/)) keresztül[telepített](https://www.powershellgallery.com/packages/Az.Sql/) vagy elért
- Azure CLI-verzió 2.19.0 vagy újabb verziója ([telepített](/cli/azure/install-azure-cli))
- Azure Blob Storage-tároló kiépítve
- Közös hozzáférésű aláírás (SAS) biztonsági jogkivonat az Blob Storage tárolóhoz generált olvasási és listázási engedélyekkel

### <a name="migration-of-multiple-databases"></a>Több adatbázis áttelepítése
A különböző adatbázisokhoz tartozó biztonsági másolati fájlokat külön mappákban kell elhelyezni Blob Storageon.

Az egyes adatbázisokhoz külön LRS kell elindítania, ha a Blob Storage megfelelő mappájára mutat. A LRS legfeljebb 100 egyidejű visszaállítási folyamatot tud támogatni egyetlen felügyelt példányon.

### <a name="azure-rbac-permissions"></a>Azure RBAC engedélyek
A LRS a megadott ügyfeleken keresztül történő futtatása a következő Azure-szerepkörök egyikét igényli:
- Előfizetés tulajdonosi szerepköre
- [Felügyelt példány közreműködői](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) szerepköre
- Egyéni szerepkör a következő engedélyekkel: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Ajánlott eljárások

A következő ajánlott eljárásokat javasoljuk:
- [Data Migration Assistant](/sql/dma/dma-overview) futtatásával ellenőrizheti, hogy az adatbázisok készen állnak-e az SQL felügyelt példányra való áttelepítésre. 
- A teljes és a különbözeti biztonsági mentéseket több fájlba is eloszthatja egyetlen fájl használata helyett.
- A biztonsági másolatok tömörítésének engedélyezése.
- A parancsfájlokat a Cloud Shell használatával futtathatja, mivel a rendszer mindig frissíti a legújabb parancsmagokat.
- Tervezze meg az áttelepítés befejezését a LRS elindítása után 47 órán belül. Ez egy türelmi időszak, amely megakadályozza a rendszer által felügyelt szoftveres javítások telepítését.

> [!IMPORTANT]
> - A LRS-en keresztül visszaállított adatbázis nem használható, amíg az áttelepítési folyamat be nem fejeződik. 
> - A LRS nem támogatja az adatbázisok írásvédett elérését az áttelepítés során.
> - Az áttelepítés befejeződése után az áttelepítési folyamat véglegesítve lett, mert a LRS nem támogatja a visszaállítási folyamat folytatását.

## <a name="steps-to-execute"></a>Végrehajtandó lépések

### <a name="make-backups-of-sql-server"></a>SQL Server biztonsági mentések készítése

SQL Server biztonsági mentését a következő lehetőségek egyikével végezheti el:

- Készítsen biztonsági mentést a helyi lemezes tárhelyre, majd töltse fel a fájlokat az Azure Blob Storageba, ha a környezet korlátozza a közvetlen biztonsági mentéseket Blob Storagere.
- `TO URL`Ha a környezet és a biztonsági eljárások lehetővé teszik, biztonsági mentést készíthet közvetlenül blob Storage a T-SQL lehetőséggel. 

Állítsa be azokat az adatbázisokat, amelyeket át szeretne telepíteni a teljes helyreállítási módba, hogy engedélyezze a naplók biztonsági mentését.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Az adatbázis helyi tárolón való teljes, differenciált és naplózott biztonsági mentésének manuális elvégzéséhez használja az alábbi T-SQL-parancsfájlokat. Győződjön meg arról `CHECKSUM` , hogy a beállítás engedélyezve van, mert kötelező a LRS.

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

Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti biztonsági mentési fájlok köztes tárolóként használatos. Az alábbi lépéseket követve hozzon létre egy új Storage-fiókot és egy BLOB-tárolót a Storage-fiókon belül:

1. [Hozzon létre egy Storage-fiókot](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete a blob tároló](../../storage/blobs/storage-quickstart-blobs-portal.md) a Storage-fiókon belül.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Biztonsági másolatok másolása SQL Serverról Blob Storage

Az adatbázisok felügyelt példányra történő áttelepítése a LRS használatával a következő módszerekkel tölthetők fel a biztonsági másolatok Blob Storageba:
- Natív [biztonsági mentés használata az URL-](/sql/relational-databases/backup-restore/sql-server-backup-to-url) funkciók SQL Server
- A [Azcopy](../../storage/common/storage-use-azcopy-v10.md) vagy a [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) használata a biztonsági másolatok blob-tárolóba való feltöltéséhez
- Storage Explorer használata a Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Biztonsági másolatok SQL Server közvetlenül a Blob Storage
Ha a vállalati és hálózati házirendek lehetővé teszik, egy másik lehetőség, hogy a biztonsági másolatokat közvetlenül a SQL Server Blob Storage a SQL Server natív [biztonsági mentés URL-címére](/sql/relational-databases/backup-restore/sql-server-backup-to-url) történő másolásával. Ha ezt a lehetőséget választja, nem kell biztonsági mentést készítenie a helyi tárolón, és fel kell töltenie őket a Blob Storageba.

Első lépésként a művelethez a Blob Storage SAS-hitelesítési tokent kell előállítania, majd importálnia kell a tokent a SQL Serverba. A második lépés az, hogy a biztonsági mentéseket a `TO URL` T-SQL lehetőséggel hozza. Győződjön meg arról, hogy az összes biztonsági mentés engedélyezve van a `CHEKSUM` beállítással.

A következő mintakód lehetővé teszi, hogy a biztonsági mentések Blob Storage. Ez a példa nem tartalmazza az SAS-token importálására vonatkozó utasításokat. Az oktatóanyagban részletes útmutatást talál, amely azt is ismerteti, hogyan hozhatja SQL Server az SAS-tokent, és hogyan importálhatja őket az [Azure Blob Storage az SQL Server használatával](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

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

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Blob Storage SAS hitelesítési jogkivonat létrehozása a LRS

Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti biztonsági mentési fájlok köztes tárolóként használatos. A LRS csak a List és a Read engedélyekkel rendelkező SAS-hitelesítési tokent kell létrehoznia. A token lehetővé teszi a LRS számára a Blob Storage elérését, és a biztonságimásolat-fájlok használatával visszaállíthatja őket az SQL felügyelt példányain. 

A jogkivonat létrehozásához kövesse az alábbi lépéseket:

1. Storage Explorer megnyitása a Azure Portal.
2. Bontsa ki a **blob-tárolókat**.
3. Kattintson a jobb gombbal a blob-tárolóra, majd válassza a **közös hozzáférési aláírás beolvasása** elemet.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Képernyőkép, amely megjeleníti az s A s hitelesítési jogkivonat létrehozásához szükséges beállításokat.":::

4. Válassza ki a jogkivonat lejárati időkeretét. Győződjön meg arról, hogy a jogkivonat érvényes az áttelepítés időtartamára.
5. Válassza ki a token időzónáját: UTC vagy a helyi idő.
    
   > [!IMPORTANT]
   > A jogkivonat időzónája és a felügyelt példány eltérő lehet. Győződjön meg arról, hogy az SAS-jogkivonat rendelkezik a megfelelő időponttal, figyelembe véve az időzónákat. Ha lehetséges, állítsa az időzónát a tervezett áttelepítési időszak korábbi és későbbi időpontjára.
6. Válassza az **olvasási** és **listázási** engedélyek lehetőséget.

   > [!IMPORTANT]
   > Ne válasszon más engedélyeket. Ha így tesz, a LRS nem indul el. Ezt a biztonsági követelményt a terv szerint kell megtervezni.
7. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Képernyőkép, amely megjeleníti az s token lejáratának, időzónájának és engedélyeinek kiválasztásait a létrehozás gombbal együtt.":::

A SAS-hitelesítés a megadott időérvényességi idővel jön létre. Szüksége lesz a jogkivonat URI-verziójára, ahogy az alábbi képernyőképen is látható.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Képernyőkép, amely az S-S token U R I verziójának egy példáját mutatja be.":::

### <a name="copy-parameters-from-the-sas-token"></a>Paraméterek másolása az SAS-tokenből

Mielőtt a SAS-tokent használja a LRS elindításához, meg kell ismernie a szerkezetét. A generált SAS-jogkivonat URI-ja két, kérdőjel () karakterrel elválasztott részből áll `?` , ahogy az ebben a példában is látható:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Példa: U R I egy generált S token a log Replay szolgáltatáshoz." border="false":::

Az első, a kérdőjel () értéktől kezdődően a `https://` `?` `StorageContainerURI` LRS bemenetként megjelenő paraméterhez van használatban. Az adatbázis biztonsági mentési fájljait tároló mappával kapcsolatos LRS információkat biztosít.

A második rész a kérdőjel () után kezdődik, `?` és egészen a karakterlánc végéig haladva a `StorageContainerSasToken` paraméter. Ez a tényleges aláírt hitelesítési jogkivonat, amely a megadott idő időtartamára érvényes. Ennek a résznek nem feltétlenül kell kezdődnie, `sp=` ahogy az a példában látható. Az eset eltérő lehet.

Másolja a paramétereket az alábbiak szerint:

1. Másolja a token első részét, `https://` egészen addig, amíg a kérdőjel () be nem fejeződik `?` . A `StorageContainerUri` LRS megkezdéséhez használja a PowerShellben vagy az Azure CLI-ben paraméterként.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Képernyőkép, amely a jogkivonat első részének másolását mutatja be.":::

2. Másolja a token második részét a kérdőjeltől ( `?` ) kezdve egészen a karakterlánc végéig. A `StorageContainerSasToken` LRS megkezdéséhez használja a PowerShellben vagy az Azure CLI-ben paraméterként.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Képernyőkép, amely a jogkivonat második részének másolását mutatja be.":::
   
> [!NOTE]
> Ha a jogkivonat egy részét másolja, ne adja meg a kérdőjelet.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Jelentkezzen be az Azure-ba, és válasszon egy előfizetést

Az Azure-ba való bejelentkezéshez használja a következő PowerShell-parancsmagot:

```powershell
Login-AzAccount
```

Válassza ki a megfelelő előfizetést, ahol a felügyelt példány a következő PowerShell-parancsmag használatával található:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Az áttelepítés elindítása

A LRS megkezdésével elindítja az áttelepítést. A szolgáltatást akár automatikus kiegészítéssel, akár folyamatos módban is elindíthatja. 

Ha automatikus kiegészítési módot használ, az áttelepítés automatikusan befejeződik, amikor a rendszer visszaállította az utolsó megadott biztonságimásolat-fájlt. Ehhez a beállításhoz a Start parancsnak kell megadnia az utolsó biztonságimásolat-fájl fájlnevét. 

Folyamatos mód használata esetén a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat. Az áttelepítés csak a manuális átváltás végezhető el. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS indítása automatikus kiegészítési módban

A LRS automatikus kiegészítési módban való elindításához használja a következő PowerShell-vagy Azure CLI-parancsokat. Adja meg az utolsó biztonságimásolat-fájl nevét a `-LastBackupName` paraméter használatával. A megadott biztonságimásolat-fájlok utolsó visszaállítását követően a szolgáltatás automatikusan kezdeményez egy átváltás.

Az alábbi példa a LRS automatikus kiegészítési módban való elindítását szemlélteti a PowerShell használatával:

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

Az alábbi példa a LRS automatikus kiegészítési módban való elindítását szemlélteti az Azure CLI használatával:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS elindítása folyamatos módban

Az alábbi példa a LRS folyamatos módban való elindítását szemlélteti a PowerShell használatával:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Íme egy példa arra, hogy az Azure CLI használatával hogyan kell a LRS-t folyamatos módban elindítani:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

A PowerShell és a CLI-ügyfelek folyamatos módban kezdhetik a LRS, szinkronban vannak. Ez azt jelenti, hogy az ügyfelek megvárhatják, hogy az API-válasz sikeres vagy sikertelen legyen a feladatok elindításához. 

A várakozás során a parancs nem fogja visszaadni a vezérlést a parancssorba. Ha az áttelepítési élményt futtatja, és szüksége van a LRS Start parancsra, amely azonnal visszaadja a vezérlést a többi parancsfájlnak, a PowerShellt háttérbeli feladatokként futtathatja a `-AsJob` kapcsoló használatával. Például:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Amikor elindít egy háttérben futó feladatot, a feladatütemezés azonnal visszatér, még akkor is, ha a művelet hosszabb időt vesz igénybe. A munkamenet futtatása megszakítás nélkül folytathatja a munkát. A PowerShell háttérbeli feladatként való futtatásával kapcsolatos részletekért tekintse meg a [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) dokumentációját.

Hasonlóképpen, ha a Linuxon háttérbeli folyamatként szeretné elindítani az Azure CLI-parancsot, használja az `&` LRS start parancs végén található jelet ():

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> A LRS megkezdése után a rendszer által felügyelt szoftverek 47 óráig megállnak. Az ablak után a következő automatizált szoftver-javítás automatikusan leállítja a LRS. Ha ez történik, nem folytathatja a Migrálás folytatását, és újra kell indítania azt a semmiből. 

## <a name="monitor-the-migration-progress"></a>Az áttelepítési folyamat figyelése

Az áttelepítés PowerShellen keresztüli előrehaladásának figyeléséhez használja a következő parancsot:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az Azure CLI-n keresztüli áttelepítés előrehaladásának figyeléséhez használja a következő parancsot:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Az áttelepítés leállítása

Ha le kell állítania az áttelepítést, használja a következő parancsmagokat. Az áttelepítés leállítása törli az adatbázis visszaállítását az SQL felügyelt példányon, így az áttelepítés folytatása nem lehetséges.

Az áttelepítési folyamat PowerShellen keresztüli leállításához használja a következő parancsot:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Az áttelepítési folyamat Azure CLI-n keresztüli leállításához használja a következő parancsot:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Az áttelepítés befejezése (folyamatos mód)

Ha a LRS folyamatos módban indult el, miután az összes biztonsági mentést visszaállította, a átváltás elindításával befejezi az áttelepítést. A átváltás követően a rendszer áttelepíti az adatbázist, és készen áll az olvasási és írási hozzáférésre.

Az áttelepítési folyamat LRS folyamatos módban történő végrehajtásához a PowerShell használatával hajtsa végre a következő parancsot:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Ha az áttelepítési folyamatot LRS folyamatos módban szeretné elvégezni az Azure CLI-n keresztül, használja a következő parancsot:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funkcionális korlátozások

A LRS működési korlátai a következők:
- A visszaállítani kívánt adatbázis nem használható írásvédett hozzáférésre az áttelepítési folyamat során.
- A rendszer által felügyelt szoftveres javítások 47 órával a LRS elindítása után le vannak tiltva. Az időszak lejárta után a következő szoftverfrissítés leállítja a LRS. Ezután újra kell indítania a LRS-t.
- A LRS-nek szüksége van a SQL Server adatbázisainak biztonsági mentésére, ha `CHECKSUM` engedélyezve van a beállítás.
- A LRS által használt SAS-jogkivonatot a teljes Azure Blob Storage-tárolóhoz kell létrehozni, és csak olvasási és listázási engedélyekkel kell rendelkeznie.
- A különböző adatbázisok biztonsági mentési fájljait külön mappákba kell helyezni Blob Storageon.
- A LRS külön kell elindítani minden olyan adatbázis esetében, amely külön mappákra mutat, és a Blob Storage található biztonsági mentési fájlok.
- A LRS legfeljebb 100 egyidejű visszaállítási folyamatot tud támogatni egyetlen felügyelt példányon.

## <a name="troubleshooting"></a>Hibaelhárítás

A LRS megkezdése után a figyelési parancsmag ( `get-azsqlinstancedatabaselogreplay` vagy `az_sql_midb_log_replay_show` ) használatával megtekintheti a művelet állapotát. Ha a LRS egy kis idő elteltével nem indul el, és hibaüzenetet kap, ellenőrizze a leggyakoribb problémákat:

- Az SQL felügyelt példányain meglévő adatbázis neve megegyezik azzal, amelyet SQL Server szeretne áttelepíteni? Oldja meg az ütközést az adatbázisok egyikének átnevezésével.
- Az adatbázis biztonsági másolata SQL Server a `CHECKSUM` kapcsolón keresztül történt?
- A SAS-jogkivonat engedélyei csak a LRS-re vannak beolvasva és listázva?
- Másolta a LRS SAS-tokenjét a kérdőjel () után a következőhöz `?` hasonló tartalommal: `sv=2020-02-10...` ? 
- Az SAS-jogkivonat érvényességi ideje érvényes az áttelepítés elkezdésének és befejezésének időablakára? Előfordulhat, hogy az SQL felügyelt példányhoz és az SAS-tokenhez használt különböző időzónák miatt eltérések vannak. Próbálja meg újragenerálni az SAS-jogkivonatot, és az aktuális dátum előtt és után kiterjeszteni az időablak jogkivonat-érvényességét.
- Az adatbázis neve, az erőforráscsoport neve és a felügyelt példány neve helyesen van-e írva?
- Ha a LRS automatikus kiegészítési módban indult, a megadott utolsó biztonságimásolat-fájlhoz érvényes fájlnév volt?

## <a name="next-steps"></a>Következő lépések
- További információ a [SQL Servernak az SQL felügyelt példányra való áttelepítéséről](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- További információ a [SQL Server és az SQL felügyelt példányai közötti különbségekről](transact-sql-tsql-differences-sql-server.md).
- További információ az [Azure-ba migrált](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)számítási feladatokkal kapcsolatos ajánlott eljárásokról.
