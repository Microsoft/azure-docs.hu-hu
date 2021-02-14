---
title: A metrikus riasztások által támogatott erőforrások Azure Monitor
description: A metrikus riasztások támogatási mérőszámait és naplóit ismertető Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: ee281e60a0eb6f6192e63a9733146714e4aaf2eb
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104382"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>A metrikus riasztások által támogatott erőforrások Azure Monitor

A Azure Monitor mostantól támogatja az [új metrikai riasztás típusát](./alerts-overview.md) , amely jelentős előnnyel jár a régebbi [klasszikus metrikai riasztásokkal](./alerts-classic.overview.md)szemben. A metrikák az [Azure-szolgáltatások nagyméretű listájához](./metrics-supported.md)érhetők el. Az újabb riasztások támogatják az erőforrástípusok (növekvő) részhalmazát. Ez a cikk felsorolja az adott részhalmazt.

Újabb metrikai riasztásokat is használhat a Log Analytics munkaterületen tárolt népszerű naplófájlokban, amelyeket metrikaként Kinyer. További információkért tekintse meg [a naplók metrikai riasztásait](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatás
Jelenleg csak a Azure Portal, [REST API](/rest/api/monitor/metricalerts/)vagy [Resource Manager-sablonokban](./alerts-metric-create-templates.md)hozhatók létre újabb metrikai riasztások. Az újabb riasztások a PowerShell és az Azure CLI 2,0-es és újabb verzióival való konfigurálásának támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>A metrikák és a méretek támogatottak
Az újabb metrikai riasztások támogatják a dimenziókat használó mérőszámok riasztásait. Méretek használatával szűrheti a metrikát a megfelelő szintre. Az összes támogatott mérőszám és a vonatkozó dimenziók megtekinthetők és megjeleníthetők [Azure monitor-Metrikaböngészőból](./metrics-charts.md).

Az újabb riasztások által támogatott Azure Monitor metrikus források teljes listája:

|Erőforrás típusa  |Támogatott méretek |Több erőforrásból álló riasztások| Elérhető metrikák|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Igen | Nem | |
|Microsoft.ApiManagement/service | Igen | Nem | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Igen | Nem | [Alkalmazás konfigurációja](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Spring | Igen | Nem | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Igen| Nem | [Automation-fiókok](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Nem | Nem | [Azure VMware Solution](./metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.BatCH/batchAccounts | Igen | Nem | [Batch-fiókok](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Igen | Igen | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Nem | Nem | [Klasszikus Cloud Services](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nem | Nem | [Klasszikus Virtual Machines](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Igen | Nem | [Storage-fiókok (klasszikus)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Igen | Nem | [Storage-fiókok (klasszikus) – Blobok](./metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Igen | Nem | [Storage-fiókok (klasszikus) – fájlok](./metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Igen | Nem | [Storage-fiókok (klasszikus) – várólisták](./metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Igen | Nem | [Storage-fiókok (klasszikus) – táblák](./metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/fiókok | Igen | Nem | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Igen | Igen<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Igen | Nem |[Virtuálisgép-méretezési csoportok](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Igen| Nem | [Tárolócsoportok](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/nyilvántartók | Nem | Nem | [Tároló-nyilvántartók](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. Tárolószolgáltatás/managedClusters | Igen | Nem | [Felügyelt fürtök](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Igen | Igen | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Igen| Nem | [Az adatgyárak v1-es verziója](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/gyárak |Igen | Nem | [Az adatgyár v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/fiókok | Igen | Nem | [Adatmegosztások](./metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/kiszolgálók | Nem | Nem | [MariaDB-adatbázis](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/kiszolgálók | Nem | Nem |[A MySQL-hez készült adatbázis](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/kiszolgálók | Nem | Nem | [PostgreSQL-hez készült adatbázis](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nem | Nem | [A PostgreSQL v2 ADATBÁZISa](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Igen | Nem | [PostgreSQL-hez készült adatbázis (rugalmas kiszolgálók)](./metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Igen | Nem |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Igen | Nem | [Eszközök kiépítési szolgáltatásai](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Igen | Nem | [Digital Twins](./metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Igen | Nem | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/tartományok | Igen | Nem | [Event Grid-tartományok](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Igen | Nem | [Event Grid rendszertémakörök](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témakörök |Igen | Nem | [Event Grid-témakörök](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/fürtök |Igen| Nem | [Fürtök Event Hubs](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/névterek |Igen| Nem | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/fürtök | Igen | Nem | [HDInsight-fürtök](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. bepillantások/összetevők | Igen | Nem | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. kulcstartó/tárolók | Igen |Igen |[Kulcstartók](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/fürtök | Igen |Nem |[Fürtök Adatkezelő](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Igen | Nem |[Integrációs szolgáltatási környezetek](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/munkafolyamatok | Nem | Nem |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/munkaterületek | Igen | Nem | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/fiókok | Igen | Nem | [Maps-fiókok](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Mediaservices | Nem | Nem | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Mediaservices/streamingEndpoints | Igen | Nem | [Media Services streaming-végpontok](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Igen | Igen | [Azure NetApp-kapacitási készletek](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | Igen | Igen | [Azure NetApp-kötetek](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Igen | Nem | [Application Gateway átjárók](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Igen | Nem | [Tűzfalak](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nem | Nem | [DNS-zónák](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Igen | Nem |[ExpressRoute-áramkörök](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Igen | Nem |[ExpressRoute Direct](./metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (csak standard SKU-hoz)| Igen| Nem | [Terheléselosztó](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nem | Nem | [NAT-átjárók](./metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nem | Nem | [Privát végpontok](./metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nem | Nem | [Private link Services](./metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/nyilvános IP | Nem | Nem | [Nyilvános IP-címek](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Igen | Nem | [Traffic Manager-profilok](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/munkaterületek| Igen | Nem | [Log Analytics-munkaterületek](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/társaik | Igen | Nem | [Társviszonyok](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Igen | Nem | [Társviszony-létesítési szolgáltatások](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitások | Nem | Nem | [Kapacitások](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/névterek | Igen | Nem | [Továbbítók](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nem | Nem | [Keresési szolgáltatások](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/névterek | Igen | Nem | [Szolgáltatásbusz](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nem | Igen | [Felügyelt SQL-példányok](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/kiszolgálók/adatbázisok | Nem | Igen | [SQL Database-adatbázisok](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/kiszolgálók/elasticPools | Nem | Igen | [Rugalmas SQL-készletek](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Igen | Nem | [Storage-fiókok](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Igen| Nem | [Storage-fiókok – Blobok](./metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Igen| Nem | [Storage-fiókok – fájlok](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Igen| Nem | [Storage-fiókok – várólisták](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Igen| Nem | [Storage-fiókok – táblák](./metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/gyorsítótárak | Igen | Nem | [HPC-gyorsítótárak](./metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Igen | Nem | [Storage Sync Services](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Igen | Nem | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. szinapszis/munkaterületek | Igen | Nem | [Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. szinapszis/munkaterületek/bigDataPools | Igen | Nem | [Szinapszis Analitika Apache Spark készletek](./metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. szinapszis/munkaterületek/sqlPools | Igen | Nem | [Szinapszis Analytics SQL-készletek](./metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Igen | Nem | [CloudSimple-virtuálisgépek](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Igen | Nem | [Több szerepkört használó készletek App Service Environment](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Igen | Nem | [Munkavégző készletek App Service Environment](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/kiszolgálófarmok | Igen | Nem | [App Service csomagok](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Igen | Nem | [App Services és függvények](./metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/Slots | Igen | Nem | [App Service tárolóhelyek](./metrics-supported.md#microsoftwebsitesslots)|

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
