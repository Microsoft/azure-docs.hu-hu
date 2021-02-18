---
title: A BYOS konfigurálása (saját tárhely használata) a Profiler & Snapshot Debugger
description: A BYOS konfigurálása (saját tárhely használata) a Profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589662"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>A saját tároló (BYOS) beállítása a Application Insights Profiler és a Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Mi a saját tárterület használata (BYOS), és miért van rá szükségem? 
Application Insights Profiler vagy Snapshot Debugger használatakor az alkalmazás által generált összetevők a nyilvános interneten keresztül töltődnek fel az Azure Storage-fiókba. Ezeket a fiókokat a Microsoft a feldolgozás és az elemzés céljából fizeti és szabályozza. A Microsoft vezérli az adott összetevőkre vonatkozó titkosítási, Rest-és élettartam-kezelési házirendeket.

Ha saját tárhelyet használ, a rendszer feltölti ezeket az összetevőket egy Ön által vezérelt Storage-fiókba. Ez azt jelenti, hogy a titkosítást a REST-re vonatkozó szabályzatot, az élettartam-kezelési házirendet és a hálózati hozzáférést szabályozza. A Storage-fiókkal kapcsolatos költségekért azonban felelősnek kell lennie.

> [!NOTE]
> Ha engedélyezi a privát hivatkozásokat, a saját tárterületet is igénybe kell hoznia. További információ a Application Insightshoz tartozó privát hivatkozásokról [: dokumentáció.](../logs/private-link-security.md)
>
> Ha engedélyezi Customer-Managed kulcsokat, a saját tárterületet kell használnia. A Application Insights Customer-Managed kulcsaival kapcsolatos további információkért [tekintse meg a dokumentációt.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Hogyan lesz elérhető a Storage-fiókom?
1. A Virtual Machinesban vagy App Serviceban futó ügynökök az összetevők (profilok, Pillanatképek és szimbólumok) feltöltését is feltölthetik a fiókban található blob-tárolóba. Ehhez a folyamathoz kapcsolatba kell lépnie a Application Insights Profiler vagy Snapshot Debugger szolgáltatással, hogy SAS (közös hozzáférésű aláírási) tokent szerezzen be a Storage-fiókjában lévő új blobba.
1. A Application Insights Profiler vagy Snapshot Debugger szolgáltatás elemzi a bejövő blobot, és visszaírja az elemzési eredményeket és a naplófájlokat a blob Storage-ba. A rendelkezésre álló számítási kapacitástól függően ez a folyamat bármikor felmerülhet a feltöltés után.
1. Amikor megtekinti a Profiler-nyomkövetéseket vagy a pillanatkép-hibakereső elemzését, a szolgáltatás beolvassa az elemzési eredményeket a blob Storage-ból.

## <a name="prerequisites"></a>Előfeltételek
* Győződjön meg arról, hogy a Storage-fiókját a Application Insights erőforrással megegyező helyen hozza létre. Például: Ha a Application Insights-erőforrás az USA 2. nyugati régiójában található, a Storage-fióknak az USA 2. nyugati régiójában is szerepelnie kell. 
* Adja meg a "Storage blob-adatközreműködői" szerepkört a HRE alkalmazás "diagnosztikai szolgáltatások megbízható tároló-hozzáférése" számára a Storage-fiókban a Access Control (IAM) felhasználói felületén keresztül.
* Ha engedélyezve van a privát hivatkozás, konfigurálja a további beállítást, hogy engedélyezze a kapcsolatot a megbízható Microsoft-szolgáltatással a Virtual Network. 

## <a name="how-to-enable-byos"></a>A BYOS engedélyezése

### <a name="create-storage-account"></a>Tárfiók létrehozása
Hozzon létre egy teljesen új Storage-fiókot (ha nem rendelkezik vele) ugyanazon a helyen, mint a Application Insights erőforrás.
Ha a Application Insights erőforrása be van kapcsolva `West US 2` , akkor a Storage-fióknak a-ben kell lennie `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>A diagnosztikai szolgáltatásokhoz való hozzáférés biztosítása a Storage-fiókhoz
Egy BYOS-fiók egy Application Insights erőforráshoz lesz csatolva. Application Insights erőforráshoz csak egy Storage-fiók tartozhat, és mindkettőnek ugyanazon a helyen kell lennie. Ugyanazt a Storage-fiókot használhatja egynél több Application Insights erőforrással is.

Először is a Application Insights Profiler és a Snapshot Debugger szolgáltatásnak hozzáférést kell biztosítania a Storage-fiókhoz. A hozzáférés megadásához adja hozzá a szerepkört `Storage Blob Data Contributor` a HRE alkalmazáshoz a `Diagnostic Services Trusted Storage Access` Storage-fiók Access Control (iam) lapján, ahogyan az a 1,0. ábrán látható. 

Lépések: 
1. Kattintson a "Hozzáadás" gombra a "szerepkör-hozzárendelés hozzáadása" szakaszban. 
1. A "Storage blob adatközreműködői" szerepkör kiválasztása 
1. A "hozzáférés társítása" szakaszban válassza az "Azure AD-felhasználó,-csoport vagy egyszerű szolgáltatásnév" lehetőséget. 
1. Keressen & válassza a "diagnosztikai szolgáltatások megbízható tároló hozzáférése" alkalmazást 
1. Módosítások mentése

_![ 1,0](media/profiler-bring-your-own-storage/figure-10.png)_. ábra, 
 _1,0_ 

Miután hozzáadta a szerepkört, az megjelenik a "szerepkör-hozzárendelések" szakaszban, például a lenti 1,1 ábrán. 
_![ 1,1](media/profiler-bring-your-own-storage/figure-11.png)_. ábra, 
 _1,1_ 

Ha privát hivatkozást is használ, egy további konfigurációra van szükség, amely lehetővé teszi a megbízható Microsoft-szolgáltatásokhoz való kapcsolódást a Virtual Network. Tekintse át a [Storage hálózati biztonsági dokumentációját](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>A Storage-fiók összekapcsolása a Application Insights erőforrással
A BYOS diagnosztika (Profiler/Debugger) konfigurálásához három lehetőség közül választhat:

* Azure PowerShell-parancsmagok használata
* Az Azure parancssori felületének (CLI) használata
* Az Azure Resource Manager-sablonok használata

#### <a name="configure-using-azure-powershell-cmdlets"></a>Konfigurálás Azure PowerShell-parancsmagok használatával

1. Győződjön meg arról, hogy telepítette az az PowerShell 4.2.0 vagy újabb.

    Azure PowerShell telepítéséhez tekintse meg a [hivatalos Azure PowerShell dokumentációját](/powershell/azure/install-az-ps).

1. Telepítse a Application Insights PowerShell-bővítményt.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Jelentkezzen be az Azure-fiókjával
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    A bejelentkezéssel kapcsolatos további információkért tekintse meg a [kapcsolódási AzAccount dokumentációját](/powershell/module/az.accounts/connect-azaccount).

1. Távolítsa el a Application Insights erőforráshoz társított korábbi Storage-fiókot.

    Minta
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Példa:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Kapcsolja össze a Storage-fiókját a Application Insights erőforrással.
    
    Minta
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Példa:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Konfigurálás az Azure CLI használatával

1. Győződjön meg arról, hogy telepítette az Azure CLI-t.

    Az Azure CLI telepítéséhez tekintse meg a [hivatalos Azure CLI dokumentációját](/cli/azure/install-azure-cli).

1. Telepítse a Application Insights CLI-bővítményt.
    ```powershell
    az extension add -n application-insights
    ```

1. Kapcsolja össze a Storage-fiókját a Application Insights erőforrással.

    Minta
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Példa:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Várt kimenet:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > A társított Storage-fiókok frissítéseinek a Application Insights erőforráshoz való végrehajtásához tekintse meg a [Application INSIGHTS CLI dokumentációját](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage).

#### <a name="configure-using-azure-resource-manager-template"></a>Konfigurálás Azure Resource Manager sablon használatával

1. Hozzon létre egy Azure Resource Manager sablonfájlt a következő tartalommal (byos.template.js).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Futtassa a következő PowerShell-parancsot az előző sablon üzembe helyezéséhez (társított Storage-fiók létrehozása).

    Minta
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Példa:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Adja meg a következő paramétereket, ha a rendszer a PowerShell-konzolon kéri:
    
    |           Paraméter           |                                Leírás                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | A BYOS engedélyezéséhez Application Insights erőforrás neve.            |
    | storage_account_name          | A BYOS használt Storage-fiók erőforrásának neve. |
    
    Várt kimenet:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Engedélyezheti a kód szintű diagnosztikát (Profiler/Debugger) a Azure Portalon keresztül elérhető számítási feladatokhoz. (App Service > Application Insights) _![ 2,0](media/profiler-bring-your-own-storage/figure-20.png)_. ábra, 
 _2,0_

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="template-schema-schema_uri-isnt-supported"></a>A (z) {schema_uri} sablon sémája nem támogatott.
* Győződjön meg arról, hogy a `$schema` sablon tulajdonsága érvényes. A következő mintát kell követnie: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Győződjön meg arról, hogy a `schema_version` sablon az érvényes értékeken belül van: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Hibaüzenet:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Nem található regisztrált erőforrás-szolgáltató a (z) {Location} helyen.
* Győződjön meg arról, hogy az `apiVersion` erőforrás `microsoft.insights/components` a `2015-05-01` .
* Győződjön meg arról, hogy az `apiVersion` erőforrás `linkedStorageAccount` a `2020-03-01-preview` .
    Hibaüzenet:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>A Storage-fiók helyének meg kell egyeznie az AI-összetevő helyével.
* Győződjön meg arról, hogy a Application Insights erőforrás helye megegyezik a Storage-fiókkal.
    Hibaüzenet:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

A Profiler általános hibaelhárításához tekintse meg a [Profiler hibaelhárítási dokumentációját](profiler-troubleshooting.md).

Az általános Snapshot Debugger hibaelhárításhoz tekintse meg a [Snapshot Debugger hibaelhárítási dokumentációját](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Gyakori kérdések
* Ha engedélyezve van a Profiler vagy a Snapshot, és Engedélyezem a BYOS-t, a rendszer áttelepíti az adataikat a saját Storage-fiókomba?
    _Nem, nem._

* A BYOS működik a titkosítással és a Customer-Managed kulccsal?
    _Igen, a BYOS a szükséges, hogy a Profiler/Debugger engedélyezve legyen Customer-Manager kulcsokkal._

* A BYOS az internetről elszigetelt környezetben fog működni?
    _igen. Valójában a BYOS az elkülönített hálózati forgatókönyvek követelménye._

* A BYOS működik, ha mindkét Customer-Managed kulcs és privát hivatkozás engedélyezve lett? 
    _Igen, lehetséges._

* Ha engedélyezve van a BYOS, Visszatérhetek a diagnosztikai szolgáltatások tárolási fiókjaival a gyűjtött adatok tárolására? 
    _Igen, de most már nem támogatjuk a BYOS való adatáttelepítést._

* A BYOS engedélyezése után átveszem az összes kapcsolódó költségét, amely a tárolás és a hálózatkezelés? 
    _Igen_
