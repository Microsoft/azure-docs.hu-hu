---
title: 'Rövid útmutató: Dedikált SQL-készlet (korábban SQL DW) létrehozása Azure PowerShell'
description: Gyorsan létrehozhat egy dedikált SQL-készletet (korábbi nevén SQL DW-t) kiszolgálószintű tűzfals Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537461"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Rövid útmutató: Dedikált SQL-készlet (korábban SQL DW) létrehozása Azure PowerShell

Dedikált SQL-készlet (korábban SQL DW) létrehozása a Azure Synapse Analytics a Azure PowerShell.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT]
> Dedikált SQL-készlet (korábban SQL DW) létrehozása új számlázható szolgáltatást eredményezhet.  További információkért lásd: [Azure Synapse Analytics díjszabása.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A használt előfizetést a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával tudja megnézni.

```powershell
Get-AzSubscription
```

Ha az alapértelmezett előfizetéstől eltérő előfizetést kell használnia, futtassa a [Set-AzContext futtatását.](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Változók létrehozása

Ebben a rövid útmutatóban a szkriptekben használt változókat határozhat meg.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzResourceGroup paranccsal.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Az erőforráscsoport olyan tároló, amelyben a rendszer üzembe helyez és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre [egy logikai SQL-kiszolgálót](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServer paranccsal.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. Az alábbi példa egy véletlenszerűen elnevezett kiszolgálót hoz létre az erőforráscsoportban egy nevű rendszergazdai felhasználóval és `ServerAdmin` a jelszavával. `ChangeYourAdminPassword1` Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon [létre egy kiszolgálószintű tűzfalszabályt](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServerFirewallRule paranccsal.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) A kiszolgálószintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például az SQL Server Management Studio vagy az SQLCMD segédprogram számára, hogy dedikált SQL-készlethez (korábban SQL DW) csatlakozzon a dedikált SQL-készlet szolgáltatás tűzfalán keresztül.

A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Az SQL-végpontok az 1433-as porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy a hálózati tűzfal nem engedélyezett kimenő forgalmat az 1433-as porton keresztül. Ha igen, csak akkor tud csatlakozni a kiszolgálóhoz, ha az it-részleg megnyitja az 1433-as portot.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Dedikált SQL-készlet (korábban SQL DW) létrehozása

Az alábbi példa egy dedikált SQL-készletet (korábban SQL DW) hoz létre a korábban definiált változók használatával.  A szolgáltatási célt DW100c-ként határozza meg, amely a dedikált SQL-készlet (korábban SQL DW) alacsonyabb költségű kiindulási pontja.

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

A szükséges paraméterek a következők:

* **RequestedServiceObjectiveName:** A kért [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) mennyisége. Ennek az összegnek a növelése növeli a számítási költségeket. A támogatott értékek listájáért tekintse meg a [memória- és egyidejűségi korlátokat.](memory-concurrency-limits.md)
* **DatabaseName:** A létrehozott dedikált SQL-készlet (korábban SQL DW) neve.
* **ServerName:** A létrehozáshoz használt kiszolgáló neve.
* **ResourceGroupName:** Az Ön által használt erőforráscsoport. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Kiadás:** Dedikált SQL-készlet (korábban SQL DW) létrehozásához "DataWarehouse" kell, hogy legyen.

A választható paraméterek a következők:

* **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS. Az adatbázisok rendezése nem módosítható.
* **MaxSizeBytes:** Az adatbázisok alapértelmezett maximális mérete 240TB. A maximális méretkorlát a sorcentrikus adatokra vonatkozik. Az oszlopos adatok korlátlan tárterületet kínálnak.

További információ a paraméterbeállításokról: [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy a későbbiekben rövid útmutatót is munkához fog látni, akkor ne takarítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Következő lépések

Ezzel létrehozott egy dedikált SQL-készletet (korábban SQL DW), létrehozott egy tűzfalszabályt, és csatlakozott a dedikált SQL-készlethez. További tudnivalókért folytassa az Adatok betöltése dedikált [SQL-készletbe című cikkel.](./load-data-from-azure-blob-storage-using-copy.md)
