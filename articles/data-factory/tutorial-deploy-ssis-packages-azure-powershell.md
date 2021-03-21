---
title: Azure-SSIS Integration Runtime beállítása a PowerShell-lel
description: Megtudhatja, hogyan állíthat be egy Azure-SSIS Integration Runtime Azure Data Factory a PowerShellben, hogy SSIS-csomagokat telepítsen és futtasson az Azure-ban.
ms.service: data-factory
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: a0a6e115d2b76ab3f060ee1bcb197da58f2c8fa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391286"
---
# <a name="set-up-an-azure-ssis-ir-in-azure-data-factory-by-using-powershell"></a>Azure Data Factory Azure-SSIS IR beállítása a PowerShell használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag azt ismerteti, hogyan lehet a PowerShell használatával Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) kiépíteni Azure Data Factory (ADF)-ben. Az Azure-SSIS IR a következőket támogatja:

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök már Azure-kompatibilisek, és tartalmaznak SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) és parancssori segédeszközöket, például a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

> [!NOTE]
> Ez a cikk bemutatja, hogyan állíthatja be a Azure-SSIS IRt a Azure PowerShell használatával. Ha a Azure-SSIS IR beállításához a Azure Portal vagy Azure Data Factory alkalmazást szeretné használni, tekintse meg az [oktatóanyag: Azure-SSIS IR beállítása](./tutorial-deploy-ssis-packages-azure.md)című témakört. 

Az oktatóanyagban a következőket végezheti el:
> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy Azure-SSIS Integration Runtime.
> * Indítsa el a Azure-SSIS Integration Runtime.
> * Tekintse át a teljes parancsfájlt.
> * SSIS-csomagok telepítése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

- **Azure SQL Database kiszolgáló vagy felügyelt példány (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [SQL Database és az SQL felügyelt példányának összehasonlítása](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatcsatorna konfigurálása nélkül szeretné elérni a helyszíni adatait, csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](./create-azure-ssis-integration-runtime.md).

  - Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez. További információ: [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.

  - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy olyan IP-címtartományt, amely tartalmazza az ügyfélszámítógép IP-címét az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listára. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../azure-sql/database/firewall-configure.md).

  - A kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítéssel, vagy az adat-előállító felügyelt identitásával az Azure AD-hitelesítés használatával kapcsolódhat az adatbázis-kiszolgálóhoz. Az utóbbi esetében hozzá kell adnia az adatok előállítójának felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](./create-azure-ssis-integration-runtime.md).

  - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB-példánnyal. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB-példányok használatát.

- **Azure PowerShell**. Ha PowerShell-parancsfájlt szeretne futtatni a Azure-SSIS IR beállításához, kövesse az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps)című témakör utasításait.

> [!NOTE]
> Azon Azure-régiók listáját, amelyekben a Azure Data Factory és a Azure-SSIS IR jelenleg elérhető, tekintse meg a [Azure Data Factory és a Azure-SSIS IR rendelkezésre állás régió szerint](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)című témakört. 

## <a name="open-the-windows-powershell-ise"></a>A Windows PowerShell integrált parancsprogram-kezelési környezet megnyitása

Nyissa meg a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE) rendszergazdai engedélyekkel. 

## <a name="create-variables"></a>Változók létrehozása

Másolja a következő szkriptet az ISE-be. Határozza meg a változók értékeit. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character (for example, "$"), precede it with the escape character "`" (for example, "`$")
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info; this is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, although Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

## <a name="sign-in-and-select-your-subscription"></a>Jelentkezzen be, és válassza ki az előfizetését

A bejelentkezéshez és az Azure-előfizetés kiválasztásához adja hozzá a következő kódot a szkripthez:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-your-database-server"></a>Az adatbázis-kiszolgálóval létesített kapcsolatok ellenőrzése

A kapcsolódás ellenőrzéséhez adja hozzá a következő parancsfájlt: 

```powershell
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Ha Azure SQL Database példányt szeretne létrehozni a parancsfájl részeként, tekintse meg a következő példát. Állítsa be a már nem definiált változók értékeit (például SSISDBServerName, FirewallIPAddress). 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime létrehozása

Ha SSIS-csomagokat futtató Azure-SSIS Integration Runtime szeretne létrehozni az Azure-ban, futtassa a következő parancsokat. Ha nem használ SSISDB, kihagyhatja a CatalogServerEndpoint, a CatalogPricingTier és a CatalogAdminCredential paramétereket.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
```

## <a name="start-the-azure-ssis-integration-runtime"></a>A Azure-SSIS Integration Runtime elindítása

A Azure-SSIS IR elindításához futtassa a következő parancsokat:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie.
>
> Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. 
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). További információ a telepíthető további összetevőkről: [a Azure-SSIS IR egyéni beállításai](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Teljes szkript

Az ebben a szakaszban található PowerShell-szkript a SSIS-csomagokat futtató Azure-SSIS IR egy példányát konfigurálja. A szkript sikeres futtatása után SSIS-csomagokat helyezhet üzembe és futtathat az Azure-ban.

1. Nyissa meg az ISE-t.
2. Az ISE parancssorában futtassa a következő parancsot:  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Másolja az ebben a szakaszban található PowerShell-szkriptet az ISE-be.
4. Adja meg a szkript elején a paramétereknek megfelelő értékeket.
5. Futtassa a szkriptet. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x the number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you want to use SSISDB, ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-your-azure-ssis-ir"></a>A Azure-SSIS IR monitorozása és kezelése

A Azure-SSIS IR figyelésével és kezelésével kapcsolatos információkért lásd: 

- [A Azure-SSIS IR figyelése](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [A Azure-SSIS IR kezelése](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és futtathatja azokat a Azure-SSIS IR az Azure-kompatibilis SSDT vagy SSMS eszközök használatával. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma a következő: `<server name>.database.windows.net` .
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.<dns prefix>.database.windows.net` .
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Ha nem használja a SSISDB-t, a csomagokat telepítheti fájlrendszerbe, Azure Filesba vagy MSDB, amelyet az Azure SQL felügyelt példánya futtat, és a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) parancssori segédeszközök használatával futtathatja azokat a Azure-SSIS IR. 

További információ: [SSIS-projektek/csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Mindkét esetben a telepített csomagokat Azure-SSIS IR is futtathatja a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).

További SSIS-dokumentációt a következő témakörben talál: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy Azure-SSIS Integration Runtime.
> * Indítsa el a Azure-SSIS Integration Runtime.
> * Tekintse át a teljes parancsfájlt.
> * SSIS-csomagok telepítése.

A Azure-SSIS Integration Runtime testreszabásával kapcsolatos további tudnivalókért tekintse meg a következő cikket:

> [!div class="nextstepaction"]
>[A Azure-SSIS IR testreszabása](./how-to-configure-azure-ssis-ir-custom-setup.md)