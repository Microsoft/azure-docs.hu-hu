---
title: DNS-alias (PowerShell & Azure CLI)
description: A PowerShell és az Azure CLI-parancsmagok lehetővé teszik az új ügyfélkapcsolatok átirányítását egy másik Azure-beli SQL Server-kiszolgálóra anélkül, hogy meg kellene érintenie az ügyfél konfigurációját.
keywords: DNS SQL Database
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790457"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell DNS-aliashoz Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk egy PowerShell-parancsfájlt tartalmaz, amely bemutatja, hogyan kezelhető a Azure SQL Database üzemeltető [SQL Server-kiszolgáló](logical-servers.md) DNS-aliasa.

> [!NOTE]
> Ez a cikk a Azure PowerShell az Module vagy az Azure CLI használatára lett frissítve. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig.
>
> Ha többet szeretne megtudni az az Module és a AzureRM kompatibilitásáról, tekintse meg [a Azure PowerShell az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakört. A telepítési utasításokért lásd: [Azure PowerShell telepítése](/powershell/azure/install-az-ps) vagy az [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>DNS-alias a kapcsolatok karakterláncában

A [logikai SQL-kiszolgáló](logical-servers.md)összekapcsolásához a (z) SQL Server Management Studio (SSMS) ügyfél a DNS-alias nevét is megadja a valódi kiszolgálónév helyett. A következő példa kiszolgálói sztringben az alias- *Unique-alias-Name* az első pont-tagolt csomópontot helyettesíti a négy csomópontos kiszolgáló sztringben:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Előfeltételek

Ha a jelen cikkben megadott demo PowerShell-szkriptet szeretné futtatni, a következő előfeltételek érvényesek:

- Azure-előfizetéssel és-fiókkal, az ingyenes próbaverzióért lásd: [Azure-próbaverziók](https://azure.microsoft.com/free/)
- Két kiszolgáló

## <a name="example"></a>Példa

A következő kódrészlet a literál értékeknek több változóhoz való hozzárendelésével kezdődik.

A kód futtatásához szerkessze a helyőrző értékeit úgy, hogy azok megfeleljenek a rendszer valós értékeinek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): DNS-aliast hoz létre a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az 1. kiszolgálóhoz rendelt összes alias beolvasása és listázása.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról a 2. kiszolgálóra.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): távolítsa el az aliast a 2. kiszolgálóról az alias nevének használatával.

A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

`Get-Module -ListAvailable Az`A verzió megkereséséhez használja a *PowerShell \_ise.exe*.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A használt parancsok a következők:

- [az SQL Server DNS-alias Create](/powershell/module/az.Sql/New-azSqlServerDnsAlias): létrehoz egy DNS-aliast a kiszolgálóhoz. Az alias az 1. kiszolgálóra hivatkozik.
- az [SQL Server DNS-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az 1. kiszolgálóhoz rendelt összes alias beolvasása és listázása.
- az [SQL Server DNS-alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról a 2. kiszolgálóra.
- az [SQL Server DNS-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): távolítsa el az aliast a 2-es kiszolgálóról az alias nevével.

Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Következő lépések

A SQL Database DNS-alias funkciójának részletes ismertetését lásd: [Azure SQL Database DNS-aliasa](./dns-alias-overview.md).