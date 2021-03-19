---
title: Adatbázis migrálása SQL Serverról Azure arc-kompatibilis SQL felügyelt példányra
description: Adatbázis migrálása SQL Serverról Azure arc-kompatibilis SQL felügyelt példányra
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90939633"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrálás: SQL Server az Azure arc használatára képes SQL felügyelt példányhoz

Ez a forgatókönyv végigvezeti az adatbázisok SQL Server példányról az Azure SQL felügyelt példányra való áttelepítésének lépésein két különböző biztonsági mentési és visszaállítási módszer használatával.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Az Azure Blob Storage használata 

Az Azure Blob Storage használata az Azure arc használatára képes SQL felügyelt példányra való áttelepítéshez.

Ez a módszer az Azure Blob Storage-t használja ideiglenes tárolási helyként, amelyről biztonsági másolatot készíthet, majd visszaállíthatja a alkalmazást.

### <a name="prerequisites"></a>Előfeltételek

- [Azure Data Studio telepítése](install-client-tools.md)
- [Azure Storage Explorer telepítése](https://azure.microsoft.com/features/storage-explorer/)
- Azure-előfizetés

### <a name="step-1-provision-azure-blob-storage"></a>1. lépés: az Azure Blob Storage kiépítése

1. Kövesse az [Azure Blob Storage-fiók létrehozása](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal) című témakörben ismertetett lépéseket.
1. Azure Storage Explorer elindítása
1. [Jelentkezzen](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) be az Azure-ba az előző lépésben létrehozott blob Storage eléréséhez
1. Kattintson a jobb gombbal a blob Storage-fiókra, és válassza a **blob-tároló létrehozása** lehetőséget egy új tároló létrehozásához, ahol a biztonságimásolat-fájlt tárolni fogja

### <a name="step-2-get-storage-blob-credentials"></a>2. lépés: a Storage-blob hitelesítő adatainak beolvasása

1. Azure Storage Explorer kattintson a jobb gombbal az imént létrehozott blob-tárolóra, és válassza a **közös hozzáférésű aláírás beolvasása** elemet.

1. Az **olvasás**, **írás** és **lista** kiválasztása

1. Kattintson a **Létrehozás** elemre.

   Jegyezze fel az URI-t és a lekérdezési karakterláncot ebből a képernyőből. Ezekre a későbbi lépésekben szükség lesz. Kattintson a **copy (másolás** ) gombra a Jegyzettömbbe vagy OneNote-ba való mentéshez.

1. A **megosztott hozzáférés aláírása** ablakának lezárása.

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>3. lépés: az adatbázis-fájl biztonsági mentése az Azure Blob Storageba

Ebben a lépésben csatlakozni fogunk a forrás SQL Serverhoz, és létrehozjuk azon adatbázis biztonságimásolat-fájlját, amelyet át szeretnénk telepíteni az SQL felügyelt példányba – Azure arc.

1. Azure Data Studio elindítása
1. Kapcsolódjon ahhoz a SQL Server-példányhoz, amelyhez az SQL felügyelt példányának áttelepíteni kívánt adatbázisa van – Azure arc
1. Kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.
1. Készítse elő a lekérdezést a következő formátumban, és cserélje le azokat a helyőrzőket, amelyek a `<...>` megosztott hozzáférési aláírásban található információk használatával a korábbi lépésekben szerepelnek.  Ha kicserélte az értékeket, futtassa a lekérdezést.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Hasonlóképpen készítse elő a **biztonsági mentési adatbázis** parancsát a következőképpen: hozzon létre egy biztonságimásolat-fájlt a blob-tárolóba.  Ha kicserélte az értékeket, futtassa a lekérdezést.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Nyissa meg Azure Storage Explorer és ellenőrizze, hogy az előző lépésben létrehozott biztonságimásolat-fájl látható-e a blob-tárolóban.

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>4. lépés: az adatbázis visszaállítása az Azure Blob Storage-ból az SQL felügyelt példányára – Azure arc

1. Azure Data Studio jelentkezzen be, és kapcsolódjon a felügyelt SQL-példányhoz – Azure arc.
1. Bontsa ki a **rendszeradatbázisok** csomópontot, kattintson a jobb gombbal a **Master** adatbázisra, és válassza az **Új lekérdezés** lehetőséget.
1. A hitelesítő adatok létrehozásához a lekérdezés-szerkesztő ablakban készítse elő és futtassa ugyanazt a lekérdezést az előző lépésből.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Készítse elő és futtassa az alábbi parancsot a biztonságimásolat-fájl olvasható és érintetlen állapotának ellenőrzéséhez.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Készítse elő és futtassa az **adatbázis visszaállítása** parancsot a következőképpen, hogy visszaállítsa a biztonságimásolat-fájlt egy adatbázisra az SQL felügyelt példányán – Azure arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

További információ az URL-címre történő biztonsági mentésről:

[Biztonsági mentés URL-címekre](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Biztonsági mentés az URL-címre SQL Server Management Studio (SSMS) használatával](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>2. módszer: a biztonságimásolat-fájl másolása egy felügyelt Azure SQL-példányba – Azure arc Pod a kubectl használatával

Ebből a módszerből megtudhatja, hogyan hozhat létre egy tetszőleges módszerrel létrehozott biztonságimásolat-fájlt, majd a helyi tárolóba másolja azt az Azure SQL felügyelt példányok Pod-ban, így a tipikus fájlrendszerhez hasonlóan a Windows vagy Linux rendszerű számítógépeken is visszaállíthatók. Ebben az esetben a parancsot fogja használni a `kubectl cp` fájl egy helyről a pod fájlrendszerbe való másolásához.

### <a name="prerequisites"></a>Előfeltételek

- A kubectl telepítése és konfigurálása, hogy a Kubernetes-fürtre mutasson, ahol az Azure arc-adatszolgáltatások telepítve vannak
- Olyan eszközzel kell rendelkeznie, mint Azure Data Studio vagy SQL Server felügyeleti kiszolgáló telepítve van, és csatlakoztatva van a SQL Serverhoz, ahol létre kívánja hozni a biztonságimásolat-fájlt, vagy már létre van hozva egy meglévő. bak fájl a helyi fájlrendszerben.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>1. lépés: az adatbázis biztonsági mentése, ha még nem tette meg

Készítsen biztonsági másolatot a SQL Server-adatbázisról a helyi elérési útra, például a lemezre jellemző SQL Server biztonsági mentéshez:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>2. lépés: a biztonságimásolat-fájl másolása a pod fájlrendszerbe

Keresse meg azon Pod nevét, amelyben az SQL-példány telepítve van. Általában úgy kell kinéznie, mint `pod/<sqlinstancename>-0`

Az összes hüvely listájának lekérése a futtatásával:

 ```console
kubectl get pods -n <namespace of data controller>
```

Példa:

Másolja a biztonságimásolat-fájlt a helyi tárolóból a fürtben található SQL-Pod-ra.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>3. lépés: az adatbázis visszaállítása

A Restore parancs előkészítése és futtatása a biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára – Azure arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Példa:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Következő lépések

[További információ az Azure arc-kompatibilis SQL felügyelt példány szolgáltatásairól és képességeiről](managed-instance-features.md)

[Első lépések adatkezelő létrehozásával](create-data-controller.md)

[Már létrehozott egy adatvezérlőt? Azure arc használatára képes SQL felügyelt példány létrehozása](create-sql-managed-instance.md)