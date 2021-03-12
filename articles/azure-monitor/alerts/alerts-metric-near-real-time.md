---
title: A metrikus riasztások által támogatott erőforrások Azure Monitor
description: A metrikus riasztások támogatási mérőszámait és naplóit ismertető Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: efc1438fdc539af278ebff1f292c5fa0a91b7b91
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016100"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>A metrikus riasztások által támogatott erőforrások Azure Monitor

A Azure Monitor mostantól támogatja az [új metrikai riasztás típusát](./alerts-overview.md) , amely jelentős előnnyel jár a régebbi [klasszikus metrikai riasztásokkal](./alerts-classic.overview.md)szemben. A metrikák az [Azure-szolgáltatások nagyméretű listájához](../essentials/metrics-supported.md)érhetők el. Az újabb riasztások támogatják az erőforrástípusok (növekvő) részhalmazát. Ez a cikk felsorolja az adott részhalmazt.

Újabb metrikai riasztásokat is használhat a Log Analytics munkaterületen tárolt népszerű naplófájlokban, amelyeket metrikaként Kinyer. További információkért tekintse meg [a naplók metrikai riasztásait](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatás
Jelenleg csak a Azure Portal, [REST API](/rest/api/monitor/metricalerts/)vagy [Resource Manager-sablonokban](./alerts-metric-create-templates.md)hozhatók létre újabb metrikai riasztások. Az újabb riasztások a PowerShell és az Azure CLI 2,0-es és újabb verzióival való konfigurálásának támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>A metrikák és a méretek támogatottak
Az újabb metrikai riasztások támogatják a dimenziókat használó mérőszámok riasztásait. Méretek használatával szűrheti a metrikát a megfelelő szintre. Az összes támogatott mérőszám és a vonatkozó dimenziók megtekinthetők és megjeleníthetők [Azure monitor-Metrikaböngészőból](../essentials/metrics-charts.md).

Az újabb riasztások által támogatott Azure Monitor metrikus források teljes listája:

|Erőforrás típusa  |Támogatott méretek |Több erőforrásból álló riasztások| Elérhető metrikák|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Igen | Nem | |
|Microsoft.ApiManagement/service | Igen | Nem | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Igen | Nem | [Alkalmazás konfigurációja](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Spring | Igen | Nem | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Igen| Nem | [Automation-fiókok](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Nem | Nem | [Azure VMware Solution](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.BatCH/batchAccounts | Igen | Nem | [Batch-fiókok](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Igen | Nem | [Bot Services](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft. cache/Redis | Igen | Yes | [Azure Cache for Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Nem | Nem | [Klasszikus Cloud Services](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nem | Nem | [Klasszikus Virtual Machines](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Igen | Nem | [Storage-fiókok (klasszikus)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Igen | Nem | [Storage-fiókok (klasszikus) – Blobok](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Igen | Nem | [Storage-fiókok (klasszikus) – fájlok](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Igen | Nem | [Storage-fiókok (klasszikus) – várólisták](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Igen | Nem | [Storage-fiókok (klasszikus) – táblák](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/fiókok | Igen | Nem | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. számítás/cloudServices | Igen | Nem |  [Felhőszolgáltatások](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. számítási/cloudServices/szerepkörök | Igen | Nem |  [Cloud Service-szerepkörök](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Yes | Igen<sup>1</sup> | [Virtual Machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Igen | Nem |[Virtuálisgép-méretezési csoportok](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Igen| Nem | [Tárolócsoportok](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/nyilvántartók | Nem | Nem | [Tároló-nyilvántartók](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. Tárolószolgáltatás/managedClusters | Igen | Nem | [Felügyelt fürtök](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Igen | Yes | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Igen| Nem | [Az adatgyárak v1-es verziója](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/gyárak |Igen | Nem | [Az adatgyár v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/fiókok | Igen | Nem | [Adatmegosztások](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/kiszolgálók | Nem | Nem | [MariaDB-adatbázis](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/kiszolgálók | Nem | Nem |[A MySQL-hez készült adatbázis](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Igen | Nem | [PostgreSQL-hez készült adatbázis (rugalmas kiszolgálók)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Igen | Nem | PostgreSQL-adatbázis (nagy kapacitású) |
|Microsoft. DBforPostgreSQL/kiszolgálók | Nem | Nem | [PostgreSQL-hez készült adatbázis](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nem | Nem | [A PostgreSQL v2 ADATBÁZISa](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Devices/IotHubs | Igen | Nem |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Igen | Nem | [Eszközök kiépítési szolgáltatásai](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Igen | Nem | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Igen | Nem | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/tartományok | Igen | Nem | [Event Grid-tartományok](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Igen | Nem | [Event Grid rendszertémakörök](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témakörök |Igen | Nem | [Event Grid-témakörök](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/fürtök |Igen| Nem | [Fürtök Event Hubs](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/névterek |Igen| Nem | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/fürtök | Igen | Nem | [HDInsight-fürtök](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. bepillantások/összetevők | Igen | Nem | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. kulcstartó/tárolók | Igen |Yes |[Kulcstartók](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/fürtök | Igen |Nem |[Fürtök Adatkezelő](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Igen | Nem |[Integrációs szolgáltatási környezetek](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/munkafolyamatok | Nem | Nem |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/munkaterületek | Igen | Nem | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/munkaterületek/onlineEndpoints | Igen | Nem | Machine Learning – végpontok |
|Microsoft. MachineLearningServices/munkaterületek/onlineEndpoints/központi telepítések | Igen | Nem | Machine Learning – végponti központi telepítések |
|Microsoft. Maps/fiókok | Igen | Nem | [Maps-fiókok](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Mediaservices | Nem | Nem | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Mediaservices/streamingEndpoints | Igen | Nem | [Media Services streaming-végpontok](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Igen | Yes | [Azure NetApp-kapacitási készletek](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | Igen | Yes | [Azure NetApp-kötetek](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Igen | Nem | [Application Gateway átjárók](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Igen | Nem | [Tűzfalak](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nem | Nem | [DNS-zónák](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Igen | Nem |[ExpressRoute-áramkörök](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Igen | Nem |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (csak standard SKU-hoz)| Igen| Nem | [Terheléselosztó](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nem | Nem | [NAT-átjárók](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nem | Nem | [Privát végpontok](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nem | Nem | [Private link Services](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/nyilvános IP | Nem | Nem | [Nyilvános IP-címek](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Igen | Nem | [Traffic Manager-profilok](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/munkaterületek| Igen | Nem | [Log Analytics-munkaterületek](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/társaik | Igen | Nem | [Társviszonyok](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Igen | Nem | [Társviszony-létesítési szolgáltatások](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitások | Nem | Nem | [Kapacitások](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/névterek | Igen | Nem | [Továbbítók](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nem | Nem | [Keresési szolgáltatások](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/névterek | Igen | Nem | [Szolgáltatásbusz](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nem | Igen | [Felügyelt SQL-példányok](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/kiszolgálók/adatbázisok | Nem | Igen | [SQL Database-adatbázisok](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/kiszolgálók/elasticPools | Nem | Igen | [Rugalmas SQL-készletek](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Igen | Nem | [Storage-fiókok](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Igen| Nem | [Storage-fiókok – Blobok](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Igen| Nem | [Storage-fiókok – fájlok](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Igen| Nem | [Storage-fiókok – várólisták](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Igen| Nem | [Storage-fiókok – táblák](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/gyorsítótárak | Igen | Nem | [HPC-gyorsítótárak](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Igen | Nem | [Storage Sync Services](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Igen | Nem | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. szinapszis/munkaterületek | Igen | Nem | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. szinapszis/munkaterületek/bigDataPools | Igen | Nem | [Szinapszis Analitika Apache Spark készletek](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. szinapszis/munkaterületek/sqlPools | Igen | Nem | [Szinapszis Analytics SQL-készletek](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Igen | Nem | [CloudSimple-virtuálisgépek](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Igen | Nem | [Több szerepkört használó készletek App Service Environment](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Igen | Nem | [Munkavégző készletek App Service Environment](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/kiszolgálófarmok | Igen | Nem | [App Service csomagok](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Igen | Nem | [App Services és függvények](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/Slots | Igen | Nem | [App Service tárolóhelyek](../essentials/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> nem támogatott a virtuálisgép-hálózati metrikák esetében (összesen a hálózat, a hálózat teljes száma, a bejövő folyamatok, a kimenő folyamatok, a bejövő folyamatok maximális létrehozási sebessége, a kimenő forgalom maximális száma) és az egyéni metrikák.

## <a name="payload-schema"></a>Hasznos adatok sémája

> [!NOTE]
> Használhatja továbbá a [Common Alert sémát](./alerts-common-schema.md)is, amely lehetővé teszi, hogy a webhook-integrációk esetében egyetlen bővíthető és egységesített riasztási adattartalom legyen a Azure monitor összes riasztási szolgáltatásában. [Ismerje meg a riasztási séma általános definícióit.](./alerts-common-schema-definitions.md)


A POST művelet a következő JSON-adattartalomot és sémát tartalmazza az összes újabb metrikai riasztáshoz, ha egy megfelelő módon konfigurált [műveleti csoport](./action-groups.md) van használatban:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* További információ az új [riasztási élményről](./alerts-overview.md).
* További információ a [log-riasztásokról az Azure-ban](./alerts-unified-log.md).
* További információ [Az Azure-beli riasztásokról](./alerts-overview.md).
