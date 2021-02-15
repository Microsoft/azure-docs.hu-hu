---
title: Sablon functions – erőforrások
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti az erőforrásokra vonatkozó értékek lekéréséhez.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: da85308e7d214f198b29b40bc380a4d33947c865
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364562"
---
# <a name="resource-functions-for-arm-templates"></a>Az ARM-sablonokhoz tartozó Resource functions

A Resource Manager a következő függvényeket biztosítja az erőforrások értékének lekéréséhez a Azure Resource Manager-sablonban (ARM-sablon):

* [extensionResourceId](#extensionresourceid)
* [listáját](#list)
* [pickZones](#pickzones)
* [szolgáltatók](#providers)
* [referencia](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [előfizetés](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

A paraméterek, változók vagy a jelenlegi központi telepítés értékeinek lekéréséhez lásd: a [központi telepítési érték függvényei](template-functions-deployment.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

A [bővítmény erőforrásának](../management/extension-resource-types.md)erőforrás-azonosítóját adja vissza, amely egy másik erőforrásra alkalmazott erőforrástípus, amely a képességeihez való hozzáadásra szolgál.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| resourceId |Yes |sztring |Annak az erőforrásnak az erőforrás-azonosítója, amelyre a bővítmény erőforrása vonatkozik. |
| resourceType |Yes |sztring |Az erőforrás típusa, beleértve az erőforrás-szolgáltatói névteret. |
| resourceName1 |Yes |sztring |Az erőforrás neve. |
| resourceName2 |No |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Visszatérési érték

A függvény által visszaadott erőforrás-azonosító alapszintű formátuma a következő:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

A hatókör szegmense a kibővített erőforrástól függ.

Ha a bővítmény erőforrása egy **erőforrásra** vonatkozik, a rendszer az erőforrás-azonosítót a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrását egy **erőforráscsoport** alkalmazza, a formátum a következő:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrása egy **előfizetésre** van alkalmazva, a formátum a következő:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrását egy **felügyeleti csoportra** alkalmazza, a formátum a következő:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId példa

A következő példa egy erőforráscsoport-zárolás erőforrás-AZONOSÍTÓját adja vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Egy felügyeleti csoportba telepített egyéni szabályzat-definíció kiterjesztési erőforrásként van implementálva. Házirend létrehozásához és hozzárendeléséhez telepítse a következő sablont egy felügyeleti csoportba.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

A beépített szabályzat-definíciók a bérlői szintű erőforrások. A beépített szabályzat-definíciók üzembe helyezésére példát a következő témakörben talál: [tenantResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>listáját

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

A függvény szintaxisa a lista műveleteinek nevével változik. Minden implementáció a lista műveletét támogató erőforrástípus értékeit adja vissza. A művelet nevének kezdődnie kell, `list` és lehet utótagja. Néhány gyakori használat:, `list` , `listKeys` `listKeyValue` és `listSecrets` .

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Yes |sztring |Az erőforrás egyedi azonosítója. |
| apiVersion |Yes |sztring |Az erőforrás-futtatókörnyezet állapotának API-verziója. Általában az **éééé-hh-nn** formátumban kell megadni. |
| functionValues |No |object | Egy objektum, amely a függvény értékeit tartalmazta. Csak olyan függvényeknek adja meg ezt az objektumot, amelyek támogatják a paraméterek értékeit, például a **listAccountSas** . Ebben a cikkben látható egy példa a függvény értékének átadására. |

### <a name="valid-uses"></a>Érvényes használati módok

A List függvények az erőforrás-definíciók tulajdonságaiban is használhatók. Ne használjon olyan lista függvényt, amely bizalmas adatokat tesz elérhetővé a sablon outputs (kimenet) szakaszában. A kimeneti értékek tárolása a központi telepítési előzményekben történik, és egy rosszindulatú felhasználó lekérhető.

Ha tulajdonság- [iterációt](copy-properties.md)használ, használhatja a List függvényeket, `input` mert a kifejezés hozzá van rendelve az erőforrás tulajdonsághoz. Ezeket nem használhatja, `count` mert a Count függvényt a lista funkció feloldása előtt kell meghatározni.

### <a name="implementations"></a>Megvalósítások

A (z) * lista lehetséges felhasználási módjai a következő táblázatban láthatók.

| Erőforrás típusa | Függvény neve |
| ------------- | ------------- |
| Microsoft. addons/supportProviders | listsupportplaninfo |
| Microsoft. AnalysisServices/kiszolgálók | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. ApiManagement/Service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft. ApiManagement/szolgáltatás/átjárók | [Listkeys műveletének beolvasása](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft. ApiManagement/Service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft. ApiManagement/Service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft. ApiManagement/Service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft. ApiManagement/szolgáltatás/előfizetések | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Microsoft. AppConfiguration/configurationStores | [Listkeys műveletének beolvasása](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft. AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft. Automation/automationAccounts | [Listkeys műveletének beolvasása](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.BatCH/batchAccounts | [listkeys műveletének beolvasása](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/munkaterületek/kísérletek/feladatok | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/csatornák | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft. cache/Redis | [Listkeys műveletének beolvasása](/rest/api/redis/redis/listkeys) |
| Microsoft. CognitiveServices/fiókok | [Listkeys műveletének beolvasása](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/nyilvántartók | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/nyilvántartók | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/nyilvántartók | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/nyilvántartók/agentpools | listQueueStatus |
| Microsoft. ContainerRegistry/nyilvántartók/buildTasks | listSourceRepositoryProperties |
| Microsoft. ContainerRegistry/nyilvántartások/buildTasks/lépések | listBuildArguments |
| Microsoft. ContainerRegistry/nyilvántartók/taskruns | listDetails |
| Microsoft. ContainerRegistry/nyilvántartók/webhookok | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/nyilvántartások/futtatások | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/nyilvántartások/feladatok | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. Tárolószolgáltatás/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. Tárolószolgáltatás/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft. Tárolószolgáltatás/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. Tárolószolgáltatás/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/feladatok | listCredentials |
| Microsoft. DataFactory/datafactories/átjárók | listauthkeys |
| Microsoft. DataFactory/gyárak/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/fiókok/storageAccounts/tárolók | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. DataShare/fiókok/megosztások | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft. DataShare/fiókok/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. DataShare/fiókok/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. DataShare/fiókok/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft. Devices/iotHubs | [listkeys műveletének beolvasása](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/iotHubKeys | [listkeys műveletének beolvasása](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft. Devices/provisioningServices/Keys | [listkeys műveletének beolvasása](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [listkeys műveletének beolvasása](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. segédösszetevője/Labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft. segédösszetevője/Labs/ütemtervek | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. segédösszetevője/Labs/felhasználók/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. segédösszetevője/Labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [Listkeys műveletének beolvasása](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2020-06-01/notebookworkspaces/listconnectioninfo) |
| Microsoft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/tartományok | [Listkeys műveletének beolvasása](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/témakörök | [Listkeys műveletének beolvasása](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft. EventHub/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/eventhub) |
| Microsoft. EventHub/névterek/disasterRecoveryConfigs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/eventhub) |
| Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/eventhub) |
| Microsoft. ImportExport/feladatok | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/fürtök/adatbázisok | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft. LabServices/felhasználók | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/felhasználók | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/szerződések | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/szerelvények | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/partnerek | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/sémák | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft. Logic/munkafolyamatok | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft. Logic/munkafolyamatok | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/munkafolyamatok/futtatások/műveletek | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/munkafolyamatok/futtatások/műveletek/ismétlődések | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/munkafolyamatok/triggerek | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/munkafolyamatok/verziók/eseményindítók | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/webszolgáltatások | [listkeys műveletének beolvasása](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/munkaterületek | listworkspacekeys |
| Microsoft. MachineLearningServices/munkaterületek/számítások | [Listkeys műveletének beolvasása](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/munkaterületek/számítások | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft. MachineLearningServices/munkaterületek | [Listkeys műveletének beolvasása](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/fiókok | [Listkeys műveletének beolvasása](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/Mediaservices/eszközök | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/Mediaservices/eszközök | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/Mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/Mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/munkaterületek | [listáját](/rest/api/loganalytics/workspaces/list) |
| Microsoft. OperationalInsights/munkaterületek | Listkeys műveletének beolvasása |
| Microsoft. PolicyInsights/szervizelések | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft. RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft. Relay/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/névterek/disasterRecoveryConfigs/engedélyezési szabályok | listkeys műveletének beolvasása |
| Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/névterek/várólisták/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft. SignalRService/szignáló | [listkeys műveletének beolvasása](/rest/api/signalr/signalr/listkeys) |
| Microsoft. Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft. Storage/storageAccounts | [listkeys műveletének beolvasása](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft. Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/vezetők/eszközök | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/vezetők/eszközök | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/vezetők | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/vezetők | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. szinapszis/munkaterületek/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft. Web/connectionGateways | ListStatus |
| Microsoft. Web/kapcsolatok | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/Locations | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok | listsecrets |
| Microsoft. Web/Sites/Backups | [listáját](/rest/api/appservice/webapps/listbackups) |
| Microsoft. Web/Sites/config | [listáját](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft. Web/Sites/functions | [listkeys műveletének beolvasása](/rest/api/appservice/webapps/listfunctionkeys)
| Microsoft. Web/Sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/Relays | [listkeys műveletének beolvasása](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| Microsoft. Web/Sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/Slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| Microsoft. Web/Sites/Slots/Backups | [listáját](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft. Web/Sites/Slots/config | [listáját](/rest/api/appservice/webapps/listconfigurationsslot) |
| Microsoft. Web/Sites/Slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Annak megállapításához, hogy mely erőforrástípusok rendelkeznek lista-művelettel, a következő lehetőségek közül választhat:

* Tekintse meg az erőforrás-szolgáltató [REST API műveleteit](/rest/api/) , és keresse meg a lista műveleteit. A Storage-fiókok például rendelkeznek a [listkeys műveletének beolvasása művelettel](/rest/api/storagerp/storageaccounts).
* Használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell-parancsmagot. A következő példa beolvassa a Storage-fiókok összes műveletét:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* A következő Azure CLI-parancs használatával csak a lista műveleteit szűrheti:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektum a használt lista függvénytől függ. A Storage-fiók Listkeys műveletének beolvasása például a következő formátumot adja vissza:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

A többi lista függvények eltérő visszatérési formátummal rendelkeznek. A függvények formátumának megtekintéséhez vegye fel azt a kimenetek szakaszba, ahogyan az a példában szereplő sablonban látható.

### <a name="remarks"></a>Megjegyzések

Adja meg az erőforrást az erőforrás neve vagy a [resourceId függvény](#resourceid)használatával. Ha egy, a hivatkozott erőforrást telepítő sablonban egy list függvényt használ, használja az erőforrás nevét.

Ha feltételesen telepített erőforrásban használ egy **List** függvényt, akkor a függvény akkor is kiértékelésre kerül, ha az erőforrás nincs telepítve. Hibaüzenet jelenik meg, ha a **lista** függvény olyan erőforrásra hivatkozik, amely nem létezik. Az **IF** függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor lesz kiértékelve. Tekintse meg a if [függvényt](template-functions-logical.md#if) egy olyan minta sablon esetében, amely a IF és a listát feltételesen telepített erőforrást használja.

### <a name="list-example"></a>Példa a listára

Az alábbi példa Listkeys műveletének beolvasása használ a [telepítési parancsfájlok](deployment-script-template.md)értékének beállításakor.

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

A következő példa egy list függvényt mutat be, amely egy paramétert vesz igénybe. Ebben az esetben a függvény **listAccountSas**. Egy objektum továbbítása a lejárati időhöz. A lejárati időnek a jövőben kell lennie.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Meghatározza, hogy az erőforrástípus támogatja-e egy adott régió zónáit.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace | Yes | sztring | Az erőforrás-szolgáltató névterét, amely a zónák támogatását keresi. |
| resourceType | Yes | sztring | A zóna támogatásának kereséséhez használt erőforrástípus. |
| location | Yes | sztring | A zóna támogatását támogató régió. |
| numberOfZones | No | egész szám | A visszaadni kívánt logikai zónák száma. Az alapértelmezett érték 1. A számnak 1 és 3 közötti pozitív egész számnak kell lennie.  Az egyzónás erőforrások esetében az 1 érték használható. A többzónás erőforrások esetében az értéknek kisebbnek vagy egyenlőnek kell lennie a támogatott zónák számával. |
| offset | No | egész szám | A kezdő logikai zóna eltolása. A függvény hibát ad vissza, ha az eltolás plusz numberOfZones meghaladja a támogatott zónák számát. |

### <a name="return-value"></a>Visszatérési érték

A támogatott zónákat tartalmazó tömb. Az eltolás és a numberOfZones alapértelmezett értékeinek használatakor a zónákat támogató erőforrástípus és régió a következő tömböt adja vissza:

```json
[
    "1"
]
```

Ha a paraméter értéke 3, a a következőt `numberOfZones` adja vissza:

```json
[
    "1",
    "2",
    "3"
]
```

Ha az erőforrás típusa vagy régiója nem támogatja a zónákat, a rendszer üres tömböt ad vissza.

```json
[
]
```

### <a name="pickzones-example"></a>pickZones példa

A következő sablon három eredményt mutat a pickZones függvény használatához.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

Az előző példák kimenete három tömböt ad vissza.

| Név | Típus | Érték |
| ---- | ---- | ----- |
| támogatott | array | ["1"] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

A pickZones válaszával határozhatja meg, hogy null értéket kíván-e biztosítani a zónák számára, vagy a virtuális gépeket különböző zónákhoz rendeli. A következő példa a zónák rendelkezésre állása alapján állítja be a zóna értékét.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> A hurkok és a copyIndex () még nincsenek implementálva.  Lásd: [hurkok](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

## <a name="providers"></a>szolgáltatók

`providers(providerNamespace, [resourceType])`

Egy erőforrás-szolgáltatóval és annak támogatott erőforrásaival kapcsolatos információkat ad vissza. Ha nem ad meg erőforrástípust, a függvény az erőforrás-szolgáltató összes támogatott típusát adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |sztring |A szolgáltató névtere |
| resourceType |No |sztring |Az erőforrás típusa a megadott névtéren belül. |

### <a name="return-value"></a>Visszatérési érték

Minden támogatott típus a következő formátumban lesz visszaadva:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

A visszaadott értékek tömbbeli sorrendje nem garantált.

### <a name="providers-example"></a>Szolgáltatók – példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) a szolgáltatói függvény használatát mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

A **Microsoft. Web** erőforrás-szolgáltató és a **helyek** erőforrástípus esetében az előző példa egy objektumot ad vissza a következő formátumban:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>referencia

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Egy erőforrás futásidejű állapotát jelképező objektumot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Yes |sztring |Egy erőforrás neve vagy egyedi azonosítója. Ha az aktuális sablonban lévő erőforrásra hivatkozik, csak az erőforrás nevét adja meg paraméterként. Ha egy korábban központilag telepített erőforrásra hivatkozik, vagy ha az erőforrás neve nem egyértelmű, adja meg az erőforrás-azonosítót. |
| apiVersion |No |sztring |A megadott erőforrás API-verziója. **Ezt a paramétert akkor kell megadni, ha az erőforrás nincs kiépítve ugyanazon a sablonon belül.** Általában az **éééé-hh-nn** formátumban kell megadni. Az erőforrás érvényes API-verzióihoz lásd: [sablon-hivatkozás](/azure/templates/). |
| Teljes |No |sztring |Az érték, amely megadja, hogy a rendszer visszaadja-e a teljes erőforrás-objektumot. Ha nem adja meg `'Full'` , csak az erőforrás tulajdonságok objektuma lesz visszaadva. A teljes objektum olyan értékeket tartalmaz, mint például az erőforrás-azonosító és a hely. |

### <a name="return-value"></a>Visszatérési érték

Minden erőforrástípus a hivatkozási függvény különböző tulajdonságait adja vissza. A függvény nem ad vissza egyetlen, előre definiált formátumot. Továbbá a visszaadott érték az argumentum értéke alapján eltér `'Full'` . Az erőforrástípus tulajdonságainak megtekintéséhez adja vissza az objektumot a kimenetek szakaszban, ahogy az a példában látható.

### <a name="remarks"></a>Megjegyzések

A Reference függvény egy korábban központilag telepített erőforrás vagy egy, az aktuális sablonban üzembe helyezett erőforrás futásidejű állapotát kérdezi le. Ez a cikk mindkét forgatókönyvhöz mutat példákat.

A **Reference** függvénnyel általában egy adott értéket adhat vissza egy objektumból, például a blob-végpont URI-ját vagy teljes tartománynevét.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Akkor használja `'Full'` , ha olyan erőforrás-értékekre van szüksége, amelyek nem részei a tulajdonságok sémájának. A Key Vault hozzáférési házirendjeinek beállításához például szerezze be a virtuális gép identitásának tulajdonságait.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Érvényes használati módok

A Reference függvény csak az erőforrás-definíció tulajdonságaiban és egy sablon vagy központi telepítés kimenetek szakaszában használható. Ha tulajdonság- [iterációt](copy-properties.md)használ, használhatja a Reference függvényt, `input` mert a kifejezés hozzá van rendelve az erőforrás tulajdonsághoz.

A Reference függvény nem használható másolási hurokban lévő tulajdonság értékének megadásához `count` . A paranccsal más tulajdonságokat is megadhat a hurokban. A Count tulajdonsághoz a hivatkozás le van tiltva, mert a hivatkozási függvény feloldása előtt meg kell határozni a tulajdonságot.

Ha egy beágyazott sablon kimenetek szakaszában a Reference függvényt vagy bármely lista * függvényt szeretné használni, akkor a  ```expressionEvaluationOptions``` [belső hatókör](linked-templates.md#expression-evaluation-scope-in-nested-templates) kiértékeléséhez kell beállítania a értéket, vagy egy beágyazott sablon helyett egy csatolást kell használnia.

Ha a **hivatkozási** függvényt egy feltételesen üzembe helyezett erőforrásban használja, akkor a függvény akkor is ki lesz értékelve, ha az erőforrás nincs telepítve.  Hibaüzenet jelenik meg, ha a **hivatkozási** függvény olyan erőforrásra hivatkozik, amely nem létezik. Az **IF** függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor lesz kiértékelve. Tekintse meg az IF [függvényt](template-functions-logical.md#if) egy olyan sablon esetében, amely a IF és a hivatkozást egy feltételesen telepített erőforrással használja.

### <a name="implicit-dependency"></a>Implicit függőség

A Reference függvény használatával implicit módon deklarálhatja, hogy egy erőforrás egy másik erőforrástól függ, ha a hivatkozott erőforrás ugyanabban a sablonban van kiépítve, és az erőforrásra a neve alapján hivatkozik (nem erőforrás-azonosító). Nem kell a dependsOn tulajdonságot is használni. A függvény nem lesz kiértékelve, amíg a hivatkozott erőforrás üzembe helyezése befejeződött.

### <a name="resource-name-or-identifier"></a>Erőforrás neve vagy azonosítója

Ha egy olyan erőforrásra hivatkozik, amely ugyanabban a sablonban van telepítve, adja meg az erőforrás nevét.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

Ha olyan erőforrásra hivatkozik, amely nem ugyanabban a sablonban van telepítve, adja meg az erőforrás-azonosítót és a-t `apiVersion` .

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

Annak elkerülése érdekében, hogy a rendszer melyik erőforrásra hivatkozik, megadhat egy teljesen minősített erőforrás-azonosítót.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

Egy erőforrásra vonatkozó teljes körű hivatkozás létrehozásakor a szegmensek és a név összevonásának sorrendje nem csupán a kettő összefűzése. Ehelyett a névtér után olyan *típusú/név* párokat használjon, amelyek legalább a legpontosabbak:

**{erőforrás-szolgáltató-névtér}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-name}]**

Például:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` helyes `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` , helytelen

Az erőforrás-AZONOSÍTÓk létrehozásának egyszerűbbé tétele érdekében a `resourceId()` függvény helyett használja a jelen dokumentumban ismertetett függvényeket `concat()` .

### <a name="get-managed-identity"></a>Felügyelt identitás beolvasása

[Az Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md) az egyes erőforrásokhoz implicit módon létrehozott [bővítmény-erőforrástípusok](../management/extension-resource-types.md) . Mivel a felügyelt identitás nincs explicit módon definiálva a sablonban, hivatkozni kell arra az erőforrásra, amelyre az identitás vonatkozik. A használatával az `Full` összes tulajdonságot beolvashatja, beleértve az implicit módon létrehozott identitást is.

A minta a következő:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Ha például egy virtuális gépre alkalmazott felügyelt identitás elsődleges AZONOSÍTÓját szeretné lekérni, használja a következőt:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Vagy egy virtuálisgép-méretezési csoportra alkalmazott felügyelt identitás bérlői AZONOSÍTÓjának lekéréséhez használja a következőt:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>Példa hivatkozásra

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) egy erőforrást telepít, és hivatkozik erre az erőforrásra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

Az előző példa a két objektumot adja vissza. A tulajdonságok objektum formátuma a következő:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

A teljes objektum formátuma a következő:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) olyan Storage-fiókra hivatkozik, amely nincs telepítve ebben a sablonban. A Storage-fiók már létezik ugyanazon az előfizetésen belül.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Egy olyan objektumot ad vissza, amely az aktuális erőforráscsoportot jelképezi.

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektum formátuma a következő:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A **többé** tulajdonság csak olyan erőforráscsoportok esetében lesz visszaadva, amelyek egy másik szolgáltatás által felügyelt erőforrásokat tartalmaznak. A felügyelt alkalmazások, a Databricks és az AK esetében a tulajdonság értéke a kezelő erőforrás erőforrás-azonosítója.

### <a name="remarks"></a>Megjegyzések

A `resourceGroup()` függvény nem használható [az előfizetés szintjén üzembe helyezett](deploy-to-subscription.md)sablonban. Csak az erőforráscsoporthoz központilag telepített sablonokban használható. Használhatja a `resourceGroup()` függvényt egy olyan [csatolt vagy beágyazott sablonban (belső hatókörrel)](linked-templates.md) , amely egy erőforráscsoportot céloz meg, még akkor is, ha a fölérendelt sablon telepítve van az előfizetésre. Ebben az esetben a csatolt vagy beágyazott sablon az erőforráscsoport szintjén lesz telepítve. Az erőforráscsoportok előfizetési szintű üzembe helyezésével kapcsolatos további információkért lásd: [Azure-erőforrások telepítése több előfizetéshez vagy erőforráscsoporthoz](./deploy-to-resource-group.md).

A resourceGroup függvény gyakori használata az, hogy az erőforrásokat ugyanabban a helyen hozza létre, mint az erőforráscsoportot. Az alábbi példa az erőforráscsoport helyét használja az alapértelmezett paraméterérték értékének megadásához.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

A resourceGroup függvény használatával címkéket is alkalmazhat az erőforráscsoporthoz egy erőforráshoz. További információ: [címkék alkalmazása az erőforráscsoporthoz](../management/tag-resources.md#apply-tags-from-resource-group).

Ha beágyazott sablonokat használ több erőforráscsoporthoz való üzembe helyezéshez, megadhatja a hatókört a resourceGroup függvény kiértékeléséhez. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoport-re](./deploy-to-resource-group.md).

### <a name="resource-group-example"></a>Erőforráscsoport – példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) az erőforráscsoport tulajdonságait adja vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

Az előző példa egy objektumot ad vissza a következő formátumban:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Egy erőforrás egyedi azonosítóját adja vissza. Ezt a függvényt akkor használja, ha az erőforrás neve nem egyértelmű, vagy nem lett kiépítve ugyanazon a sablonon belül. A visszaadott azonosító formátuma attól függően változik, hogy a központi telepítés egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő hatókörébe esik-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |karakterlánc (GUID formátumban) |Az alapértelmezett érték az aktuális előfizetés. Akkor adja meg ezt az értéket, ha egy másik előfizetésben le kell kérnie egy erőforrást. Csak akkor adja meg ezt az értéket, ha egy erőforráscsoport vagy előfizetés hatókörére telepíti. |
| resourceGroupName |No |sztring |Az alapértelmezett érték az aktuális erőforráscsoport. Akkor adja meg ezt az értéket, ha egy másik erőforráscsoport erőforrását le kell kérnie. Csak akkor adja meg ezt az értéket, ha egy erőforráscsoport hatókörére telepíti. |
| resourceType |Yes |sztring |Az erőforrás típusa, beleértve az erőforrás-szolgáltatói névteret. |
| resourceName1 |Yes |sztring |Az erőforrás neve. |
| resourceName2 |No |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Visszatérési érték

Ha a sablont egy erőforráscsoport hatókörébe telepíti, a rendszer az erőforrás-azonosítót a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

A resourceId függvényt más központi telepítési hatókörökhöz is használhatja, de az azonosító formátuma megváltozik.

Ha resourceId használ az előfizetés telepítésekor, az erőforrás-azonosítót a következő formátumban adja vissza a rendszer:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Ha a resourceId a felügyeleti csoportba vagy bérlőbe való központi telepítés során használja, az erőforrás-azonosítót a következő formátumban adja vissza a rendszer:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

A félreértések elkerülése érdekében javasoljuk, hogy ne használja a resourceId-t, amikor az előfizetésre, a felügyeleti csoportra vagy a bérlőre telepített erőforrásokkal dolgozik. Ehelyett használja a hatókörhöz tervezett ID függvényt.

Az [előfizetési szintű erőforrások](deploy-to-subscription.md)esetében használja a [subscriptionResourceId](#subscriptionresourceid) függvényt.

[Felügyeleti csoport szintű erőforrások](deploy-to-management-group.md)esetén használja a [extensionResourceId](#extensionresourceid) függvényt egy felügyeleti csoport kiterjesztéseként megvalósított erőforrásra való hivatkozáshoz. Például a felügyeleti csoportba telepített egyéni házirend-definíciók a felügyeleti csoport bővítményei. A [tenantResourceId](#tenantresourceid) függvénnyel hivatkozhat a bérlőre központilag telepített és a felügyeleti csoportban elérhető erőforrásokra. Például a beépített szabályzat-definíciók a bérlői szintű erőforrásokként vannak implementálva.

A [bérlői szintű erőforrások](deploy-to-tenant.md)esetében használja a [tenantResourceId](#tenantresourceid) függvényt. A tenantResourceId használata beépített szabályzat-definíciók esetében, mivel azok a bérlői szinten vannak implementálva.

### <a name="remarks"></a>Megjegyzések

Az Ön által megadott paraméterek száma attól függően változik, hogy az erőforrás szülő-vagy gyermek erőforrás-e, és hogy az erőforrás ugyanabban az előfizetésben vagy erőforráscsoporthoz van-e.

Az azonos előfizetésben és erőforráscsoporthoz tartozó szülő erőforráshoz tartozó erőforrás-azonosító lekéréséhez adja meg az erőforrás típusát és nevét.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Egy alárendelt erőforrás erőforrás-AZONOSÍTÓjának lekéréséhez figyeljen az erőforrástípus szegmensek számára. Adja meg az erőforrástípus minden szegmensének erőforrás-nevét. A szegmens neve a hierarchia adott részéhez tartozó erőforrásnak felel meg.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Ha egy erőforrás erőforrás-AZONOSÍTÓját szeretné lekérni ugyanabba az előfizetésbe, de más erőforráscsoporthoz, adja meg az erőforráscsoport nevét.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Egy másik előfizetéshez és erőforráscsoporthoz tartozó erőforrás-azonosító lekéréséhez adja meg az előfizetés AZONOSÍTÓját és az erőforráscsoport nevét.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Gyakran ezt a függvényt kell használnia, ha egy másik erőforráscsoport Storage-fiókját vagy virtuális hálózatát használja. Az alábbi példa azt szemlélteti, hogyan használhatók az erőforrások egy külső erőforráscsoport használatával:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>Példa erőforrás-AZONOSÍTÓra

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) egy Storage-fiók erőforrás-azonosítóját adja vissza az erőforráscsoport számára:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| sameRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Sztring | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>előfizetést

`subscription()`

Az aktuális üzemelő példányra vonatkozó előfizetés részleteit adja vissza.

### <a name="return-value"></a>Visszatérési érték

A függvény a következő formátumot adja vissza:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Megjegyzések

Ha beágyazott sablonokat használ több előfizetésben való üzembe helyezéshez, megadhatja az előfizetési funkció értékelésének hatókörét. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoport-re](./deploy-to-resource-group.md).

### <a name="subscription-example"></a>Előfizetés – példa

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) a kimenetek szakaszban megnevezett előfizetési függvény látható.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Az előfizetési szinten üzembe helyezett erőforrás egyedi azonosítóját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |karakterlánc (GUID formátumban) |Az alapértelmezett érték az aktuális előfizetés. Akkor adja meg ezt az értéket, ha egy másik előfizetésben le kell kérnie egy erőforrást. |
| resourceType |Yes |sztring |Az erőforrás típusa, beleértve az erőforrás-szolgáltatói névteret. |
| resourceName1 |Yes |sztring |Az erőforrás neve. |
| resourceName2 |No |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Visszatérési érték

Az azonosító a következő formátumban lesz visszaadva:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekérheti az erőforrás-AZONOSÍTÓt az [előfizetésre az erőforráscsoport helyett központilag telepített](deploy-to-subscription.md) erőforrásokhoz. A visszaadott azonosító eltér a [resourceId](#resourceid) függvény által visszaadott értéktől, mert nem tartalmazza az erőforráscsoport értékét.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID példa

A következő sablon egy beépített szerepkört rendel hozzá. Egy erőforráscsoport vagy előfizetés számára is telepíthető. A subscriptionResourceId függvény használatával beolvassa a beépített szerepkörök erőforrás-AZONOSÍTÓját.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

A bérlői szinten üzembe helyezett erőforrás egyedi azonosítóját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| resourceType |Yes |sztring |Az erőforrás típusa, beleértve az erőforrás-szolgáltatói névteret. |
| resourceName1 |Yes |sztring |Az erőforrás neve. |
| resourceName2 |No |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Visszatérési érték

Az azonosító a következő formátumban lesz visszaadva:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekérheti a bérlőhöz központilag telepített erőforrások erőforrás-AZONOSÍTÓját. A visszaadott azonosító eltér a más erőforrás-azonosító függvények által visszaadott értékektől, ha nem tartalmazza az erőforráscsoport vagy az előfizetés értékét.

### <a name="tenantresourceid-example"></a>tenantResourceId példa

A beépített szabályzat-definíciók a bérlői szintű erőforrások. A beépített szabályzat-definícióra hivatkozó szabályzat-hozzárendelés telepítéséhez használja a tenantResourceId függvényt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
* Több sablon egyesítéséhez tekintse meg a [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](linked-templates.md)című témakört.
* Egy adott típusú erőforrás létrehozásakor a megadott számú alkalommal megismételheti az [erőforrás-iteráció az ARM-sablonokban](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md).
