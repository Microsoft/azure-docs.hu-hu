---
title: Környezet kezelése Azure Resource Manager sablonok használatával – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan kezelheti Azure Time Series Insights környezetét programozott módon Azure Resource Manager használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011659"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>1. generációs Azure Time Series Insights-erőforrások létrehozása Azure Resource Manager-sablonok használatával

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk azt ismerteti, hogyan hozhatók létre és helyezhetők üzembe Azure Time Series Insights erőforrások [Azure Resource Manager sablonok](../azure-resource-manager/index.yml), a PowerShell és a Azure Time Series Insights erőforrás-szolgáltató használatával.

A Azure Time Series Insights a következő erőforrásokat támogatja:

   | Erőforrás | Leírás |
   | --- | --- |
   | Környezet | A Azure Time Series Insights-környezetek az események olyan logikai csoportjai, amelyeket a rendszer az esemény-közvetítők, a tárolt és a lekérdezés számára elérhetővé tesz. További információért olvassa el [a Azure Time Series Insights-környezet megtervezése](time-series-insights-environment-planning.md) című témakört. |
   | Eseményforrás | Az eseményforrás egy olyan esemény-közvetítőhöz való kapcsolat, amelyből a Azure Time Series Insights beolvassa és betölti az eseményeket a környezetbe. A jelenleg támogatott eseményforrás a IoT Hub és az Event hub. |
   | Hivatkozási adathalmaz | A hivatkozási adatkészletek metaadatokat biztosítanak a környezetben lévő eseményekről. A rendszer a hivatkozási adatkészletekben lévő metaadatokat a beléptetéskor zajló eseményekhez fogja csatlakoztatni. A hivatkozási adatkészletek az esemény kulcsának tulajdonságai alapján határozzák meg az erőforrásokat. A hivatkozási adatkészletet alkotó tényleges metaadatokat a rendszer az adatsík API-kkal feltölti vagy módosítja. |
   | Hozzáférési szabályzat | A hozzáférési házirendek engedélyeket biztosítanak az adatlekérdezések kiküldéséhez, a környezetben lévő hivatkozási adat kezeléséhez, valamint a környezethez társított mentett lekérdezések és perspektívák megosztásához. További információért olvassa el [az adathozzáférés engedélyezése Azure Time Series Insights környezethez Azure Portal használatával](./concepts-access-policies.md) című témakört. |

A Resource Manager-sablon egy olyan JSON-fájl, amely meghatározza az erőforráscsoport erőforrásainak infrastruktúráját és konfigurációját. A következő dokumentumok részletesebben ismertetik a sablonfájlokat:

- [Azure Resource Manager sablon központi telepítése](../azure-resource-manager/templates/overview.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft. TimeSeriesInsights erőforrástípusok](/azure/templates/microsoft.timeseriesinsights/allversions)

Az [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) gyors üzembe helyezési sablont a githubon tesszük közzé. Ez a sablon létrehoz egy Azure Time Series Insights környezetet, egy alárendelt eseményforrás, amely az Event hub eseményeinek felhasználására van konfigurálva, és olyan hozzáférési házirendeket hoz létre, amelyek hozzáférést biztosítanak a környezeti adatokhoz. Ha nincs megadva egy meglévő Event hub, a rendszer létrehoz egyet a telepítéssel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Telepítési sablon és paraméterek megadása

Az alábbi eljárás azt ismerteti, hogyan használható a PowerShell egy olyan Azure Resource Manager sablon üzembe helyezéséhez, amely létrehoz egy Azure Time Series Insights-környezetet, egy alárendelt eseményforrás, amely az Event hub eseményeinek felhasználására van konfigurálva, valamint olyan hozzáférési házirendeket, amelyek hozzáférést biztosítanak a környezeti adatokhoz. Ha nincs megadva egy meglévő Event hub, a rendszer létrehoz egyet a telepítéssel.

1. Telepítse Azure PowerShell a [Azure PowerShell első lépéseinek](/powershell/azure/get-started-azureps)utasításait követve.

1. Az [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sablon klónozása vagy másolása a githubról.

   - Parameters-fájl létrehozása

     A Parameters fájl létrehozásához másolja a [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fájlt.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Kötelező paraméterek

     | Paraméter | Leírás |
     | --- | --- |
     | eventHubNamespaceName | A forrás-esemény központja névterét. |
     | eventHubName | A forrás-esemény hub neve. |
     | consumerGroupName | Annak a fogyasztói csoportnak a neve, amelyet a Azure Time Series Insights szolgáltatás az Event hub adatainak beolvasásához használ majd. **Megjegyzés:** Az erőforrás-tartalom elkerüléséhez ezt a fogyasztói csoportot a Azure Time Series Insights szolgáltatásnak kell elosztania, és nem kell más olvasókkal megosztania. |
     | environmentName | A környezet neve. A név nem tartalmazhatja a következőket:,,,,,,   `<` `>` `%` `&` `:` `\\` `?` , `/` és bármely vezérlő karaktert. Minden egyéb karakter engedélyezett.|
     | eventSourceName | Az eseményforrás alárendelt erőforrásának neve. A név nem tartalmazhatja a következőket:,,,,,,   `<` `>` `%` `&` `:` `\\` `?` , `/` és bármely vezérlő karaktert. Minden egyéb karakter engedélyezett. |

    <div id="optional-parameters"></div>

   - Opcionális paraméterek

     | Paraméter | Leírás |
     | --- | --- |
     | existingEventHubResourceId | Egy meglévő Event hub nem kötelező erőforrás-azonosítója, amely az eseményforrás használatával fog csatlakozni a Azure Time Series Insights környezethez. **Megjegyzés:** A sablont telepítő felhasználónak jogosultságokkal kell rendelkeznie a listkeys műveletének beolvasása művelet végrehajtásához az Event hub-on. Ha a rendszer nem ad át értéket, a sablon létrehoz egy új Event hub-t. |
     | environmentDisplayName | A környezet neve helyett az eszközökön vagy a felhasználói felületeken megjelenítendő, nem kötelezően megadandó felhasználóbarát név. |
     | environmentSkuName | A termékváltozat neve. További információért olvassa el a [Azure Time Series Insights díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Az SKU egységnyi kapacitása. További információért olvassa el a [Azure Time Series Insights díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | A környezet eseményeinek minimális TimeSpan lesznek elérhetők a lekérdezéshez. Az értéket ISO 8601 formátumban kell megadni, például `P30D` egy 30 napos adatmegőrzési szabályhoz. |
     | eventSourceDisplayName | Az eseményforrás neve helyett az eszközön vagy a felhasználói felületeken megjelenítendő, nem kötelezően megadandó felhasználóbarát név. |
     | eventSourceTimestampPropertyName | Az eseményforrás időbélyegzőként használt esemény tulajdonsága. Ha nincs megadva érték a timestampPropertyName, vagy null vagy üres karakterlánc van megadva, akkor a rendszer az esemény létrehozásának idejét fogja használni. |
     | eventSourceKeyName | Annak a megosztott elérési kulcsnak a neve, amelyet a Azure Time Series Insights szolgáltatás fog használni az Event hub-hoz való kapcsolódáshoz. |
     | accessPolicyReaderObjectIds | Az Azure AD-beli felhasználók vagy alkalmazások objektumazonosítók listáját, amelyeknek olvasói hozzáféréssel kell rendelkezniük a környezethez. Az egyszerű szolgáltatásnév objectId a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok meghívásával szerezhető be. Az Azure AD-csoportok hozzáférési házirendjének létrehozása még nem támogatott. |
     | accessPolicyContributorObjectIds | Az Azure AD azon felhasználói vagy alkalmazásaihoz tartozó objektumazonosítók listája, amelyeknek közreműködői hozzáféréssel kell rendelkezniük a környezethez. Az egyszerű szolgáltatásnév objectId a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok meghívásával szerezhető be. Az Azure AD-csoportok hozzáférési házirendjének létrehozása még nem támogatott. |

   - Példaként a következő paramétereket fogja használni egy környezet és egy olyan eseményforrás létrehozásához, amely egy meglévő Event hub eseményeinek olvasására szolgál. Emellett két olyan hozzáférési szabályzatot is létrehoz, amelyek közreműködői hozzáférést biztosítanak a környezethez.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }
         }
     }
     ```

   - További információért olvassa el a [paraméterekkel](../azure-resource-manager/templates/parameter-files.md) kapcsolatos cikket.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>A gyors üzembe helyezési sablon központi telepítése a PowerShell használatával

> [!IMPORTANT]
> Az alább látható parancssori műveletek az az PowerShell- [modult](/powershell/azure/)írják le.

1. A PowerShellben jelentkezzen be az Azure-fiókjába.

    - A PowerShell-parancssorból futtassa a következő parancsot:

      ```powershell
      Connect-AzAccount
      ```

    - A rendszer felszólítja, hogy jelentkezzen be az Azure-fiókjába. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

      ```powershell
      Get-AzSubscription
      ```

    - Ez a parancs az elérhető Azure-előfizetések listáját adja vissza. A következő parancs futtatásával válasszon egy előfizetést az aktuális munkamenethez. Cserélje le a helyére a `<YourSubscriptionId>` használni kívánt Azure-előfizetés GUID azonosítóját:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot, ha még nem létezik ilyen.

   - Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy új erőforráscsoportot a **New-AzResourceGroup** paranccsal. Adja meg az erőforráscsoport nevét és a használni kívánt helyet. Például:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Ha ez sikeres, megjelenik az új erőforráscsoport összefoglalása.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Tesztelje az üzemelő példányt.

   - Ellenőrizze az üzemelő példányt a `Test-AzResourceGroupDeployment` parancsmag futtatásával. A központi telepítés tesztelésekor pontosan úgy adja meg a paramétereket, ahogy az üzemelő példány végrehajtásakor.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. A központi telepítés létrehozása

    - Az új központi telepítés létrehozásához futtassa a `New-AzResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, ha a rendszer kéri. A paraméterek közé tartozik az üzemelő példány neve, az erőforráscsoport neve, valamint a sablonfájl elérési útja vagy URL-címe. Ha a **Mode** paraméter nincs megadva, a **növekményes** érték alapértelmezett értékét használja a rendszer. További információért olvassa el a [növekményes és a teljes telepítések](../azure-resource-manager/templates/deployment-modes.md)című témakört.

    - A következő parancs a PowerShell-ablak öt szükséges paraméterének megadását kéri:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Ha ehelyett a paramétereket szeretné megadni, használja a következő parancsot:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - A telepítési parancsmag futtatásakor beágyazott paramétereket is használhat. A parancs formátuma a következő:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - A [teljes](../azure-resource-manager/templates/deployment-modes.md) telepítés futtatásához állítsa a **Mode** paramétert a **befejezéshez**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Az üzemelő példány ellenőrzése

    - Ha az erőforrások központi telepítése sikeresen megtörtént, a központi telepítés összegzése megjelenik a PowerShell-ablakban:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. A rövid útmutató sablonjának üzembe helyezése a Azure Portal

   - A Gyorsindítás sablon kezdőlapja a GitHubon is tartalmaz egy **üzembe helyezés az Azure-** ban gombot. A gombra kattintva megnyílik a Azure Portal egy egyéni központi telepítési lapja. Ezen a lapon megadhatja vagy kiválaszthatja az egyes paraméterek értékeit a [szükséges paraméterekből](#required-parameters) vagy [választható paraméterek](#optional-parameters) táblából. A beállítások kitöltése után a **vásárlás** gombra kattintva megkezdheti a sablon telepítését.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Következő lépések

- A REST API-k használatával történő Azure Time Series Insights erőforrások programozott kezelésével kapcsolatos információkért olvassa el [Azure Time Series Insights felügyelet](/rest/api/time-series-insights-management/)című témakört.