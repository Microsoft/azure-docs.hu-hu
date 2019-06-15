---
title: PowerShell a virtuális hálózatok végpontok és szabályok az Azure SQL-ben egyetlen vagy készletezett adatbázisok |} A Microsoft Docs
description: PowerShell-szkripteket hozhat létre és virtuális végpontok kezelése az Azure SQL Database és az SQL Data Warehouse biztosít.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6713182003a280c1d53e904209159b55b4ad01c6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331145"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell:  Az SQL virtuális szolgáltatásvégpont és VNet szabály létrehozása

*Virtuális hálózati szabályok* van egy tűzfal biztonsági funkció, amely szabályozza, hogy az adatbázis-kiszolgáló az önálló adatbázisok és rugalmas készlet az Azure-ban [SQL Database](sql-database-technical-overview.md) vagy az adatbázisok a [SQL Data Az adatraktár](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) virtuális hálózatok adott alhálózatain érkező kommunikációt fogad el.

> [!IMPORTANT]
> Ez a cikk az Azure SQL-kiszolgálóhoz, és az SQL Database és az SQL Data Warehouse-adatbázisok az Azure SQL-kiszolgálón létrehozott vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. Ebben a cikkben leírtak *nem* a alkalmazni egy **felügyelt példány** üzembe helyezése az Azure SQL Database, mert nem rendelkezik egy hozzá társított végpontot.

Ez a cikk biztosít, és elmagyarázza, egy PowerShell-parancsprogram, amely a következő műveleteket hajtja végre:

1. Létrehoz egy Microsoft Azure *virtuális szolgáltatásvégpont* az alhálózaton található.
2. A végpont ad hozzá a tűzfalat az Azure SQL Database-kiszolgáló létrehozásához egy *virtuális hálózati szabályt*.

A szabályok létrehozásához motivációit mutatjuk be: [Az Azure SQL Database virtuális Szolgáltatásvégpontok][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Ha szüksége, mérje fel, vagy adja hozzá a virtuális szolgáltatásvégpont *típusnév* az SQL Database, az alhálózatra, áttérhet a szélesebb [PowerShell-parancsprogram közvetlen](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

## <a name="major-cmdlets"></a>Fő parancsmagok

Ez a cikk emeli ki a **New-AzSqlServerVirtualNetworkRule** parancsmag, amely hozzáadja az alhálózat végpont a hozzáférés-vezérlési lista (ACL) az Azure SQL Database-kiszolgáló, ezáltal a szabályok létrehozásához.

Az alábbi lista tartalmazza a sorozat egyéb *fő* parancsmagok, amelyek futtatnia kell a hívás előkészítése **New-AzSqlServerVirtualNetworkRule**. Ez a cikk ezeket a hívásokat fordulnak elő [parancsfájl 3 "virtuális hálózati szabály"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Egy alhálózat objektumot hoz létre.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Jogosultságot ad az alhálózat a virtuális hálózatot hoz létre.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Az alhálózat egy virtuális végpontot rendel.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Továbbra is fennáll, a virtuális hálózaton végzett frissítések.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Miután az alhálózat egy végpontot, hozzáadja az alhálózat egy virtuális hálózati szabály, az ACL-t az Azure SQL Database-kiszolgáló.
   - Ez a parancsmag kínál a paraméter **- IgnoreMissingVNetServiceEndpoint**, már akár az Azure RM PowerShell-modul 5.1.1-es verzió.

## <a name="prerequisites-for-running-powershell"></a>Futtatja a Powershellt előfeltételei

- Már bejelentkezhet az Azure-ba, mint például keresztül a [az Azure portal][http-azure-portal-link-ref-477t].
- PowerShell-szkriptek már futtathatja.

> [!NOTE]
> Győződjön meg arról, hogy a Szolgáltatásvégpontok a virtuális hálózat/alhálózat, amelyeket szeretne hozzáadni a kiszolgálóhoz ellenkező esetben a VNet tűzfalszabály létrehozása sikertelen lesz vannak kapcsolva.

## <a name="one-script-divided-into-four-chunks"></a>Egy parancsprogram négy adattömbök felosztva

A PowerShell-parancsprogram bemutató kisebb parancsfájlok sorozatát oszlik. A részleg megkönnyíti a tanulási és rugalmasságot biztosít. A parancsfájlok a megjelölt sorrendben kell futtatni. Ha nem rendelkeznek aktuális idő a szkriptek futtatására, a tényleges tesztkimenet szkript 4 után jelenik meg.

<a name="a-script-10" />

### <a name="script-1-variables"></a>1\. parancsfájl: Változók

Az első PowerShell-parancsprogram értékeket rendel a változókat. Az ezt követő parancsfájlok attól függ, hogy ezeket a változókat.

> [!IMPORTANT]
> Mielőtt ezt a szkriptet futtatta, igény szerint szerkesztheti az értékeket. Például ha már rendelkezik erőforráscsoporttal, érdemes az erőforráscsoport nevét a hozzárendelt érték szerkesztéséhez.
>
> Az előfizetés nevét módosítani kell a parancsfájlba.

### <a name="powershell-script-1-source-code"></a>PowerShell parancsfájl 1 forráskód

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>2\. parancsfájlt: Előfeltételek

Ez a parancsfájl előkészíti a következő szkriptet, ahol a végpont beavatkozásra. Ez a szkript létrehoz az Ön számára a következő elemek, de csak listában, ha azok nem léteznek. Szkript 2 kihagyhatja, ha biztos benne, hogy ezek az elemek már létezik:

- Azure-erőforráscsoport
- Azure SQL Database-kiszolgáló

### <a name="powershell-script-2-source-code"></a>PowerShell parancsfájl 2 forráskód

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>3\. parancsfájlt: A végpont és a egy szabály létrehozása

Ez a szkript létrehoz egy virtuális hálózat alhálózatához. Ezután a parancsfájl hozzárendeli a **Microsoft.Sql** típusú végpont az alhálózathoz. Végül pedig a parancsfájl az SQL Database-kiszolgálóhoz, és ezáltal a szabály létrehozása a hozzáférés-vezérlési lista (ACL) ad hozzá az alhálózat.

### <a name="powershell-script-3-source-code"></a>PowerShell parancsfájl 3 forráskód

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>4\. parancsfájlt: Karbantartás

A végső parancsfájl törli a bemutató a korábbi parancsfájlok létrehozott erőforrásokat. Azonban a parancsprogram kéri a jóváhagyás előtt törli a következő:

- Azure SQL Database-kiszolgáló
- Azure-erőforráscsoport

Szkript 4 1 parancsfájl befejezése után bármikor futtathatja.

### <a name="powershell-script-4-source-code"></a>PowerShell-szkript 4 forráskód

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>1 – 4 parancsfájlok tényleges kimenete

A tesztfuttatás kimenete ezután megjelenik egy rövidített formátumban. A kimenet jelenthet, abban az esetben, ha nem szeretné, hogy futtatja a PowerShell-parancsfájlok most ténylegesen.

```cmd
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Completed script 2, the "Prerequisites".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations
----------------- -------       ---------
Succeeded         Microsoft.Sql {westcentralus}

Verify that the rule is in the SQL DB ACL.

Completed script 3, the "Virtual-Network-Rule".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Ez az a fő PowerShell-szkript végén.

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Ellenőrizze az alhálózat-végpont

Előfordulhat, hogy egy alhálózatot, amely már hozzá van rendelve a **Microsoft.Sql** típusnév, ami azt jelenti, már egy virtuális végpontot. Használhatja a [az Azure portal] [ http-azure-portal-link-ref-477t] hozhat létre egy virtuális hálózati szabályt a végpontról.

Vagy, akkor előfordulhat, hogy nem tudja, hogy hogy rendelkezik-e az alhálózat a **Microsoft.Sql** írja be a nevet. A következő PowerShell-parancsfájl műveletek futtathatja:

1. Annak megállapítása, hogy rendelkezik-e az alhálózat a **Microsoft.Sql** írja be a nevet.
2. Igény szerint rendelje hozzá a típusnév, ha nincs megadva.
    - A parancsprogram kéri, hogy *megerősítése*, mielőtt vonatkozik a hiányzó típus neve.

### <a name="phases-of-the-script"></a>A parancsfájl fázisai

A PowerShell-parancsfájl fázisai a következők:

1. JELENTKEZZEN be Azure-fiókjába, PS-munkamenetenként csak egyszer szükséges.  Rendelje hozzá a változókat.
2. Keresse meg a virtuális hálózathoz, majd az alhálózathoz.
3. Az alhálózat személyesnek minősül **Microsoft.Sql** kiszolgáló típusú végpont?
4. Adjon hozzá egy virtuális végpontot típus nevének **Microsoft.Sql**, az alhálózaton található.

> [!IMPORTANT]
> Ez a szkript futtatása, szerkesztenie kell a $változók, a parancsfájl elején rendelt értékeket.

### <a name="direct-powershell-source-code"></a>A közvetlen forráskódot PowerShell

A PowerShell-parancsprogram nem frissíti semmit, kivéve, ha az Igen válaszol, ha jóváhagyást kér. A parancsfájl adhat hozzá a típusnév **Microsoft.Sql** az alhálózathoz. De a parancsfájl megpróbálja hozzáadása csak akkor, ha az alhálózat nem rendelkezik a típusnév.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

### <a name="actual-output"></a>Aktuális kimenete

A következő blokkot a tényleges visszajelzés (formai módosításokat) jeleníti meg.

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```

<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/
