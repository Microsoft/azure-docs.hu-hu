---
title: PowerShell a VNet-végpontok és az önálló és készletezett adatbázisok szabályaihoz
description: PowerShell-parancsfájlokat biztosít a Azure SQL Database és az Azure szinapszis virtuális szolgáltatási végpontjának létrehozásához és kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: 76a1d3aaadcbd1b15966a84f5dd2fe876f82c43a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177623"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell: virtuális szolgáltatási végpont és VNet-szabály létrehozása a Azure SQL Databasehoz
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

A *virtuális hálózati szabályok* egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy a [Azure SQL Database](../sql-database-paas-overview.md) adatbázisok, rugalmas készletek vagy az [Azure szinapszis](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) -adatbázisok [logikai SQL Server-kiszolgálója](../logical-servers.md) fogadja-e a virtuális hálózatok adott alhálózatáról érkező kommunikációt.

> [!IMPORTANT]
> Ez a cikk Azure SQL Databasera vonatkozik, beleértve az Azure Szinapszist (korábban SQL DW). Az egyszerűség kedvéért a jelen cikkben Azure SQL Database kifejezés az Azure SQL Database vagy az Azure Szinapszishoz tartozó adatbázisokra vonatkozik. Ez a cikk *nem* vonatkozik az Azure SQL felügyelt példányaira, mert nem rendelkezik hozzá társított szolgáltatási végponttal.

Ez a cikk egy PowerShell-parancsfájlt mutat be, amely a következő műveleteket végzi el:

1. Létrehoz egy Microsoft Azure *virtuális szolgáltatási végpontot* az alhálózaton.
2. Hozzáadja a végpontot a kiszolgáló tűzfalához a *virtuális hálózati szabály* létrehozásához.

További hátteret [a Azure SQL Database virtuális szolgáltatási végpontok][sql-db-vnet-service-endpoint-rule-overview-735r]című témakörben talál.

> [!TIP]
> Ha mindössze annyit kell tennie, hogy felmérje vagy hozzáadja a virtuális szolgáltatás végpontjának *típusát* a Azure SQL Databasehoz az alhálózathoz, ugorjon a további [közvetlen PowerShell-szkriptre](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de az összes jövőbeli fejlesztés a [ `Az.Sql` parancsmagok](/powershell/module/az.sql)esetében is támogatott. A régebbi modul esetében lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

## <a name="major-cmdlets"></a>Fő parancsmagok

Ez a cikk a [ **New-AzSqlServerVirtualNetworkRule** parancsmagot](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule) emeli ki, amely hozzáadja az alhálózati végpontot a kiszolgáló hozzáférés-vezérlési listájához (ACL), és ezzel létrehoz egy szabályt.

Az alábbi lista azokat a *főbb* parancsmagokat mutatja be, amelyeket a **New-AzSqlServerVirtualNetworkRule** hívására való felkészüléshez futtatnia kell. Ebben a cikkben ezek a hívások a ["virtuális hálózati szabály" 3. parancsfájlban](#a-script-30)történnek:

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): létrehoz egy alhálózati objektumot.
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): létrehozza a virtuális hálózatot, és megadja az alhálózatot.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): virtuális szolgáltatási végpontot rendel az alhálózathoz.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): a virtuális hálózaton végzett frissítések megőrzése.
5. [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): miután az alhálózat egy végpont, hozzáadja az alhálózatot virtuális hálózati szabályként a kiszolgáló ACL-jéhez.
   - Ez a parancsmag a **-IgnoreMissingVNetServiceEndpoint** paramétert kínálja az Azure RM PowerShell-modul 5.1.1-es verziójától kezdve.

## <a name="prerequisites-for-running-powershell"></a>A PowerShell futtatásának előfeltételei

- Már be is jelentkezhet az Azure-ba, például a [Azure Portalon][http-azure-portal-link-ref-477t]keresztül.
- Már futtathatja a PowerShell-parancsfájlokat is.

> [!NOTE]
> Győződjön meg arról, hogy a szolgáltatáshoz tartozó végpontok be vannak kapcsolva ahhoz a VNet/alhálózathoz, amelyet hozzá szeretne adni a kiszolgálóhoz, máskülönben a VNet-tűzfalszabály létrehozása sikertelen lesz.

## <a name="one-script-divided-into-four-chunks"></a>Egy parancsfájl négy darabra oszlik

A bemutató PowerShell-szkript kisebb szkriptek sorozatából van felosztva. A divízió megkönnyíti a tanulást és rugalmasságot biztosít. A parancsfájlokat a jelzett sorozatban kell futtatni. Ha nincs ideje a parancsfájlok futtatására, a tényleges teszt kimenet a 4. parancsfájl után jelenik meg.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>1. parancsfájl: változók

Ez az első PowerShell-parancsfájl értékeket rendel a változókhoz. A következő parancsfájlok ezen változóktól függenek.

> [!IMPORTANT]
> A szkript futtatása előtt módosíthatja az értékeket, ha szeretné. Ha például már rendelkezik erőforráscsoporthoz, érdemes lehet hozzárendelt értékként szerkesztenie az erőforráscsoport nevét.
>
> Az előfizetés nevét szerkeszteni kell a szkriptbe.

### <a name="powershell-script-1-source-code"></a>1. PowerShell-parancsfájl forráskódja

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>2. parancsfájl: előfeltételek

Ez a szkript előkészíti a következő parancsfájlt, ahol a végpont művelete. Ez a szkript a következő felsorolt elemeket hozza létre, de csak akkor, ha még nem léteznek. Kihagyhatja a 2. parancsfájlt, ha biztos benne, hogy ezek az elemek már léteznek:

- Azure-erőforráscsoport
- Logikai SQL Server

### <a name="powershell-script-2-source-code"></a>2. PowerShell-parancsfájl forráskódja

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>3. parancsfájl: végpont és szabály létrehozása

Ez a szkript létrehoz egy alhálózattal rendelkező virtuális hálózatot. Ezután a parancsfájl hozzárendeli a **Microsoft. SQL** -végpont típusát az alhálózathoz. Végül a parancsfájl hozzáadja az alhálózatot a hozzáférés-vezérlési listához (ACL), így létrehoz egy szabályt.

### <a name="powershell-script-3-source-code"></a>3. PowerShell-szkript forráskódja

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>4. parancsfájl: tisztítás

Ez a végső parancsfájl törli azokat az erőforrásokat, amelyeket az előző szkriptek készítettek a bemutatóhoz. A parancsfájl azonban megerősítést kér, mielőtt törli a következőt:

- Logikai SQL Server
- Azure-erőforráscsoport

Az 1. parancsfájl futtatása után bármikor futtathatja a 4-es parancsfájlt.

### <a name="powershell-script-4-source-code"></a>4. PowerShell-parancsfájl forráskódja

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Alhálózat ellenőrzése végpontként

Lehet, hogy olyan alhálózattal rendelkezik, amely már hozzá lett rendelve a **Microsoft. SQL** típushoz, ami azt jelenti, hogy már egy virtuális szolgáltatási végpont. A [Azure Portal][http-azure-portal-link-ref-477t] használatával létrehozhat egy virtuális hálózati szabályt a végpontból.

Vagy előfordulhat, hogy nem biztos benne, hogy az alhálózat rendelkezik-e a **Microsoft. SQL** típus nevével. A következő PowerShell-szkript futtatásával hajthatja végre a műveleteket:

1. Győződjön meg arról, hogy az alhálózat rendelkezik-e a **Microsoft. SQL** típus nevével.
2. Ha nincs megadva, rendelje hozzá a típus nevét.
    - A szkript megkéri, hogy *erősítse* meg, mielőtt alkalmazza a hiányzó típus nevét.

### <a name="phases-of-the-script"></a>A parancsfájl fázisai

A PowerShell-parancsfájl fázisai:

1. Jelentkezzen be az Azure-fiókjába, és csak egyszer kell bejelentkeznie PS-munkamenetben.  Változók kiosztása.
2. Keressen rá a virtuális hálózatra, majd az alhálózatra.
3. Az alhálózat a **Microsoft. SQL** Endpoint Server típusa?
4. Adja hozzá a **Microsoft. SQL** nevű virtuális szolgáltatási végpontot az alhálózaton.

> [!IMPORTANT]
> A szkript futtatása előtt szerkesztenie kell a $-változókhoz rendelt értékeket a szkript felső részén.

### <a name="direct-powershell-source-code"></a>Közvetlen PowerShell-forráskód

Ez a PowerShell-parancsfájl nem frissíti a semmit, hacsak nem válaszol az Igen értékre, ha megerősítést kér. A szkript a **Microsoft. SQL** Type nevet adhatja hozzá az alhálózathoz. A parancsfájl azonban csak akkor próbálja meg a hozzáadást, ha az alhálózat nem rendelkezik a típus nevével.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
