---
title: Az adattárak és a fájlmegosztás elérése Windows-hitelesítéssel
description: Megtudhatja, hogyan konfigurálhatja a SSIS-katalógust a Azure SQL Database-ben, és Azure-SSIS Integration Runtimeheti a Azure Data Factory-ben, hogy olyan csomagokat futtasson, amelyek hozzáférnek az adattárakhoz
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 77283596e4b0d7e25d757c9de76c284ca4f5d4f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98556393"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Adattárak és fájlmegosztások elérése Windows-hitelesítéssel, Azure-beli SSIS-csomagokból

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Windows-hitelesítéssel olyan adattárakhoz férhet hozzá, mint például az SQL-kiszolgálók, a fájlmegosztások, a Azure Files stb. a Azure Data Factory (ADF) Azure-SSIS Integration Runtimeon (IR) futó SSIS-csomagokból. Az adattárak lehetnek a helyszínen, az Azure Virtual Machines (VM) szolgáltatásban, vagy az Azure-ban felügyelt szolgáltatásként futnak. Ha a helyszínen vannak, csatlakoztatnia kell a Azure-SSIS IRt a helyszíni hálózathoz csatlakoztatott Virtual Networkhoz (Microsoft Azure Virtual Network), lásd: [csatlakozás Azure-SSIS IR egy Microsoft Azure Virtual Networkhoz](./join-azure-ssis-integration-runtime-virtual-network.md). Az adattárakat négy módon érheti el a Azure-SSIS IR futó SSIS-csomagokból származó Windows-hitelesítéssel:

| Kapcsolati módszer | Hatályos hatókör | Telepítési lépés | Hozzáférési módszer a csomagokban | A hitelesítőadat-készletek és a csatlakoztatott erőforrások száma | A csatlakoztatott erőforrások típusa | 
|---|---|---|---|---|---|
| Tevékenység szintű végrehajtási környezet beállítása | SSIS-csomag végrehajtása tevékenység | Konfigurálja a **Windows-hitelesítés** tulajdonságot úgy, hogy a "végrehajtás/Futtatás as" környezetet állítsa be, amikor SSIS-csomagokat futtat az ADF-folyamatokban végrehajtott SSIS-csomagként.<br/><br/> További információ: a [SSIS-csomag végrehajtása tevékenység konfigurálása](./how-to-invoke-ssis-package-ssis-activity.md). | Közvetlen hozzáférés a csomagokhoz, például UNC elérési út használata a fájlmegosztás vagy a Azure Files eléréséhez: `\\YourFileShareServerName\YourFolderName` vagy `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Csak egy hitelesítőadat-készlet támogatása az összes csatlakoztatott erőforráshoz | -Fájlmegosztás a helyszínen/Azure-beli virtuális gépeken<br/><br/> – Azure Files, lásd: [Azure-fájlmegosztás használata](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-kiszolgálók helyszíni/Azure-beli virtuális gépeken Windows-hitelesítéssel<br/><br/> – Egyéb erőforrások Windows-hitelesítéssel |
| Katalógus szintű végrehajtási környezet beállítása | Azure-SSIS IR, de a tevékenység szintű végrehajtási környezet beállításakor felül van bírálva (lásd fent) | Futtassa a SSISDB `catalog.set_execution_credential` tárolt eljárást a "végrehajtás/Futtatás as" környezet beállításához.<br/><br/> További információért tekintse meg az alábbi cikk további részeit. | Közvetlen hozzáférés a csomagokhoz, például UNC elérési út használata a fájlmegosztás vagy a Azure Files eléréséhez: `\\YourFileShareServerName\YourFolderName` vagy `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Csak egy hitelesítőadat-készlet támogatása az összes csatlakoztatott erőforráshoz | -Fájlmegosztás a helyszínen/Azure-beli virtuális gépeken<br/><br/> – Azure Files, lásd: [Azure-fájlmegosztás használata](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-kiszolgálók helyszíni/Azure-beli virtuális gépeken Windows-hitelesítéssel<br/><br/> – Egyéb erőforrások Windows-hitelesítéssel |
| Hitelesítő adatok megőrzése `cmdkey` parancs használatával | Azure-SSIS IR, de a tevékenység/katalógus szintű végrehajtási környezet beállításakor felül van bírálva (lásd fent) | Futtassa a `cmdkey` parancsot egy egyéni telepítési parancsfájlban ( `main.cmd` ) a Azure-SSIS IR kiépítés során, például ha fájlmegosztást, Azure Files vagy SQL Server használ:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey` vagy <br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> További információ: [a Azure-SSIS IR telepítőjének testreszabása](./how-to-configure-azure-ssis-ir-custom-setup.md). | Közvetlen hozzáférés a csomagokhoz, például UNC elérési út használata a fájlmegosztás vagy a Azure Files eléréséhez: `\\YourFileShareServerName\YourFolderName` vagy `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Több hitelesítőadat-készlet támogatása a különböző csatlakoztatott erőforrásokhoz | -Fájlmegosztás a helyszínen/Azure-beli virtuális gépeken<br/><br/> – Azure Files, lásd: [Azure-fájlmegosztás használata](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-kiszolgálók helyszíni/Azure-beli virtuális gépeken Windows-hitelesítéssel<br/><br/> – Egyéb erőforrások Windows-hitelesítéssel |
| Meghajtók csatlakoztatása a csomag végrehajtási idején (nem állandó) | /Csomag | Futtassa a parancsot a következőben `net use` : folyamat végrehajtása feladat, amelyet a rendszer a csomagokban a Control flow elején adott hozzá, például: `net use D: \\YourFileShareServerName\YourFolderName` | Fájlmegosztás elérése csatlakoztatott meghajtókon keresztül | Több meghajtó támogatása a különböző fájlmegosztás esetén | -Fájlmegosztás a helyszínen/Azure-beli virtuális gépeken<br/><br/> – Azure Files, lásd: [Azure-fájlmegosztás használata](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Ha a fenti módszerek egyikét sem használja a Windows-hitelesítéssel rendelkező adattárakhoz való hozzáféréshez, a Windows-hitelesítéstől függő csomagok nem férhetnek hozzájuk, és futási időben sikertelenek lesznek. 

A cikk további része azt ismerteti, hogyan lehet konfigurálni a SQL Database/SQL felügyelt példányban üzemeltetett SSIS-katalógust (SSISDB), hogy olyan Azure-SSIS IR csomagokat futtasson, amelyek Windows-hitelesítést használnak az adattárakhoz való hozzáféréshez. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Csak egy hitelesítő adatot lehet használni

Ha Windows-hitelesítést használ egy SSIS-csomagban, akkor csak egy hitelesítő adatot használhat. A cikkben ismertetett lépések végrehajtásakor megadott tartományi hitelesítő adatok minden csomag-végrehajtásra vonatkoznak – interaktív vagy ütemezett – a Azure-SSIS IR, amíg meg nem változtatja vagy el nem távolítja őket. Ha a csomagnak több, különböző hitelesítő adatokkal rendelkező adattárolóhoz kell csatlakoznia, érdemes megfontolnia a fenti alternatív módszereket.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Tartományi hitelesítő adatok megadása Windows-hitelesítéshez

Ha olyan tartományi hitelesítő adatokat szeretne biztosítani, amelyek lehetővé teszik, hogy a csomagok Windows-hitelesítéssel férhessenek hozzá a helyszíni adattárakhoz, tegye a következőket:

1. SQL Server Management Studio (SSMS) vagy más eszközzel csatlakozhat a SSISDB-t üzemeltető SQL Database/SQL felügyelt példányhoz. További információ: [Kapcsolódás a SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Ha a SSISDB-t aktuális adatbázisként kívánja megnyitni, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Futtassa a SSIS-csomagokat. A csomagok a megadott hitelesítő adatokkal férnek hozzá a helyszíni adattárakhoz a Windows-hitelesítés használatával.

### <a name="view-domain-credentials"></a>Tartományi hitelesítő adatok megtekintése

Az aktív tartományi hitelesítő adatok megtekintéséhez tegye a következőket:

1. A SSMS vagy más eszközzel kapcsolódjon a SSISDB-t üzemeltető SQL Database/SQL felügyelt példányhoz. További információ: [Kapcsolódás a SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Ha a SSISDB-t aktuális adatbázisként kívánja megnyitni, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és keresse meg a kimenetet:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Tartományi hitelesítő adatok törlése
A jelen cikkben leírtak szerint megadott hitelesítő adatok törléséhez és eltávolításához tegye a következőket:

1. A SSMS vagy más eszközzel kapcsolódjon a SSISDB-t üzemeltető SQL Database/SQL felügyelt példányhoz. További információ: [Kapcsolódás a SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Ha a SSISDB-t aktuális adatbázisként kívánja megnyitni, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Kapcsolódás SQL Server a helyszínen

Ha szeretné megnézni, hogy tud-e csatlakozni egy SQL Serverhoz a helyszínen, tegye a következőket:

1. A teszt futtatásához keresse meg a tartományhoz nem csatlakoztatott számítógépeket.

2. A tartományhoz nem csatlakoztatott számítógépen futtassa a következő parancsot a SSMS megkezdéséhez a használni kívánt tartományi hitelesítő adatokkal:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. A SSMS-ben győződjön meg róla, hogy tud-e csatlakozni a SQL Serverhoz a helyszínen.

### <a name="prerequisites"></a>Előfeltételek

Ha a helyszíni SQL Server az Azure-ban futó csomagokból szeretné elérni, tegye a következőket:

1.  A SQL Server Konfigurációkezelőban engedélyezze a TCP/IP protokollt.

2. Hozzáférés engedélyezése a Windows tűzfalon keresztül. További információ: [a Windows tűzfal konfigurálása SQL Server eléréséhez](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Csatlakoztassa a Azure-SSIS IRt egy olyan Microsoft Azure Virtual Networkhoz, amely a helyszíni SQL Serverhoz van csatlakoztatva.  További információ: [Join Azure-SSIS IR to a Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

4. A SSISDB `catalog.set_execution_credential` tárolt eljárás használatával adja meg a hitelesítő adatokat a jelen cikkben leírtak szerint.

## <a name="connect-to-a-file-share-on-premises"></a>Kapcsolódás a helyi fájlmegosztás-megosztáshoz

A következő műveletek végrehajtásával ellenőrizhető, hogy tud-e kapcsolódni a helyi fájlmegosztás számára:

1. A teszt futtatásához keresse meg a tartományhoz nem csatlakoztatott számítógépeket.

2. A tartományhoz nem csatlakoztatott számítógépen futtassa a következő parancsokat. Ezek a parancsok egy parancssori ablakot nyitnak meg a használni kívánt tartományi hitelesítő adatokkal, majd a címtár-lista lekérésével ellenőrizhetik a helyi fájlmegosztás kapcsolatát.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Győződjön meg arról, hogy a rendszer visszaadja-e a címtár-listát a helyi fájlmegosztás számára.

### <a name="prerequisites"></a>Előfeltételek

A helyszíni fájlmegosztás az Azure-ban futó csomagokból való eléréséhez tegye a következőket:

1. Hozzáférés engedélyezése a Windows tűzfalon keresztül.

2. Csatlakoztassa a Azure-SSIS IRt egy olyan Microsoft Azure Virtual Networkhoz, amely a helyszíni fájlmegosztás számára van csatlakoztatva.  További információ: [Join Azure-SSIS IR to a Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

3. A SSISDB `catalog.set_execution_credential` tárolt eljárás használatával adja meg a hitelesítő adatokat a jelen cikkben leírtak szerint.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Kapcsolódás az Azure-beli virtuális gépen található fájlmegosztást

Az Azure-beli virtuális gépen lévő fájlmegosztás az Azure-ban futó csomagokból való eléréséhez tegye a következőket:

1. A SSMS vagy más eszközzel kapcsolódjon a SSISDB-t üzemeltető SQL Database/SQL felügyelt példányhoz. További információ: [Kapcsolódás a SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Ha a SSISDB-t aktuális adatbázisként kívánja megnyitni, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Kapcsolódás egy fájlmegosztás Azure Files

További információ a Azure Filesről: [Azure Files](https://azure.microsoft.com/services/storage/files/).

Ha az Azure-ban futó csomagokból Azure Files fájlmegosztást szeretne elérni, tegye a következőket:

1. A SSMS vagy más eszközzel kapcsolódjon a SSISDB-t üzemeltető SQL Database/SQL felügyelt példányhoz. További információ: [Kapcsolódás a SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Ha a SSISDB-t aktuális adatbázisként kívánja megnyitni, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Következő lépések

- Telepítse a csomagokat. További információ: [SSIS-projekt üzembe helyezése az Azure-ban a SSMS használatával](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Futtassa a csomagokat. További információ: [SSIS-csomagok futtatása az Azure-ban a SSMS-](/sql/integration-services/ssis-quickstart-run-ssms)mel.
- A csomagok beosztása. További információ: [SSIS-csomagok beosztása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
