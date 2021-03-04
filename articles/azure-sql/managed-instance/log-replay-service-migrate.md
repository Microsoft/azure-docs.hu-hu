---
title: Adatbázisok migrálása az SQL felügyelt példányára a log Replay szolgáltatással
description: Ismerje meg, hogyan migrálhat adatbázisokat SQL Serverról SQL felügyelt példányra a log Replay szolgáltatás használatával
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 74403b7ec1469ce7cdaadc9931eb5ac95f55f6f5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096836"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Adatbázisok migrálása SQL Serverról SQL felügyelt példányra a log Replay szolgáltatással (előzetes verzió)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan konfigurálni az adatbázis-áttelepítést SQL Server 2008-2019-ről SQL felügyelt példányra a jelenleg nyilvános előzetes verzióban elérhető log Replay Service (LRS) használatával. Ez egy felhőalapú szolgáltatás, amely a SQL Server naplózási technológia alapján van engedélyezve a felügyelt példányok számára. A LRS olyan esetekben kell használni, amikor összetett egyéni áttelepítések és hibrid architektúrák léteznek, ha további szabályozásra van szükség, ha kevés a leállás, vagy ha az Azure adatáttelepítési szolgáltatás (DMS) nem használható.

A DMS és a LRS ugyanazt a mögöttes áttelepítési technológiát és ugyanazokat az API-kat használja. A LRS felszabadításával továbbra is lehetővé tesszük az összetett egyéni Migrálás és a hibrid architektúra helyszíni üzemeltetését. SQL Server és SQL felügyelt példányok.

## <a name="when-to-use-log-replay-service"></a>Mikor kell használni a log Replay szolgáltatást

Abban az esetben, ha az [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) -t nem lehet áttelepítésre használni, a LRS Cloud Service közvetlenül használható a PowerShell, a CLI-parancsmagok vagy az API segítségével, hogy manuálisan felépítse és összehangolja az adatbázisok áttelepítését az SQL felügyelt példányára. 

A következő esetekben érdemes megfontolni a LRS Cloud Service használatát:
- További szabályozásra van szükség az adatbázis-áttelepítési projekthez
- A rendszer az áttelepítési átváltás kevés tűréshatárt tartalmaz
- A DMS-végrehajtható fájl nem telepíthető a környezetben
- A DMS-végrehajtható fájlhoz nem tartozik fájlok hozzáférése az adatbázis biztonsági mentéséhez
- Nem érhető el hozzáférés a gazda operációs rendszerhez, vagy nincs rendszergazdai jogosultsága
- Nem lehet megnyitni a hálózati portokat a környezetből az Azure-ba
- A biztonsági mentések közvetlenül az Azure Blob Storage az URL-cím beállítás használatával tárolódnak
- A különbözeti biztonsági mentések használata szükséges

> [!NOTE]
> Az adatbázisok SQL Serverról SQL-re felügyelt példányra történő migrálása ajánlott automatizált módon az Azure DMS használatával történik. Ez a szolgáltatás ugyanazt a LRS Cloud Service-t használja a háttérben, ha a napló szállítása nem helyreállítási módban történik. Érdemes manuálisan használni a LRS-t a Migrálás megszervezéséhez olyan esetekben, amikor az Azure DMS nem támogatja teljes mértékben a forgatókönyveit.

## <a name="how-does-it-work"></a>Hogyan működik?

A LRS használatával az adatbázisok felhőbe való áttelepítésére szolgáló egyéni megoldás létrehozásához több, a diagramon látható és az alábbi táblázatban leírt lépések szükségesek.

Az áttelepítés a SQL Server teljes biztonsági mentését teszi lehetővé, és az ELLENŐRZŐÖSSZEG engedélyezve van, és a biztonságimásolat-fájlok másolása az Azure Blob Storageba. A LRS az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti közvetítő tárolóként használatos.

A LRS figyeli az Azure-Blob Storage a teljes biztonsági mentés visszaállítása után hozzáadott új különbözeti vagy naplózott biztonsági másolatok esetében, és automatikusan visszaállítja a hozzáadott új fájlokat. Az SQL felügyelt példányon visszaállított biztonságimásolat-fájlok állapota figyelhető a szolgáltatás használatával, és szükség esetén a folyamat is megszakítható.

A LRS nem igényel konkrét biztonságimásolat-fájl elnevezési konvenciót, mivel az Azure Blob Storageba helyezett összes fájlt megvizsgálja, és a biztonsági mentési láncot csak a fájlok fejlécének olvasásával készíti el. Az adatbázisok "visszaállítási" állapotban vannak az áttelepítési folyamat során, mivel azok nem [helyreállítási](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) módban lettek visszaállítva, és nem használhatók olvasásra vagy írásra, amíg az áttelepítési folyamat teljesen be nem fejeződik. 

Több adatbázis áttelepítésekor az egyes adatbázisok biztonsági másolatait külön mappába kell helyezni az Azure Blob Storage-ban. A LRS külön kell elindítani az egyes adatbázisokhoz, és különböző elérési utakat kell megadni az Azure Blob Storage-mappák elkülönítéséhez. 

A LRS az automatikus kiegészítés vagy a folyamatos mód használatával indítható el. Ha az automatikus kiegészítési módban elindult, az áttelepítés automatikusan megtörténik, amikor a rendszer visszaállította az utolsó biztonságimásolat-fájl nevét. A folyamatos módban való indításkor a szolgáltatás folyamatosan visszaállítja a hozzáadott új biztonságimásolat-fájlokat, és az áttelepítés csak a manuális átváltás végezhető el. Azt javasoljuk, hogy a manuális átváltás csak a végső log-tail biztonsági mentés után legyen végrehajtva, és az SQL felügyelt példányon visszaállítottként jelenjen meg. Az utolsó átváltás lépéssel az adatbázis online állapotba kerül, és olvasási és írási használatra elérhető az SQL felügyelt példányain.

Miután leállította az LRS-t, automatikusan az automatikus kiegészítésben vagy manuálisan a átváltás-on, a visszaállítási folyamat nem folytatható olyan adatbázis esetében, amely az SQL felügyelt példányon online állapotú. Ha az áttelepítést automatikus kiegészítéssel, vagy manuálisan a átváltás-n keresztül szeretné visszaállítani a további biztonságimásolat-fájlokat, az adatbázist törölni kell, és a teljes biztonsági mentési láncot teljesen vissza kell állítani a LRS újraindításával.

   :::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="A log Replay szolgáltatás összehangolása című témakör ismerteti az SQL felügyelt példányát" border="false":::
    
| Művelet | Részletek |
| :----------------------------- | :------------------------- |
| **1. másolja az adatbázis biztonsági másolatait SQL Serverból az Azure Blob Storageba**. | – A teljes, különbözeti és naplózott biztonsági másolatok másolása SQL Serverról az Azure Blob Storage-tárolóba a [Azcopy](/azure/storage/common/storage-use-azcopy-v10)vagy a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával. <br />– Bármilyen fájlnevet használhat, mivel a LRS nem igényel egyedi fájlnevek elnevezési konvenciót.<br />– Több adatbázis áttelepítése esetén külön mappa szükséges az egyes adatbázisokhoz. |
| **2. Indítsa el a LRS szolgáltatást a felhőben**. | – A szolgáltatás választható parancsmagokkal indítható el: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> – A LRS külön kell elindítania minden egyes adatbázishoz, amely az Azure Blob Storage egy másik biztonsági mentési mappájára mutat. <br />– Az indítás után a szolgáltatás biztonsági mentést készít az Azure Blob Storage tárolóból, és megkezdi az SQL felügyelt példányon való visszaállítását.<br /> – Ha a LRS folyamatos módban lett elindítva, az összes eredetileg feltöltött biztonsági másolat visszaállítása után a szolgáltatás figyeli a mappába feltöltött összes új fájlt, és folyamatosan alkalmazza a naplókat a LSN lánc alapján, amíg a szolgáltatás le nem áll. |
| **2,1. figyelje a művelet előrehaladását**. | – A visszaállítási művelet előrehaladását a választható vagy a parancsmagok segítségével figyelheti: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show parancsmagok](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. szükség esetén Stop\abort a műveletet**. | – Ha az áttelepítési folyamatot meg kell szakítani, a művelet a választható parancsmagokkal állítható le: <br /> PowerShell [Stop-azsqlinstancedatabaselogreplay]/PowerShell/Module/az.SQL/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) parancsmagok. <br /><br />– Ez azt eredményezi, hogy a rendszer törli az SQL felügyelt példányon visszaállított adatbázis törlését. <br />-Ha leállt, a LRS nem folytatható egy adatbázis esetében. Az áttelepítési folyamatot teljesen újra kell indítani. |
| **3. Ha elkészült, átváltás a felhőbe**. | – Állítsa le az alkalmazást és a munkaterhelést. Végezze el az utolsó log-tail biztonsági mentést, és töltse fel az Azure Blob Storageba.<br /> – Fejezze be a átváltás a LRS befejezési műveletnek a választható parancsmagokkal való elindításával: <br />A PowerShell [kész – azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) parancsmagok. <br /><br />– Ennek hatására a rendszer leállítja a LRS szolgáltatást, és az SQL felügyelt példányon az olvasási és írási használatra az adatbázis online állapotba kerül.<br /> – Az alkalmazás kapcsolódási karakterláncának átirányítása SQL Serverról SQL felügyelt példányra. |

## <a name="requirements-for-getting-started"></a>Az első lépésekhez szükséges követelmények

### <a name="sql-server-side"></a>SQL Server oldalon
- SQL Server 2008-2019
- Adatbázisok teljes biztonsági mentése (egy vagy több fájl)
- Különbözeti biztonsági másolat (egy vagy több fájl)
- Napló biztonsági mentése (nincs felosztva a tranzakciós naplófájlra)
- Az **ellenőrzőösszeget engedélyezni** kell a biztonsági mentésekhez (kötelező)

### <a name="azure-side"></a>Azure-oldal
- PowerShell az. SQL modul verziója 2.16.0 vagy újabb verzió (az Azure [Cloud Shell](/azure/cloud-shell/)[telepítése](https://www.powershellgallery.com/packages/Az.Sql/)vagy használata)
- CLI-verzió 2.19.0 vagy újabb ([telepítés](/cli/azure/install-azure-cli))
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
- [Data Migration Assistant](/sql/dma/dma-overview) futtatásával ellenőrizheti, hogy az adatbázisok készen állnak-e az SQL felügyelt példányra való áttelepítésre. 
- Egyetlen fájl helyett a teljes és a különbözeti biztonsági mentéseket több fájlba is eloszthatja.
- A biztonsági másolatok tömörítésének engedélyezése.
- A parancsfájlokat a Cloud Shell használatával hajthatja végre, mivel a rendszer mindig frissíti a legújabb parancsmagokat.
- Tervezze meg az áttelepítés befejezését 47 órán belül, mivel a LRS szolgáltatás elindult. Ez a türelmi időszak megakadályozza a rendszer által felügyelt szoftverek javítását a LRS elindítása után.

> [!IMPORTANT]
> - Az LRS használatával visszaállított adatbázis nem használható, amíg az áttelepítési folyamat be nem fejeződik.
> - A LRS nem támogatja az adatbázisok írásvédett elérését az áttelepítés során.
> - Az áttelepítés befejezése után az áttelepítési folyamat véglegesítve lesz, mivel a LRS nem támogatja a visszaállítás folytatását.

## <a name="steps-to-execute"></a>Végrehajtandó lépések

### <a name="make-backups-on-the-sql-server"></a>Biztonsági másolatok készítése a SQL Server

A SQL Server biztonsági mentése a következő két lehetőség valamelyikével végezhető el:

- Készítsen biztonsági másolatot a helyi lemezes tárhelyre, majd töltse fel a fájlokat az Azure Blob Storageba, ha a környezete korlátozza a közvetlen biztonsági mentést az Azure Blob Storageba.
- Ha a környezet és a biztonsági eljárások lehetővé teszik, a biztonsági mentést közvetlenül az Azure-ba Blob Storage a "TO URL" lehetőséggel a T-SQL-ben. 

Állítsa be azokat az adatbázisokat, amelyeket át szeretne telepíteni a teljes helyreállítási módba, hogy engedélyezze a naplók biztonsági mentését.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Az adatbázis teljes, diff és log biztonsági mentésének manuális megadásához a helyi tárolóban használja az alábbi, példaként megadott T-SQL-parancsfájlokat. Győződjön meg arról, hogy az ELLENŐRZŐÖSSZEG beállítás engedélyezve van, mivel ez a LRS kötelező követelménye.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Azure Blob Storage létrehozása

Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti biztonsági mentési fájlok köztes tárolóként használatos. Az alábbi lépéseket követve hozzon létre egy új Storage-fiókot és egy BLOB-tárolót a Storage-fiókon belül:

1. [Tárfiók létrehozása](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crete a blob tároló](../../storage/blobs/storage-quickstart-blobs-portal.md) a Storage-fiókon belül

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Biztonsági másolatok másolása SQL Serverról az Azure-ba Blob Storage

A következő módszerek egyike használható a biztonsági másolatok a blob Storage-ba történő feltöltésére az adatbázisok áttelepítése felügyelt példányra az LRS használatával:
- A [Azcopy](/azure/storage/common/storage-use-azcopy-v10)vagy a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) használatával tölthet fel biztonsági másolatokat egy blob-tárolóba.
- A Azure Portal Storage Explorer használata.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Biztonsági másolatok készítése SQL Server közvetlenül az Azure-ba Blob Storage

Ha a vállalati és hálózati házirend lehetővé teszi, a másik lehetőség az, hogy a biztonsági másolatokat közvetlenül az Blob Storage Azure-ba SQL Server a natív [biztonsági mentés URL-címére](/sql/relational-databases/backup-restore/sql-server-backup-to-url) való SQL Server használatával. Ha ezt a lehetőséget választja, a biztonsági mentések a helyi tárolón, és az Azure-Blob Storage való feltöltése nem szükséges.

Első lépésként ehhez a művelethez a SAS-hitelesítési tokent kell létrehozni az Azure Blob Storagehoz, és a jogkivonatot importálni kell a SQL Serverba. A második lépés az, hogy a biztonsági mentéseket a T-SQL URL-címére vonatkozó beállítással hozza. Győződjön meg arról, hogy az összes biztonsági mentés engedélyezve van a CHEKSUM beállítással.

Az Azure Blob Storage-ra való biztonsági mentést az alábbi mintakód alapján teheti meg. Ez a példa nem tartalmazza az SAS-token importálására vonatkozó utasításokat. Részletes utasítások, beleértve az SAS-token SQL Server történő létrehozását és importálását a következő oktatóanyagban: az [Azure Blob Storage szolgáltatás használata SQL Serverokkal](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Azure Blob Storage SAS-hitelesítés előállítása a LRS

Az Azure Blob Storage a SQL Server és az SQL felügyelt példánya közötti biztonsági mentési fájlok köztes tárolóként használatos. A LRS szolgáltatás általi használatra elő kell állítani a SAS hitelesítési tokent a List és az írásvédett engedélyek használatával. Ez lehetővé teszi az LRS szolgáltatás számára az Azure-Blob Storage elérését, és a biztonságimásolat-fájlok használatával visszaállíthatja őket az SQL felügyelt példányain. Az alábbi lépéseket követve hozhatja ki az SAS-hitelesítést a LRS használatára:

1. A Storage Explorer elérése Azure Portal

2. BLOB-tárolók kibontása

3. Kattintson a jobb gombbal a blob-tárolóra, és válassza a közös hozzáférési aláírás beolvasása elemet.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Log Replay szolgáltatás – közös hozzáférésű aláírás beolvasása":::

4. Válassza ki a jogkivonat lejárati időkeretét. Győződjön meg arról, hogy a jogkivonat az áttelepítés időtartamára érvényes.

5. Válassza ki a token-UTC vagy a helyi idő időzónáját

   - A jogkivonat időzónája és az SQL felügyelt példánya eltérő lehet. Győződjön meg arról, hogy az SAS-token rendelkezik a megfelelő időtartammal az időzónák figyelembe vétele érdekében. Ha lehetséges, állítsa az időzónát a tervezett áttelepítési időszak korábbi és későbbi időpontjára.

6. Csak olvasási és listázási engedélyek kiválasztása

   - Nincs szükség más engedély kiválasztására, vagy egyéb módon a LRS nem fog tudni elindulni. Ezt a biztonsági követelményt a terv szerint kell megtervezni.

7. Kattintson a Létrehozás gombra

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Log Replay szolgáltatás – SAS hitelesítési jogkivonat előállítása":::

   Az SAS-hitelesítés a korábban megadott idő érvényességével jön létre. Szüksége lesz a generált jogkivonat URI-verziójára – ahogy az alábbi képernyőképen is látható.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Log Replay szolgáltatás – az URI közös hozzáférési aláírásának másolása":::

### <a name="copy-parameters-from-sas-token-generated"></a>Paraméterek másolása a létrehozott SAS-tokenből

Ahhoz, hogy megfelelően használhassa az SAS-tokent a LRS elindításához, meg kell értenünk a szerkezetét. A létrehozott SAS-jogkivonat URI-ja két részből áll:
- StorageContainerUri és 
- StorageContainerSasToken, a kérdőjel (?) karakterrel elválasztva, ahogy az alábbi képen is látható.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Log Replay szolgáltatás által generált SAS-hitelesítési URI-példa" border="false":::

- Az első rész a "https://" kezdetű, amíg a kérdőjel (?) a StorageContainerURI paraméterhez van felhasználva, amely a LRS bemenetként van táplálva. Ez a LRS információt nyújt az adatbázis-biztonságimásolat-fájlokat tároló mappáról.
- A második rész, amely a kérdőjel (?) után kezdődik, az "SP =" példában és egészen addig, amíg a sztring véget nem StorageContainerSasToken paraméternek. Ez a tényleges aláírt hitelesítési jogkivonat, amely a megadott idő időtartamára érvényes. Ennek a résznek nem feltétlenül kell az "SP =" kifejezéssel kezdődnie, ahogy az adott eset eltérő lehet.

A paramétereket a következőképpen másolja:

1. Másolja a token első részét a https://egészen addig, amíg a kérdőjel (?) be nem fejeződik, és StorageContainerUri paraméterként használja a PowerShellben vagy a CLI-ben a LRS elindításához az alábbi képernyőképen látható módon.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="A log Replay szolgáltatás StorageContainerUri-paraméterének másolása":::

2. Másolja a token második részét a kérdőjel (?) karaktertől kezdve egészen a karakterlánc végéig, és használja StorageContainerSasToken paraméterként a PowerShellben vagy a CLI-ben a LRS elindításához az alábbi képernyőképen látható módon.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="A log Replay szolgáltatás StorageContainerSasToken-paraméterének másolása":::

> [!IMPORTANT]
> - Az Azure Blob Storage SAS-jogkivonatára vonatkozó engedélyeket csak olvasási és listázási jogosultsággal kell elvégeznie. Ha más engedélyek is meg vannak adva az SAS hitelesítési jogkivonat számára, a LRS szolgáltatás indítása sikertelen lesz. Ezek a biztonsági követelmények a tervek szerint vannak kialakítva.
> - A jogkivonatnak rendelkeznie kell a megfelelő időtartammal. Ügyeljen arra, hogy a jogkivonat és a felügyelt példány közötti időzónák figyelembe legyenek véve.
> - Győződjön meg arról, hogy a PowerShell vagy a CLI StorageContainerUri paramétere a generált jogkivonat URI-ja alapján lett átmásolva, a https://kezdve egészen a kérdőjelig (?). Ne adja meg a kérdőjelet.
> Győződjön meg arról, hogy a CLI PowerShell-hez tartozó StorageContainerSasToken paraméter a generált jogkivonat URI-ja alapján lett átmásolva, a kérdőjeltől (?) kezdve a karakterlánc végéig. Ne adja meg a kérdőjelet.

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
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

LRS elindítása automatikus kiegészítési módban – CLI példa:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS elindítása folyamatos módban

LRS elindítása folyamatos módban – PowerShell-példa:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

LRS elindítása folyamatos módban – CLI-példa:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>A parancsfájlok LRS folyamatos üzemmódban indul

A PowerShell és a CLI-ügyfelek folyamatos módban kezdhetik a LRS, szinkronban vannak. Ez azt jelenti, hogy az ügyfelek megvárhatják, hogy az API-válasz sikeres vagy sikertelen legyen a feladatok elindításához. A várakozás során a parancs nem fogja visszaadni a vezérlést a parancssorba. Ha az áttelepítési élményt parancsfájlokkal futtatja, és a LRS Start parancsával azonnal vissza szeretné állítani a vezérlést a többi parancsfájlban, a PowerShellt háttérbeli feladatokként is végrehajthatja a-AsJob kapcsolóval. Például:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Amikor elindít egy háttérben futó feladatot, a feladatütemezés azonnal visszatér, még akkor is, ha a művelet hosszabb időt vesz igénybe. A munkamenet futtatása megszakítás nélkül folytathatja a munkát. A PowerShell háttérbeli feladatként való futtatásával kapcsolatos részletekért tekintse meg a [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) dokumentációját.

Hasonlóképpen, ha a parancssori felületet a Linuxon háttérbeli folyamatként szeretné elindítani, használja az LRS start parancs végén található "jel (&)" jelet.

```CLI
az sql midb log-replay start <required parameters> &
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

## <a name="functional-limitations"></a>Funkcionális korlátozások

A log Replay szolgáltatás (LRS) működési korlátai a következők:
- A visszaállítani kívánt adatbázis nem használható írásvédett hozzáférésre az áttelepítési folyamat során.
- A rendszer által felügyelt szoftveres javítások 47 órával a LRS elindítása óta le lesznek tiltva. Az idő lejárta után a következő szoftverfrissítés leállítja a LRS. Ilyen esetben a LRS újra kell indítani a semmiből.
- A LRS-nek szüksége van egy adatbázisra a SQL Serveron, hogy a biztonsági mentés engedélyezve legyen az ELLENŐRZŐÖSSZEG beállítással.
- A LRS által használandó SAS-tokent a teljes Azure Blob Storage-tárolóhoz kell létrehozni, és csak olvasási és listázási engedélyekkel kell rendelkeznie.
- A különböző adatbázisok biztonsági mentési fájljait külön mappákba kell helyezni az Azure Blob Storage-ban.
- A LRS külön kell elindítani minden olyan adatbázis esetében, amely külön mappákra mutat az Azure Blob Storage biztonsági mentési fájljaival.
- A LRS legfeljebb 100 egyidejű visszaállítási folyamatot tud támogatni egy SQL-alapú felügyelt példányon.

## <a name="troubleshooting"></a>Hibaelhárítás

Miután elindította a LRS, használja a figyelési parancsmagokat (Get-azsqlinstancedatabaselogreplay vagy az_sql_midb_log_replay_show) a művelet állapotának megtekintéséhez. Ha a LRS egy kis idő elteltével nem sikerül elindítani a hibát, ellenőrizze a leggyakoribb problémák némelyikét:
- Létezik olyan adatbázis, amelynek a neve megegyezik a SQL Server-ból áttelepíteni próbált SQL-névvel? Oldja meg az ütközést az adatbázisok egyikének átnevezésével.
- Az adatbázis biztonsági mentése a SQL Server az **ellenőrzőösszeg** beállítással történt?
- Az SAS-jogkivonatra vonatkozó engedélyek csak a LRS szolgáltatáshoz vannak **beolvasva** és **listázva** ?
- A (z) "?" kérdőjel után a LRS SAS-tokenje a következőhöz hasonló tartalommal kezdődik: "SV = 2020-02-10..."? 
- Az SAS- **jogkivonat érvényességi** ideje érvényes az áttelepítés elkezdésének és befejezésének időablakára? Az SQL felügyelt példányhoz és az SAS-tokenhez használt különböző **időzónák** miatt eltérések lehetnek. Próbálja meg újragenerálni az SAS-jogkivonatot, és az aktuális dátum előtt és után kiterjesztheti az időablak token érvényességét.
- Az adatbázis neve, az erőforráscsoport neve és a felügyelt példány neve helyesen van-e írva?
- Ha a LRS automatikus kiegészítés módban lett elindítva, érvényes fájlnév volt a megadott utolsó biztonságimásolat-fájlhoz?

## <a name="next-steps"></a>Következő lépések
- További információ az [SQL Server migrálása SQL felügyelt példányra](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- További információ a [SQL Server és az Azure SQL felügyelt példányai közötti különbségekről](transact-sql-tsql-differences-sql-server.md).
- További információ az [Azure-ba migrált](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)számítási feladatokkal kapcsolatos ajánlott eljárásokról.
