---
title: A felügyelt példányok közötti replikáció konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a tranzakciós replikációt egy Azure SQL felügyelt példány közzétevője/terjesztője és egy SQL felügyelt példány előfizetője között.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690685"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Oktatóanyag: két felügyelt példány közötti replikáció konfigurálása

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A tranzakciós replikáció lehetővé teszi az adatok replikálását az egyik adatbázisból egy másikba SQL Server vagy az [Azure SQL felügyelt példányain](sql-managed-instance-paas-overview.md). Az SQL felügyelt példánya lehet közzétevő, terjesztő vagy előfizető a replikációs topológiában. Lásd: [tranzakciós replikációs konfigurációk](replication-transactional-overview.md#common-configurations) az elérhető konfigurációkhoz. 

A tranzakciós replikáció jelenleg nyilvános előzetes verzióban érhető el a felügyelt SQL-példányhoz. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Felügyelt példány konfigurálása replikációs közzétevőként és terjesztőként.
> - Felügyelt példány konfigurálása replikációs előfizetőként.

![Replikálás két felügyelt példány között](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Ez az oktatóanyag egy tapasztalt közönség számára készült, és feltételezi, hogy a felhasználó ismeri az Azure-beli felügyelt példányok és SQL Server virtuális gépek üzembe helyezését és csatlakozását. 


> [!NOTE]
> - Ez a cikk a [tranzakciós replikáció](/sql/relational-databases/replication/transactional/transactional-replication) használatát ismerteti az Azure SQL felügyelt példányain. Nem kapcsolódik a [feladatátvételi csoportokhoz](../database/auto-failover-group-overview.md), az Azure SQL felügyelt példányának szolgáltatása, amely lehetővé teszi az egyes példányok teljes olvasható replikáinak létrehozását. A [tranzakciós replikáció feladatátvételi csoportokkal való](replication-transactional-overview.md#with-failover-groups)konfigurálásakor további szempontokat is figyelembe kell venni.



## <a name="requirements"></a>Követelmények

Ha úgy konfigurálja az SQL felügyelt példányt, hogy közzétevőként és/vagy terjesztőként működjön, a következők szükségesek:

- A közzétevő felügyelt példánya ugyanazon a virtuális hálózaton van, mint a terjesztő és az előfizető, vagy a [VPN-átjárók](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mind a három entitás virtuális hálózatai között vannak konfigurálva. 
- A kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.
- Egy Azure Storage-fiók megosztása a replikációs munkakönyvtárhoz.
- Az 445-as (TCP kimenő) port a felügyelt példányok NSG biztonsági szabályaiban van megnyitva az Azure-fájlmegosztás eléréséhez.  Ha hibát tapasztal `failed to connect to azure storage <storage account name> with os error 53` , hozzá kell adnia egy kimenő szabályt a megfelelő SQL felügyelt példány alhálózatának NSG.

## <a name="1---create-a-resource-group"></a>1 – erőforráscsoport létrehozása

A [Azure Portal](https://portal.azure.com) használatával hozzon létre egy erőforráscsoportot a névvel `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2 – felügyelt példányok létrehozása

A [Azure Portal](https://portal.azure.com) használatával hozzon létre két [SQL felügyelt példányt](instance-create-quickstart.md) ugyanazon a virtuális hálózaton és alhálózaton. Nevezze el például a két felügyelt példányt:

- `sql-mi-pub` (a véletlenszerűség néhány karakterrel együtt)
- `sql-mi-sub` (a véletlenszerűség néhány karakterrel együtt)

A felügyelt példányokhoz való [kapcsolódáshoz konfigurálnia kell egy Azure-beli virtuális gépet](connect-vm-instance-configure.md) is. 

## <a name="3---create-an-azure-storage-account"></a>3 – Azure Storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](../../storage/common/storage-account-create.md#create-a-storage-account) a munkakönyvtár számára, majd hozzon létre egy [fájlmegosztást](../../storage/files/storage-how-to-create-file-share.md) a Storage-fiókon belül. 

Másolja a fájlmegosztás elérési útját a (z) formátumban: `\\storage-account-name.file.core.windows.net\file-share-name`

Például: `\\replstorage.file.core.windows.net\replshare`

Másolja a Storage-hozzáférési kulcsokat a formátumba: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Például: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

További információ: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 – közzétevő adatbázis létrehozása

Kapcsolódjon a `sql-mi-pub` felügyelt példányhoz SQL Server Management Studio használatával, és futtassa a következő Transact-SQL (T-SQL) kódot a közzétevő adatbázis létrehozásához:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 – előfizetői adatbázis létrehozása

Kapcsolódjon a `sql-mi-sub` felügyelt példányhoz SQL Server Management Studio használatával, és futtassa az alábbi T-SQL-kódot az üres előfizetői adatbázis létrehozásához:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 – eloszlás konfigurálása

Kapcsolódjon a `sql-mi-pub` felügyelt példányhoz SQL Server Management Studio segítségével, és futtassa az alábbi T-SQL-kódot a terjesztési adatbázis konfigurálásához.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – a közzétevő konfigurálása a terjesztő használatára

A közzétevő SQL felügyelt példányán `sql-mi-pub` módosítsa a lekérdezés végrehajtását [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) módra, és futtassa az alábbi kódot az új terjesztő közzétevőhöz való regisztrálásához.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Ügyeljen arra, hogy csak fordított perjelet ( `\` ) használjon a file_storage paraméterhez. A Forward perjel ( `/` ) használata hibát okozhat a fájlmegosztáshoz való csatlakozáskor.

Ez a parancsfájl egy helyi közzétevőt konfigurál a felügyelt példányon, hozzáadja a csatolt kiszolgálót, és létrehoz egy feladatot a SQL Server ügynökhöz.

## <a name="8---create-publication-and-subscriber"></a>8 – kiadvány és előfizető létrehozása

A [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mód használatával futtassa az alábbi T-SQL-szkriptet az adatbázis replikációjának engedélyezéséhez, és konfigurálja a közzétevő, a terjesztő és az előfizető közötti replikálást.

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – ügynök paramétereinek módosítása

Az Azure SQL felügyelt példánya jelenleg néhány háttérbeli problémát tapasztal a replikációs ügynökökkel való kapcsolattal. A probléma megoldása közben a megkerülő megoldással növelheti a replikációs ügynökök bejelentkezési időtúllépési értékét.

A bejelentkezés időtúllépésének növeléséhez futtassa a következő T-SQL-parancsot a közzétevőn:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Futtassa újra az alábbi T-SQL-parancsot a bejelentkezési időkorlát alapértelmezett értékre való visszaállításához:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

A módosítások érvénybe léptetéséhez indítsa újra mindhárom ügynököt.

## <a name="10---test-replication"></a>10 – a replikáció tesztelése

Miután konfigurálta a replikálást, tesztelheti úgy, hogy új elemeket szúr be a közzétevőbe, és figyeli a módosításokat az előfizetőnek.

A következő T-SQL-kódrészlet futtatásával tekintheti meg az előfizető sorait:

```sql
select * from dbo.ReplTest
```

Futtassa az alábbi T-SQL-kódrészletet további sorok beszúrásához a közzétevőn, majd jelölje be újra a sorokat az előfizetőn.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiadvány eldobásához futtassa az alábbi T-SQL-parancsot:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

A replikálási lehetőség az adatbázisból való eltávolításához futtassa a következő T-SQL-parancsot:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

A közzététel és a terjesztés letiltásához futtassa az alábbi T-SQL-parancsot:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Az Azure-erőforrások tisztításához [törölje az SQL felügyelt példányának erőforrásait az erőforráscsoporthoz](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) , majd törölje az erőforráscsoportot `SQLMI-Repl` . 

## <a name="next-steps"></a>Következő lépések

További információkat is megtudhat a [tranzakciós replikálásról az Azure SQL felügyelt példányával](replication-transactional-overview.md) kapcsolatban, vagy megismerheti az [SQL felügyelt példányok közzétevője/terjesztője és az Azure-beli virtuális gép SQL-előfizetője](replication-two-instances-and-sql-server-configure-tutorial.md)közötti replikáció konfigurálását.