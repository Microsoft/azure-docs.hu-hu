---
title: Az Azure Database Migration Service előfeltételei
description: Ismerje meg, hogyan tekintheti át az előfeltételeket az adatbázis-áttelepítés végrehajtásához a Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094044"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése

Több előfeltétel szükséges annak biztosításához, hogy Azure Database Migration Service zökkenőmentesen fusson az adatbázisok áttelepítésekor. Néhány előfeltétel a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) esetében érvényes, míg más előfeltételek egyediek egy adott forgatókönyv esetében.

A Azure Database Migration Service használatával kapcsolatos előfeltételek a következő részekben találhatók.

## <a name="prerequisites-common-across-migration-scenarios"></a>Gyakori előfeltételek az áttelepítési forgatókönyvek között

Azure Database Migration Service az összes támogatott áttelepítési forgatókönyv esetében gyakori előfeltételek közé tartozik a következők szükségesek:

* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a ServiceTag 443 kimenő portját a ServiceBus, a Storage és a AzureMonitor számára. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.

    > [!IMPORTANT]
    > Azure Database Migration Service egy példányának létrehozása olyan virtuális hálózati beállításokhoz való hozzáférést igényel, amelyek általában nem azonos erőforráscsoporthoz tartoznak. Ennek eredményeképpen a DMS-példányt létrehozó felhasználónak engedélyre van szüksége az előfizetés szintjén. A szükséges szerepkörök létrehozásához, amelyeket igény szerint hozzárendelhet, futtassa a következő parancsfájlt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server áttelepítésének előfeltételei a Azure SQL Database

A Azure Database Migration Service az összes áttelepítési forgatókönyvre vonatkozó előfeltételeket is, amelyek az egyes forgatókönyvekre vagy egy másikra is érvényesek.

Ha a Azure Database Migration Service használatával hajtja végre SQL Server Azure SQL Database áttelepítéseket, az összes áttelepítési forgatókönyvhöz közös előfeltételek mellett ügyeljen arra, hogy a következő további előfeltételeket kövesse:

* Hozzon létre egy Azure SQL Database példány példányát, amelyet a [Azure Portalban található Azure SQL Database adatbázis létrehozása](../azure-sql/database/single-database-create-quickstart.md)című cikkben ismertetett részletek követnek.
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály-szabályt](../azure-sql/database/firewall-configure.md) a SQL Database számára, hogy lehetővé tegye a Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Servicehoz használt virtuális hálózat alhálózati tartományát.
* Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
* Győződjön meg arról, hogy a célként megadott adatbázishoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek-e az adatbázis VEZÉRLÉSére szolgáló engedéllyel.

   > [!NOTE]
   > A Azure Database Migration Service SQL Serverról Azure SQL Databasere való áttelepítésének végrehajtásához szükséges előfeltételek teljes listáját az oktatóanyag [SQL Server áttelepítése Azure SQL Database](./tutorial-sql-server-to-azure-sql.md)című cikkben tekintheti meg.
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>SQL Server áttelepítésének előfeltételei az Azure SQL felügyelt példányaihoz

* Hozzon létre egy felügyelt SQL-példányt a [Azure Portalban az Azure SQL felügyelt példány létrehozása](../azure-sql/managed-instance/instance-create-quickstart.md)című cikkben ismertetett részleteket követve.
* Nyissa meg a tűzfalakat, hogy engedélyezze az SMB-forgalmat az 445-as porton a Azure Database Migration Service IP-cím vagy alhálózat-tartomány számára.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. A Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat a biztonságimásolat-fájlok Azure Storage-tárolóba való feltöltéséhez a visszaállítási művelethez.
* Hozzon létre egy BLOB-tárolót, és kérje le az SAS URI- [t az Azure Blob Storage-erőforrások kezelése Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)használatával című cikk lépéseit követve. A SAS URI létrehozásakor ügyeljen arra, hogy a házirend ablakban válassza az összes engedély (olvasás, írás, törlés, Listázás) lehetőséget.

   > [!NOTE]
   > A SQL Serverról az SQL felügyelt példányra történő áttelepítések végrehajtásához szükséges előfeltételek teljes listáját az oktatóanyag az [SQL felügyelt példányra SQL Server áttelepítése](./tutorial-sql-server-to-managed-instance.md)című témakörben Azure Database Migration Service.

## <a name="next-steps"></a>Következő lépések

A Azure Database Migration Service és a regionális elérhetőség áttekintését lásd: [Mi a Azure Database Migration Service](dms-overview.md).