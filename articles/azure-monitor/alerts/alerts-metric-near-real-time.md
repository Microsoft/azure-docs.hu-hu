---
title: A metrikus riasztások által támogatott erőforrások Azure Monitor
description: A metrikus riasztások támogatási mérőszámait és naplóit ismertető Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614638"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>A metrikus riasztások által támogatott erőforrások Azure Monitor

A Azure Monitor mostantól támogatja az [új metrikai riasztás típusát](../platform/alerts-overview.md) , amely jelentős előnnyel jár a régebbi [klasszikus metrikai riasztásokkal](./alerts-classic.overview.md)szemben. A metrikák az [Azure-szolgáltatások nagyméretű listájához](../platform/metrics-supported.md)érhetők el. Az újabb riasztások támogatják az erőforrástípusok (növekvő) részhalmazát. Ez a cikk felsorolja az adott részhalmazt.

Újabb metrikai riasztásokat is használhat a Log Analytics munkaterületen tárolt népszerű naplófájlokban, amelyeket metrikaként Kinyer. További információkért tekintse meg [a naplók metrikai riasztásait](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatás
Jelenleg csak a Azure Portal, [REST API](/rest/api/monitor/metricalerts/)vagy [Resource Manager-sablonokban](./alerts-metric-create-templates.md)hozhatók létre újabb metrikai riasztások. Az újabb riasztások a PowerShell és az Azure CLI 2,0-es és újabb verzióival való konfigurálásának támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>A metrikák és a méretek támogatottak
Az újabb metrikai riasztások támogatják a dimenziókat használó mérőszámok riasztásait. Méretek használatával szűrheti a metrikát a megfelelő szintre. Az összes támogatott mérőszám és a vonatkozó dimenziók megtekinthetők és megjeleníthetők [Azure monitor-Metrikaböngészőból](../essentials/metrics-charts.md).

Az újabb riasztások által támogatott Azure Monitor metrikus források teljes listája:

|Erőforrás típusa  |Támogatott méretek |Több erőforrásból álló riasztások| Elérhető metrikák|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Igen | Nem | |
|Microsoft.ApiManagement/service | Igen | Nem | [API Management](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Igen | Nem | [Alkalmazás konfigurációja](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Spring | Igen | Nem | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Igen| Nem | [Automation-fiókok](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Nem | Nem | [Azure VMware Solution](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.BatCH/batchAccounts | Igen | Nem | [Batch-fiókok](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Igen | Yes | [Azure Cache for Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Nem | Nem | [Klasszikus Cloud Services](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nem | Nem | [Klasszikus Virtual Machines](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Igen | Nem | [Storage-fiókok (klasszikus)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Igen | Nem | [Storage-fiókok (klasszikus) – Blobok](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Igen | Nem | [Storage-fiókok (klasszikus) – fájlok](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Igen | Nem | [Storage-fiókok (klasszikus) – várólisták](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Igen | Nem | [Storage-fiókok (klasszikus) – táblák](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/fiókok | Igen | Nem | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Igen<sup>1</sup> | [Virtual Machines](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Igen | Nem |[Virtuálisgép-méretezési csoportok](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Igen| Nem | [Tárolócsoportok](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/nyilvántartók | Nem | Nem | [Tároló-nyilvántartók](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. Tárolószolgáltatás/managedClusters | Igen | Nem | [Felügyelt fürtök](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Igen | Yes | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Igen| Nem | [Az adatgyárak v1-es verziója](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/gyárak |Igen | Nem | [Az adatgyár v2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/fiókok | Igen | Nem | [Adatmegosztások](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/kiszolgálók | Nem | Nem | [MariaDB-adatbázis](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/kiszolgálók | Nem | Nem |[A MySQL-hez készült adatbázis](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/kiszolgálók | Nem | Nem | [PostgreSQL-hez készült adatbázis](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nem | Nem | [A PostgreSQL v2 ADATBÁZISa](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Igen | Nem | [PostgreSQL-hez készült adatbázis (rugalmas kiszolgálók)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Igen | Nem |[IoT Hub](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Igen | Nem | [Eszközök kiépítési szolgáltatásai](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Igen | Nem | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Igen | Nem | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/tartományok | Igen | Nem | [Event Grid-tartományok](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Igen | Nem | [Event Grid rendszertémakörök](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témakörök |Igen | Nem | [Event Grid-témakörök](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/fürtök |Igen| Nem | [Fürtök Event Hubs](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/névterek |Igen| Nem | [Event Hubs](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/fürtök | Igen | Nem | [HDInsight-fürtök](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. bepillantások/összetevők | Igen | Nem | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. kulcstartó/tárolók | Igen |Yes |[Kulcstartók](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/fürtök | Igen |Nem |[Fürtök Adatkezelő](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Igen | Nem |[Integrációs szolgáltatási környezetek](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/munkafolyamatok | Nem | Nem |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/munkaterületek | Igen | Nem | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/fiókok | Igen | Nem | [Maps-fiókok](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Mediaservices | Nem | Nem | [Media Services](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Mediaservices/streamingEndpoints | Igen | Nem | [Media Services streaming-végpontok](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Igen | Yes | [Azure NetApp-kapacitási készletek](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | Igen | Yes | [Azure NetApp-kötetek](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Igen | Nem | [Application Gateway átjárók](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Igen | Nem | [Tűzfalak](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nem | Nem | [DNS-zónák](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Igen | Nem |[ExpressRoute-áramkörök](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Igen | Nem |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (csak standard SKU-hoz)| Igen| Nem | [Terheléselosztó](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nem | Nem | [NAT-átjárók](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nem | Nem | [Privát végpontok](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nem | Nem | [Private link Services](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/nyilvános IP | Nem | Nem | [Nyilvános IP-címek](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Igen | Nem | [Traffic Manager-profilok](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/munkaterületek| Igen | Nem | [Log Analytics-munkaterületek](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/társaik | Igen | Nem | [Társviszonyok](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Igen | Nem | [Társviszony-létesítési szolgáltatások](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitások | Nem | Nem | [Kapacitások](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/névterek | Igen | Nem | [Továbbítók](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nem | Nem | [Keresési szolgáltatások](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/névterek | Igen | Nem | [Szolgáltatásbusz](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nem | Igen | [Felügyelt SQL-példányok](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/kiszolgálók/adatbázisok | Nem | Igen | [SQL Database-adatbázisok](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/kiszolgálók/elasticPools | Nem | Igen | [Rugalmas SQL-készletek](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Igen | Nem | [Storage-fiókok](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Igen| Nem | [Storage-fiókok – Blobok](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Igen| Nem | [Storage-fiókok – fájlok](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Igen| Nem | [Storage-fiókok – várólisták](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Igen| Nem | [Storage-fiókok – táblák](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/gyorsítótárak | Igen | Nem | [HPC-gyorsítótárak](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Igen | Nem | [Storage Sync Services](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Igen | Nem | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. szinapszis/munkaterületek | Igen | Nem | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. szinapszis/munkaterületek/bigDataPools | Igen | Nem | [Szinapszis Analitika Apache Spark készletek](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. szinapszis/munkaterületek/sqlPools | Igen | Nem | [Szinapszis Analytics SQL-készletek](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Igen | Nem | [CloudSimple-virtuálisgépek](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Igen | Nem | [Több szerepkört használó készletek App Service Environment](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Igen | Nem | [Munkavégző készletek App Service Environment](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/kiszolgálófarmok | Igen | Nem | [App Service csomagok](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Igen | Nem | [App Services és függvények](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/Slots | Igen | Nem | [App Service tárolóhelyek](../platform/metrics-supported.md#microsoftwebsitesslots)|

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

* További információ az új [riasztási élményről](../platform/alerts-overview.md).
* További információ a [log-riasztásokról az Azure-ban](./alerts-unified-log.md).
* További információ [Az Azure-beli riasztásokról](../platform/alerts-overview.md).