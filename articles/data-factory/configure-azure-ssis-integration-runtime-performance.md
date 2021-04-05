---
title: A Azure-SSIS Integration Runtime teljesítményének konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Azure-SSIS Integration Runtime tulajdonságait a nagy teljesítmény érdekében
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 5d275100124660b901504b7e7f71cf93518fd077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364392"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>A Azure-SSIS Integration Runtime konfigurálása nagy teljesítményhez

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Ez a cikk azt ismerteti, hogyan konfigurálható a nagy teljesítményű Azure-SSIS Integration Runtime (IR). A Azure-SSIS IR lehetővé teszi SQL Server Integration Services-(SSIS-) csomagok üzembe helyezését és futtatását az Azure-ban. További információ a Azure-SSIS IRról: [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) cikk. További információ a SSIS-csomagok Azure-beli üzembe helyezéséről és futtatásáról: [SQL Server Integration Services számítási feladatok felhőbe való átemelése és átirányítása](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ez a cikk a SSIS fejlesztői csapatának tagjai által végzett házon belüli tesztelések teljesítménybeli eredményeit és megfigyeléseit tartalmazza. Az eredmények eltérőek lehetnek. A konfigurációs beállítások véglegesítése előtt végezze el a saját tesztelést, amely a költségeket és a teljesítményt is befolyásolja.

## <a name="properties-to-configure"></a>Konfigurálandó tulajdonságok

A konfigurációs parancsfájl következő része a Azure-SSIS Integration Runtime létrehozásakor konfigurálható tulajdonságokat jeleníti meg. A PowerShell-parancsfájl és a Leírás teljes leírása: [SQL Server Integration Services csomagok üzembe helyezése az Azure](tutorial-deploy-ssis-packages-azure-powershell.md)-ban.

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
A **AzureSSISLocation** az Integration Runtime Worker csomópontjának helye. A feldolgozó csomópont állandó kapcsolatot tart fenn a Azure SQL Database SSIS Catalog adatbázisával (SSISDB). Állítsa a **AzureSSISLocation** a SSISDB-t futtató [logikai SQL Server-kiszolgálóval](../azure-sql/database/logical-servers.md) megegyező helyre, amely lehetővé teszi, hogy az integrációs modul a lehető leghatékonyabban működjön.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, beleértve a Azure-SSIS IR, a következő lehetőségeket támogatja:
-   Standard \_ a4 \_ v2
-   Standard \_ a8 \_ v2
-   Standard \_ D1 \_ v2
-   Standard \_ D2 \_ v2
-   Standard \_ D3 \_ v2
-   Standard \_ D4 \_ v2
-   Standard \_ D2 \_ v3
-   Standard \_ D4 \_ v3
-   Standard \_ D8 \_ v3
-   Standard \_ D16 \_ v3
-   Standard \_ D32 \_ v3
-   Standard \_ D64 \_ v3
-   Standard \_ E2 \_ v3
-   Szabványos \_ E4 \_ v3
-   Standard \_ E8 \_ v3
-   Standard \_ E16 \_ v3
-   Standard \_ E32 \_ v3
-   Standard \_ E64 \_ v3

A SSIS mérnöki csapatának nem hivatalos házon belüli tesztelésében a D sorozat úgy tűnik, hogy jobban megfelel a csomagok végrehajtásának SSIS, mint az a sorozat.

-   A D sorozat teljesítmény/ár aránya nagyobb, mint az a sorozat, és a v3 sorozat teljesítmény/ár aránya nagyobb, mint a v2 sorozat.
-   A D sorozathoz tartozó átviteli sebesség magasabb, mint a sorozat ugyanazon az áron, és a v3-as sorozat átviteli sebessége magasabb, mint a v2 sorozat ugyanazon az áron.
-   A Azure-SSIS IR v2 sorozatú csomópontjai nem alkalmasak az egyéni telepítéshez, ezért használja helyette a v3 sorozatú csomópontokat. Ha már használja a v2 sorozatú csomópontokat, a lehető leghamarabb váltson a v3 sorozatú csomópontok használatára.
-   Az E sorozat a memóriára optimalizált virtuálisgép-méretek, amelyek nagyobb memória-CPU arányt biztosítanak, mint a többi gép. Ha a csomag nagy mennyiségű memóriát igényel, érdemes lehet az E sorozatú virtuális gépet választania.

### <a name="configure-for-execution-speed"></a>Konfigurálás a végrehajtási sebességhez
Ha nem rendelkezik sok csomag futtatásával, és azt szeretné, hogy a csomagok gyorsan fussanak, az alábbi táblázatban szereplő információk alapján válasszon egy, a forgatókönyvnek megfelelő virtuálisgép-típust.

Ez az adat egyetlen feldolgozó csomóponton egyetlen csomag-végrehajtást jelöl. A csomag 3 000 000 rekordot tölt be az Azure Blob Storage-ból utónév és vezetéknév oszlopokkal, és teljes nevet hoz létre, és a teljes névvel ellátott rekordokat írja a 20 karakternél hosszabb ideig az Azure Blob Storageba.

Az y tengely azon csomagok száma, amelyek egy órán belül befejezték a végrehajtást. Vegye figyelembe, hogy ez csak egy memóriahasználat típusú csomag tesztelési eredménye. Ha tudni szeretné a csomag átviteli sebességét, azt javasoljuk, hogy saját maga végezze el a tesztet.

![SSIS Integration Runtime csomag végrehajtásának sebessége](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurálás a teljes átviteli sebességhez

Ha sok csomagot kell futtatnia, és Ön a teljes átviteli sebességről gondoskodik, akkor az alábbi táblázatban szereplő információk alapján válasszon egy, a forgatókönyvnek megfelelő virtuálisgép-típust.

Az y tengely azon csomagok száma, amelyek egy órán belül befejezték a végrehajtást. Vegye figyelembe, hogy ez csak egy memóriahasználat típusú csomag tesztelési eredménye. Ha tudni szeretné a csomag átviteli sebességét, azt javasoljuk, hogy saját maga végezze el a tesztet.

![SSIS Integration Runtime maximális átviteli sebesség](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

A **AzureSSISNodeNumber** az integrációs modul méretezhetőségét állítja be. Az Integration Runtime átviteli sebessége arányos a **AzureSSISNodeNumber**. Először állítsa be a **AzureSSISNodeNumber** egy kisebb értékre, figyelje az integrációs modul átviteli sebességét, majd állítsa be a forgatókönyv értékét. A munkavégző csomópontok számának újrakonfigurálásával kapcsolatban lásd: [Az Azure SSIS Integration Runtime kezelése](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Ha már egy hatékony munkavégző csomópontot használ a csomagok futtatásához, a **AzureSSISMaxParallelExecutionsPerNode** növelése növelheti az integrációs modul teljes átviteli sebességét. Ha a maximális értéket szeretné megnövelni, a **AzureSSISMaxParallelExecutionsPerNode** frissítéséhez Azure PowerShellt kell használnia. A megfelelő értéket a csomag költsége és a munkavégző csomópontok következő beállításai alapján becsülheti fel. További információ: [általános célú virtuális gépek méretei](../virtual-machines/sizes-general.md).

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard \_ D1 \_ v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard \_ D2 \_ v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard \_ D3 \_ v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard \_ D4 \_ v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard \_ a4 \_ v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard \_ a8 \_ v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Standard \_ D2 \_ v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Standard \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standard \_ D8 \_ v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard \_ D16 \_ v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| Standard \_ D32 \_ v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard \_ D64 \_ v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standard \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Szabványos \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standard \_ E8 \_ v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard \_ E16 \_ v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standard \_ E32 \_ v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard \_ E64 \_ v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

A **AzureSSISMaxParallelExecutionsPerNode** tulajdonság helyes értékének beállításához a következő irányelvek vonatkoznak: 

1. Először állítsa be egy kis értékre.
2. Növelje kis mennyiséggel, és győződjön meg arról, hogy a teljes átviteli sebesség javult-e.
3. Állítsa le az érték növelését, ha a teljes átviteli sebesség eléri a maximális értéket.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

A **SSISDBPricingTier** a SSIS-katalógus adatbázisának (SSISDB) díjszabási szintje Azure SQL Databaseban. Ez a beállítás befolyásolja az IR-példányon dolgozó munkavégzők maximális számát, a csomagok végrehajtásának sebességét, valamint a végrehajtási napló betöltésének sebességét.

-   Ha nem érdekli a csomagok végrehajtásának és a végrehajtási napló betöltésének gyorsasága, akkor kiválaszthatja a legalacsonyabb adatbázis-díjszabási szintet. Az alapszintű díjszabású Azure SQL Database 8 feldolgozót támogat egy Integration Runtime-példányban.

-   Ha a feldolgozók száma nagyobb, mint 8, vagy a mag száma meghaladja a 50-et, válasszon egy nagyobb teljesítményű adatbázist. Ellenkező esetben az adatbázis az Integration Runtime-példány szűk keresztmetszetét jelenti, és a teljes teljesítmény negatív hatással van.

-   Ha a naplózási szint részletes értékre van állítva, válasszon egy nagyobb teljesítményű adatbázist (például S3). A nem hivatalos házon belüli tesztelésnek megfelelően az S3 árképzési szint 2 csomóponttal, 128 párhuzamos számokkal és részletes naplózási szinttel támogatja a SSIS-csomagok végrehajtását.

Az adatbázis díjszabási szintjét a Azure Portal elérhető adatbázis- [tranzakciós egység](../azure-sql/database/service-tiers-dtu.md) (DTU) használati információk alapján is módosíthatja.

## <a name="design-for-high-performance"></a>Nagy teljesítményű rendszer tervezése
Az Azure-on futó SSIS-csomagok tervezése eltér a helyszíni végrehajtáshoz szükséges csomagok tervezésének. Az egyazon csomagban található több független feladat egyesítése helyett a Azure-SSIS IR hatékonyabb végrehajtásához válassza el őket több csomagra. Hozzon létre egy csomag-végrehajtást minden csomaghoz, így nem kell megvárniuk egymás befejezését. Ez a megközelítés az Azure-SSIS integrációs modul skálázhatóságának előnyeit és a teljes átviteli sebességet javítja.

## <a name="next-steps"></a>Következő lépések
További információ a Azure-SSIS Integration Runtimeról. Lásd: [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).