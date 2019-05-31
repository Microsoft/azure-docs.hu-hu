---
title: Az Azure Resource Manager-sablonok az Azure Time Series Insights-környezet kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezeli az Azure Time Series Insights-környezetet, programozott módon az Azure Resource Manager használatával.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: ba7d412e9bfc29a53cd0aa47a926f60580b45490
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237640"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával a Time Series Insights-erőforrások létrehozása

Ez a cikk bemutatja, hogyan hozhat létre és telepíthet a Time Series Insights-erőforrások Azure Resource Manager-sablonok, a PowerShell és a Time Series Insights erőforrás-szolgáltató.

A Time Series Insights támogatja az alábbi forrásanyagokat:

   | Resource | Leírás |
   | --- | --- |
   | Környezet | Egy Time Series Insights-környezet az eseményeket, amelyek eseményszervezőként olvasni, tárolja és elérhetővé vált a lekérdezés logikai csoportosítása. További információkért lásd: [az Azure Time Series Insights-környezet megtervezése](time-series-insights-environment-planning.md) |
   | Eseményforrás | Eseményforrás egy eseményközvetítőből, amelyről a Time Series Insights olvassa be, és jól alkalmazkodik a környezetbe való kapcsolat. Az IoT Hub és az Eseményközpont jelenleg támogatott esemény forrásai. |
   | Referencia-adatkészlet | Referencia-adatkészletekhez adja meg, hogy a környezetben eseményekkel kapcsolatos metaadatokat. A referencia-adatkészletekhez metaadatok tartományhoz lesz csatlakoztatva eseményekkel rendelkező bejövő során. Referencia-adatkészletekhez által az esemény kulcstulajdonságok erőforrások vannak meghatározva. A tényleges metaadatok, hogy a referencia-adatkészlet feltöltött vagy módosítani az adatsík API-k használatával. |
   | Hozzáférési szabályzat | Hozzáférési szabályzatok az adatlekérdezések kiadása, referenciaadatok a környezetben, módosíthatók és jelenthetők megosztása a mentett lekérdezéseket és perspektívákat a környezet társított engedélyeket. További információkért olvassa el [adathozzáférés biztosítása egy Time Series Insights-környezet az Azure portal használatával](time-series-insights-data-access.md) |

A Resource Manager-sablon egy JSON-fájlt, amely meghatározza az infrastruktúra és az erőforrások egy erőforráscsoportban. A következő dokumentumok sablonfájlokat részletesebben ismertetik:

- [Az Azure Resource Manager áttekintése – sablon telepítése](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights erőforrástípusok](/azure/templates/microsoft.timeseriesinsights/allversions)

A [201-timeseriesinsights-környezet-a-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) gyorssablont a Githubon közzétett. Ez a sablon létrehoz egy Time Series Insights környezetet, a gyermek eseményforrás úgy konfigurálva, hogy az Eseményközpontban lévő események felhasználásához és hozzáférési házirendek, amelyek hozzáférést biztosítani a környezeti adatok. Ha egy meglévő Eseményközponton nincs megadva, az egyik való üzembe helyezéséhez létrejön.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Adja meg a központi telepítési sablon és paraméterek

Az alábbi eljárás ismerteti a PowerShell használatával történő üzembe helyezése Azure Resource Manager-sablon, amely létrehoz egy Time Series Insights környezetet, a gyermek eseményforrás úgy konfigurálva, hogy az Eseményközpontban lévő események felhasználásához és hozzáférési házirendek, amelyek hozzáférést biztosítanak a a környezet adatait. Ha egy meglévő Eseményközponton nincs megadva, az egyik való üzembe helyezéséhez létrejön.

1. Azure PowerShell telepítéséhez a következő témakör utasításait követve [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps).

1. Klónozás vagy másolja ki a [201-timeseriesinsights-környezet-a-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sablont a Githubból.

   * A paraméterfájl létrehozása

     Hozzon létre egy paramétereket tartalmazó fájlt, másolja át a [201-timeseriesinsights-környezet-a-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fájlt.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Szükséges paraméterek

     | Paraméter | Leírás |
     | --- | --- |
     | eventHubNamespaceName | Az adatforrás event hubs-névtér. |
     | eventHubName | A forrás eseményközpont neve. |
     | consumerGroupName | A fogyasztói csoportot, amelynek használatával a Time Series Insights szolgáltatás az adatok olvasásához az event hubs neve. **MEGJEGYZÉS:** Az Erőforrásverseny elkerülése érdekében, a fogyasztói csoportot lehet a Time Series Insights szolgáltatás számára dedikált, és más olvasók sincs megosztva. |
     | EnvironmentName | A környezeti változó nevére. A név nem tartalmazhat: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, és bármelyik karakter. Minden egyéb karakter engedélyezett.|
     | eventSourceName | Az esemény forrás gyermek-erőforrás neve. A név nem tartalmazhat: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, és bármelyik karakter. Minden egyéb karakter engedélyezett. |

    <div id="optional-parameters"></div>

   * Választható paraméterek:

     | Paraméter | Leírás |
     | --- | --- |
     | existingEventHubResourceId | Egy nem kötelező erőforrás-azonosítója egy meglévő Eseményközponttal, amelyek a Time Series Insights-környezet az eseményforrás keresztül fog csatlakozni. **MEGJEGYZÉS:** A felhasználó helyezi üzembe a sablont az Event Hubs listkeys műveletének művelethez jogosultsággal kell rendelkeznie. Ha nem ad meg értéket fogad el, egy új eseményközpont létrejön a sablon által. |
     | environmentDisplayName | A környezet neve helyett azokat az eszközöket, vagy a felhasználói felületen megjelenítendő nem kötelező rövid neve. |
     | environmentSkuName | A termékváltozat neve. További információkért lásd: a [Time Series Insights díjszabása oldalon](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | A termékváltozat egység kapacitását. További információkért lásd: a [Time Series Insights díjszabása oldalon](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | A minimális időtartam a környezet események lesznek elérhetők a lekérdezéshez. A kötelező értéket megadni az ISO 8601 formátumú, például `P30D` egy megőrzési szabályzat 30 napig. |
     | eventSourceDisplayName | Egy nem kötelező rövid név azokat az eszközöket, vagy a felhasználói felületen helyett az eseményforrás nevének megjelenítéséhez. |
     | eventSourceTimestampPropertyName | Az esemény tulajdonság, amely az eseményforrás időbélyegzőként használható. Ha az érték nincs megadva a timestampPropertyName, vagy az null értékű vagy üres karakterlánc van megadva, az esemény létrehozásának időpontját használható. |
     | eventSourceKeyName | A közös hozzáférési kulcs, amely a Time Series Insights szolgáltatás használni fog csatlakozni az eseményközpont neve. |
     | accessPolicyReaderObjectIds | A felhasználók vagy alkalmazások az Azure ad-ben, amely elvben olvasó hozzáférést a környezethez azonosítói objektum listáját. A szolgáltatás egyszerű objectId meghívásával is beszerezhetők a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok. Az Azure AD-csoportok a hozzáférési szabályzat létrehozása még nem támogatott. |
     | accessPolicyContributorObjectIds | A felhasználók vagy alkalmazások, amelyek a környezetet közreműködői hozzáféréssel kell rendelkeznie az Azure AD-ben objektumazonosítók listáját. A szolgáltatás egyszerű objectId meghívásával is beszerezhetők a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok. Az Azure AD-csoportok a hozzáférési szabályzat létrehozása még nem támogatott. |

   * Tegyük fel a következő paramétereket tartalmazó fájlt szeretne használható egy környezetet, és a egy eseményforrás, amely eseményeket olvas be egy meglévő eseményközponton. A környezet közreműködői hozzáférést két hozzáférési házirendeket is létrehoz.

     ```json
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
  
    * További információkért lásd: a [paraméterek](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) cikk.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>A helyi PowerShell-lel gyorsindítási sablon üzembe helyezése

> [!IMPORTANT]
> Az alábbiakban a parancssori műveleteket ismertetik a [Az PowerShell-modul](https://docs.microsoft.com/powershell/azure/overview).

1. A PowerShellben jelentkezzen be az Azure-fiókjával.

    * Egy PowerShell-parancssorban futtassa a következő parancsot:

      ```powershell
      Connect-AzAccount
      ```

    * Jelentkezzen be az Azure-fiókja kéri. A bejelentkezés után futtassa a következő parancsot a rendelkezésre álló előfizetések megtekintéséhez:

      ```powershell
      Get-AzSubscription
      ```

    * Ez a parancs elérhető Azure-előfizetések listáját adja vissza. Válasszon egy előfizetést, az aktuális munkamenet a következő parancs futtatásával. Cserélje le `<YourSubscriptionId>` használni kívánt Azure-előfizetéshez tartozó GUID Azonosítóval rendelkező:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot, ha még nem létezik.

   * Ha nem rendelkezik egy meglévő erőforrást, csoport, hozzon létre egy új erőforráscsoportot a **New-AzResourceGroup** parancsot. Adja meg az erőforráscsoportot és helyet használni kívánt nevét. Példa:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Ha ez sikeres, az új erőforráscsoport összegzését jelenik meg.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. A telepítés tesztelésére.

   * A telepítés ellenőrzése futtatásával a `Test-AzResourceGroupDeployment` parancsmagot. A központi telepítés tesztelésekor paramétereket megadnia, pontosan, mint a központi telepítés végrehajtása közben.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Az üzemelő példány létrehozása

    * Az új központi telepítés létrehozásához futtassa a `New-AzResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméterek tartalmaznia kell egy nevet az üzembe helyezés a sablonfájl az erőforráscsoport és az elérési útja vagy URL-cím nevére. Ha a **mód** paraméter nincs megadva, az alapértelmezett érték **növekményes** szolgál. További információkért lásd: [növekményes és teljes körű központi telepítések](../azure-resource-manager/deployment-modes.md).

    * A következő parancs kéri a öt szükséges paraméterek a PowerShell-ablakban:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Ehelyett adja meg a paramétereket tartalmazó fájlt, használja a következő parancsot:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Emellett használhatja a beágyazott paraméterek, a központi telepítési parancsmag futtatásakor. A parancs a következőképpen történik:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Futtatásához egy [teljes](../azure-resource-manager/deployment-modes.md) központi telepítését, állítsa be a **mód** paramétert **Complete**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Az üzemelő példány ellenőrzése

    * Ha az erőforrások telepítése sikeresen megtörtént, a központi telepítés összegzését a PowerShell-ablakban jelenik meg:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 5/8/2019 10:28:34 PM
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

1. Az Azure Portalon a rövid útmutató sablon üzembe helyezése

   * A gyorsindítási sablon kezdőlapja a GitHub is tartalmaz egy **üzembe helyezés az Azure** gombra. Egy egyéni üzembe helyezés lap kattint, megnyílik az Azure Portalon. Ezen az oldalon adja meg vagy válassza ki az értékeket minden, a paraméterek a [szükséges paraméterek](#required-parameters) vagy [választható paraméterek](#optional-parameters) táblákat. A beállítások gombra kattintva mezőinek kitöltése után a **beszerzési** gombra a sablon telepítése megkezdődik.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>További lépések

- Információk a szoftveres kezelése a REST API-k a Time Series Insights-erőforrások: [Time Series Insights-felügyelet](https://docs.microsoft.com/rest/api/time-series-insights-management/).
