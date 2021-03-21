---
title: Támogatott erőforrástípusok a Azure Resource Healthon keresztül | Microsoft Docs
description: Támogatott erőforrástípusok az Azure Resource Health használatával
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 1b0958732a96786b2fb86b2b145f5cb8d36c0c5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97883367"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Erőforrástípusok és állapot-ellenőrzések az Azure Resource Health-ben
Az alábbi lista az erőforrás-állapoton keresztül végrehajtott összes ellenőrzés teljes listáját tartalmazza.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló működik és fut?</li><li>Elfogyott a kiszolgáló memóriája?</li><li>Elindul a kiszolgáló?</li><li>A kiszolgáló helyreállítása?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik az API Management szolgáltatás?</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhető az Azure Spring Cloud-példány?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a Batch-fiókja?</li><li>Túllépte a készlet kvótáját ehhez a Batch-fiókhoz?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes gyorsítótár-csomópont fel van-e telepítve és fut?</li><li>Elérheti a gyorsítótárat az adatközponton belülről?</li><li>Elérte a gyorsítótárban a kapcsolatok maximális számát?</li><li> A gyorsítótár kimerítette a rendelkezésre álló memóriát? </li><li>A gyorsítótár nagy számú laphibát tapasztal?</li><li>A gyorsítótár nagy terhelés alatt van?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft. CDN/profil
|Végrehajtott ellenőrzések|
|---|
|<ul> <li>Elérhető-e a kiegészítő portál a CDN konfigurációs műveleteihez?</li><li>Folyamatban van a CDN-végpontokkal kapcsolatos kézbesítési probléma?</li><li>Módosíthatják a felhasználók a CDN-erőforrásaik konfigurációját?</li><li>A konfigurációs változások a várt sebességgel lesznek propagálva?</li><li>A felhasználók kezelhetik a CDN-konfigurációt a Azure Portal, a PowerShell vagy az API használatával?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. classiccompute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Befejeződött a gazda operációs rendszer indítása?</li><li>A virtuális gép tárolója kiépítve és felépítve van?</li><li>Van hálózati kapcsolat a gazdagép és a Storage-fiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Folyamatban van a tervezett karbantartás?</li><li>A gazdagép hardvere csökkent, és várhatóan sikertelen lesz?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft. classiccompute/tartománynév
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A üzemi tárolóhelyek állapota Kifogástalan az összes szerepkör-példányon?</li><li>Kifogástalan a szerepkör az összes virtuálisgép-példánya között?</li><li>Mi az egyes virtuális gépek állapota egy felhőalapú szolgáltatás szerepkörén belül?</li><li>A virtuális gép állapota a platform vagy az ügyfél által kezdeményezett művelet miatt módosult?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Folyamatban van a tervezett karbantartás?</li><li>A gazdagép hardvere csökkent, és várhatóan sikertelen lesz?</li><li>[További](../cloud-services/resource-health-for-cloud-services.md) információ a végrehajtott ellenőrzésekről</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. cognitiveservices/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérheti a fiókot az adatközponton belülről?</li><li>Elérhető a Cognitive Services erőforrás-szolgáltató?</li><li>Elérhető-e a kognitív szolgáltatás a megfelelő régióban?</li><li>Elvégezhető az olvasási műveletek végrehajtása az erőforrás-metaadatokat tároló Storage-fiókon?</li><li>Elérte az API-hívási kvótát?</li><li>Elérte az API-hívás olvasási korlátját?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft. számítási/hostgroups/gazdagépek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép?</li><li>A gazdagép hardvere csökkent?</li><li>A gazdagép fel van foglalva?</li><li>A gazdagép hardveres szolgáltatása meggyógyult a különböző hardvereken?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft. számítás/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A virtuális gépet üzemeltető kiszolgáló működik?</li><li>Befejeződött a gazda operációs rendszer indítása?</li><li>A virtuális gép tárolója kiépítve és felépítve van?</li><li>Van hálózati kapcsolat a gazdagép és a Storage-fiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Folyamatban van a tervezett karbantartás?</li><li>A gazdagép hardvere csökkent, és várhatóan sikertelen lesz?</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a fürt?</li><li>Elérhetők-e az alapvető szolgáltatások a fürtön?</li><li>Készen állnak az összes fürtcsomópont?</li><li>Az egyszerű szolgáltatásnév és érvényes?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Történtek a folyamat futási hibái?</li><li>A fürt a Data Factory kifogástalan állapotú?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. datalakeanalytics/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Tapasztaltak-e a felhasználók a Data Lake Analytics feladatok elküldését vagy listázását?</li><li>A Data Lake Analytics feladatok a rendszerhibák miatt nem hajthatók végre?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Problémák léptek fel a felhasználók számára az adatfeltöltés Data Lake Store?</li><li>Problémák léptek fel a felhasználóktól a Data Lake Store adatok letöltésekor?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. datamigration/szolgáltatások
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Nem sikerült kiépíteni az adatbázis-áttelepítési szolgáltatást?</li><li>Az adatbázis-áttelepítési szolgáltatás inaktivitás vagy felhasználói kérelem miatt leállt?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az adatmegosztási fiók fel van-e telepítve és fut?</li><li>Elérhető-e az adatmegosztást tároló fürt?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/iothubs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A IoT hub működik?</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/DigitalTwinsInstances
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Azure digitális Twins-példánya működik?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Van-e olyan adatbázis-vagy gyűjtési kérelem, amely egy Azure Cosmos DB szolgáltatás nem rendelkezésre állása miatt nem szolgált?</li><li>Van-e olyan dokumentumra vonatkozó kérelem, amely egy Azure Cosmos DB szolgáltatás nem rendelkezésre állása miatt nem szolgált?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/névterek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Event Hubs névtér a felhasználó által generált hibákat észlelt?</li><li>Jelenleg folyamatban van a Event Hubs névtér frissítése?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. hdinsight/fürtök
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetők-e a Core Services a HDInsight-fürtön?</li><li>A HDInsight-fürt hozzáfér a kulcshoz a BYOK-titkosításhoz a nyugalmi állapotban?</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhető a IoT Central alkalmazás?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Azure kulcstartó-platformmal kapcsolatos problémák miatt nem sikerül a Key vaultra irányuló kérés?</li><li>A Key Vault felé irányuló kérések szabályozva lettek, mivel az ügyfél túl sok kérést igényel?</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A fürt alacsony betöltési sikerességi arányt tapasztal?</li><li>A fürt nagy mennyiségű betöltési késést tapasztal?</li><li>A fürt nagy számú lekérdezési hibát tapasztal?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. MachineLearning/webszolgáltatások
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Fut a webszolgáltatás?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik az adathordozó szolgáltatás?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Application Gateway teljesítménye romlik?</li><li>Elérhető a Application Gateway?</li></ul>|

## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionhosts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a megerősített gazdagép?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Csatlakozik a VPN-alagút?</li><li>Vannak konfigurációs ütközések a kapcsolatban?</li><li>Megfelelően vannak-e konfigurálva az előmegosztott kulcsok?</li><li>Elérhető a helyszíni VPN-eszköz?</li><li>Eltérnek az IPSec/IKE biztonsági házirend?</li><li>A S2S VPN-kapcsolat megfelelően van kiépítve vagy hibás állapotban van?</li><li>Megfelelően van-e kiépítve vagy hibás állapotban a VNET-VNET-kapcsolatok?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressroutecircuits
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Kifogástalan a ExpressRoute áramkör?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az állapotjelző háttérrendszer a hibákkal válaszol az állapot-mintavételi problémákra?</li><li>A konfigurációs változások késleltetve vannak?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetőek-e a terheléselosztási végpontok?</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficmanagerprofiles
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Vannak olyan problémák, amelyek hatással vannak a Traffic Manager profilra?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhető a VPN-átjáró az internetről?</li><li>A VPN Gateway készenléti állapotban van?</li><li>Fut a VPN-szolgáltatás az átjárón?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft. NotificationHubs/névtér
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Futtathatók-e olyan futásidejű műveletek, mint a regisztráció, a telepítés vagy a Küldés a névtérben?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/munkaterületek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Vannak-e késések a munkaterületen?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kapacitás-erőforrás felépítése és futtatása folyamatban van?</li><li>Működik az összes számítási feladat?</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Végezhető diagnosztikai műveletek a fürtön?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az ügyfelek a felhasználó által generált Service Bus hibákat észleltek?</li><li>A felhasználók a Service Bus névtér frissítése miatt átmeneti hibákat tapasztalnak?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft. ServiceFabric/fürtök
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a Service Fabric-fürt?</li><li>Kezelhető-e a Service Fabric-fürt a Azure Resource Manager használatával?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az adatbázis működik és fut?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Van-e bejelentkezés az adatbázisba?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Nem sikerül beolvasni az adatok beolvasását a Storage-fiókból az Azure Storage platformmal kapcsolatos problémák miatt?</li><li>Az Azure Storage platformmal kapcsolatos problémák miatt nem sikerül az adatbevitel a Storage-fiókba?</li><li>Az a tárolási fürt, ahol a Storage-fiók nem érhető el?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes gazdagép, ahol a feladatot végrehajtják és futtatják?</li><li>Nem sikerült elindítani a feladatot?</li><li>Folyamatban van a futtatókörnyezet frissítése?</li><li>A feladatoknak a várt állapotban kell lenniük (például az ügyfél által futtatott vagy leállított)?</li><li>Észlelte a feladatot a memórián kívüli kivételek?</li><li>Van-e folyamatban ütemezett számítási frissítés?</li><li>Elérhető a végrehajtás-kezelő (vezérlési terv)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Internet Information Services fut?</li><li>Fut a terheléselosztó?</li><li>Elérhető a App Service-csomag az adatközponton belülről?</li><li>Elérhető-e a kiszolgálófarm tároló fiók a helyek tartalmában?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft. Web/Sites
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Fut az Internet Information Server?</li><li>Fut a terheléselosztó?</li><li>Elérheti a webalkalmazást az adatközponton belülről?</li><li>Elérhető-e a webhely tartalmát üzemeltető Storage-fiók?</li></ul>|

## <a name="next-steps"></a>Következő lépések
-  További információ: [Bevezetés a Azure Service Health irányítópultra](service-health-overview.md) és [a Azure Resource Health bevezetésére](resource-health-overview.md) . 
-  [Gyakori kérdések a Azure Resource Health](resource-health-faq.md)
- Riasztások beállítása, hogy értesítést kapjon az állapottal kapcsolatos problémákról. További információ: [riasztások konfigurálása a szolgáltatás állapotára vonatkozó eseményekhez](./alerts-activity-log-service-notifications-portal.md).