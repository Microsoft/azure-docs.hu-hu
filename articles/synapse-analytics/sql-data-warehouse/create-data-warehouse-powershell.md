---
title: 'Gyors útmutató: dedikált SQL-készlet létrehozása (korábban SQL DW) Azure PowerShell'
description: A Azure PowerShell használatával gyorsan létrehozhat egy dedikált SQL-készletet (korábban SQL DW) egy kiszolgálói szintű tűzfalszabály segítségével.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 18fba46e4e8de14d9b3ec43455ba2abcc4218dc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120189"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Gyors útmutató: dedikált SQL-készlet létrehozása (korábban SQL DW) Azure PowerShell

Hozzon létre egy dedikált SQL-készletet (korábbi nevén SQL DW) az Azure szinapszis Analyticsben Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT]
> Egy dedikált SQL-készlet (korábban SQL DW) létrehozása egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával megtekintheti, hogy melyik előfizetést használja.

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)parancsot.

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

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával. Az erőforráscsoport egy tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre egy [logikai SQL Server-kiszolgálót](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával. A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. Az alábbi példa egy véletlenszerűen megnevezett kiszolgálót hoz létre az erőforráscsoporthoz egy nevű rendszergazdai felhasználóval `ServerAdmin` és egy jelszóval `ChangeYourAdminPassword1` . Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy [kiszolgálói szintű tűzfalszabály](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával. A kiszolgálói szintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például a SQL Server Management Studio vagy a SQLCMD segédprogram számára a dedikált SQL-készlethez (korábban SQL DW) való kapcsolódást a dedikált SQL Pool Service tűzfalon keresztül.

A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Az SQL-végpontok a 1433-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, akkor nem fog tudni csatlakozni a kiszolgálóhoz, ha az informatikai részleg megnyitja a 1433-es portot.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Dedikált SQL-készlet létrehozása (korábban SQL DW)

Az alábbi példa egy dedikált SQL-készletet (korábban SQL DW) hoz létre a korábban definiált változók használatával.  Meghatározza a szolgáltatás célját a DW100c, amely a dedikált SQL-készlet (korábban SQL DW) alacsonyabb díjas kiindulópontja.

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

* **RequestedServiceObjectiveName**: a kért [adatraktár-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) mennyisége. Az érték növelése növeli a számítási költségeket. A támogatott értékek listáját a [memória és a Egyidejűség korlátai](memory-concurrency-limits.md)című részben tekintheti meg.
* **Databasename**: a létrehozandó dedikált SQL-készlet (korábban SQL DW) neve.
* **Kiszolgálónév**: a létrehozáshoz használt kiszolgáló neve.
* **ResourceGroupName**: az Ön által használt erőforráscsoport. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Kiadás**: "DataWarehouse" kell lennie egy dedikált SQL-készlet (korábban SQL DW) létrehozásához.

A választható paraméterek a következők:

* **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS. A rendezés nem módosítható egy adatbázison.
* **MaxSizeBytes**: az adatbázis alapértelmezett maximális mérete 240TB. A maximális méret korlátozza a sortárindex létrehozását-adatmennyiséget. Az oszlopos adatmennyiségek korlátlan tárhelyet biztosítanak.

A paraméter beállításairól további információt a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)című témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy továbbra is a későbbi gyors útmutatók használatával dolgozik, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Következő lépések

Létrehozott egy dedikált SQL-készletet (korábban SQL DW), létrehozott egy tűzfalszabályet, és csatlakoztatta a dedikált SQL-készlethez. További információért folytassa a [betöltési információkat egy DEDIKÁLT SQL-készletbe](./load-data-from-azure-blob-storage-using-copy.md) .