---
title: Integrációs modul monitorozása Azure Data Factory
description: Megtudhatja, hogyan figyelheti Azure Data Factory Integration Runtime különböző típusait.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: a52fad39e19bdf2edf110990c8f0e392ec5803ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377499"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Integrációs modul monitorozása az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
Az **Integration Runtime** a Azure Data Factory (ADF) által használt számítási infrastruktúra, amely különböző adatintegrációs képességeket biztosít a különböző hálózati környezetek között. A Data Factory három különböző típusú integrációs modult kínál:

- Azure-beli integrációs modul
- Saját üzemeltetésű integrációs modul
- Azure-SQL Server Integration Services (SSIS) Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Integration Runtime (IR) egy példánya állapotának lekéréséhez futtassa a következő PowerShell-parancsot: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

A parancsmag különböző adatokat ad vissza az integrációs modul különböző típusaihoz. Ez a cikk az Integration Runtime egyes típusaihoz tartozó tulajdonságokat és állapotokat ismerteti.  

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul

Az Azure Integration Runtime számítási erőforrásait teljes mértékben kezelheti az Azure-ban. Az alábbi táblázat a **Get-AzDataFactoryV2IntegrationRuntime** parancs által visszaadott tulajdonságok leírásait tartalmazza:

### <a name="properties"></a>Tulajdonságok

A következő táblázat az Azure Integration Runtime parancsmag által visszaadott tulajdonságok leírását tartalmazza:

| Tulajdonság | Leírás |
-------- | ------------- | 
| Név | Az Azure Integration Runtime neve. |  
| Állam | Az Azure Integration Runtime állapota. | 
| Hely | Az Azure Integration Runtime helye. Az Azure Integration Runtime helyével kapcsolatos részletekért lásd: [az Integration Runtime bemutatása](concepts-integration-runtime.md). |
| DataFactoryName | Annak az adatelőállítónak a neve, amelyhez az Azure Integration Runtime tartozik. | 
| ResourceGroupName | Azon erőforráscsoport neve, amelyhez az adatelőállító tartozik.  |
| Description | Az Integration Runtime leírása.  |

### <a name="status"></a>Állapot

Az alábbi táblázat az Azure Integration Runtime lehetséges állapotait tartalmazza:

| Állapot | Megjegyzések/forgatókönyvek | 
| ------ | ------------------ |
| Online | Az Azure Integration Runtime online állapotban van, és készen áll a használatra. | 
| Offline | Az Azure Integration Runtime belső hiba miatt offline állapotban van. |

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul

Ez a szakasz a Get-AzDataFactoryV2IntegrationRuntime parancsmag által visszaadott tulajdonságok leírásait tartalmazza. 

> [!NOTE] 
> A visszaadott tulajdonságok és állapot a teljes saját üzemeltetésű integrációs modulról és a futtatókörnyezet egyes csomópontjairól tartalmaz információkat.  

### <a name="properties"></a>Tulajdonságok

Az alábbi táblázat az **egyes csomópontok** figyelési tulajdonságainak leírását tartalmazza:

| Tulajdonság | Leírás | 
| -------- | ----------- | 
| Név | A saját üzemeltetésű Integration Runtime és a hozzá társított csomópontok neve. A csomópont egy helyszíni Windows rendszerű számítógép, amelyen telepítve van a saját üzemeltetésű integrációs modul. |  
| Állapot | A saját üzemeltetésű integrációs modul általános és minden csomópontjának állapota. Példa: online/offline/korlátozott/stb. Ezekről az állapotokról további információt a következő szakaszban talál. | 
| Verzió | A saját üzemeltetésű Integration Runtime és az egyes csomópontok verziója. A saját üzemeltetésű integrációs modul verziója a csoportban lévő csomópontok többségének verziója alapján van meghatározva. Ha a saját üzemeltetésű integrációs modul telepítője eltérő verziójú csomópontokkal rendelkezik, akkor csak a logikai saját üzemeltetésű integrációs modulnak megfelelő verziószámmal rendelkező csomópontok működnek megfelelően. Mások korlátozott módban vannak, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés meghiúsul). | 
| Igénybe vehető memória | Rendelkezésre álló memória a saját üzemeltetésű integrációs modul csomópontján. Ez az érték a közel valós idejű pillanatkép. | 
| Processzorhasználat | Egy saját üzemeltetésű Integration Runtime-csomópont CPU-kihasználtsága. Ez az érték a közel valós idejű pillanatkép. |
| Hálózatkezelés (be/ki) | Egy saját üzemeltetésű integrációs modul csomópontjának hálózati kihasználtsága. Ez az érték a közel valós idejű pillanatkép. | 
| Egyidejű feladatok (futó/korlát) | **Fut**. Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték a közel valós idejű pillanatkép. <br/><br/>**Korlát**. A korlát az egyes csomópontok maximális egyidejű feladatait jelzi. Ez az érték a gép méretétől függően van meghatározva. Az egyidejű feladatok végrehajtásának korlátját növelheti a speciális forgatókönyvekben, ha a tevékenységek a CPU, a memória vagy a hálózat kihasználtsága alatt állnak. Ez a funkció egy egycsomópontos, saját üzemeltetésű integrációs futtatókörnyezettel is elérhető. |
| Szerepkör | A több csomópontos, saját üzemeltetésű integrációs modulban két típusú szerepkör létezik – diszpécser és feldolgozó. Minden csomópont munkavégző, ami azt jelenti, hogy mind a feladatok végrehajtásához használhatók. Csak egy kiosztó csomópont létezik, amely a feladatok/feladatok a Cloud servicesből való lekérésére és különböző munkavégző csomópontokra történő küldésére szolgál. A diszpécser csomópont is egy feldolgozó csomópont. |

A tulajdonságok egyes beállításai több értelmet mutatnak, ha a saját üzemeltetésű integrációs modulban két vagy több csomópont van (azaz egy kibővíthető forgatókönyvben).

#### <a name="concurrent-jobs-limit"></a>Egyidejű feladatok korlátja

Az egyidejű feladatok korlátjának alapértelmezett értéke a számítógép méretétől függően van beállítva. Az érték kiszámításához használt tényezők a RAM mennyiségétől és a gép CPU-magjainak számától függnek. Tehát minél több magot és annál több memóriát, annál magasabb az egyidejű feladatok alapértelmezett korlátja.

A csomópontok számának növelésével bővítheti a méretezést. A csomópontok számának növelésével az egyidejű feladatok korlátja az összes rendelkezésre álló csomópont egyidejű feladat-határértékének összege.  Ha például egy csomópont legfeljebb tizenkét egyidejű feladatot futtat, akkor három további hasonló csomópont hozzáadásával legfeljebb 48 egyidejű feladat futtatható (azaz 4 x 12). Javasoljuk, hogy csak akkor növelje az egyidejű feladatok korlátját, ha az egyes csomópontok alapértelmezett értékeivel alacsony erőforrás-használatot lát.

A Azure Portalban felülbírálhatja a számított alapértelmezett értéket. Válassza a szerző > kapcsolatok > Integration Runtimes > szerkesztés > csomópontok > egyidejű feladatok értékének módosítása csomópontra. Használhatja a PowerShell [Update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) parancsot is.
  
### <a name="status-per-node"></a>Állapot (/csomópont)

A következő táblázat a saját üzemeltetésű Integration Runtime csomópont lehetséges állapotait tartalmazza:

| Állapot | Leírás |
| ------ | ------------------ | 
| Online | A csomópont a Data Factory szolgáltatáshoz van csatlakoztatva. |
| Offline | A csomópont offline állapotban van. |
| Frissítés | A csomópont automatikus frissítése folyamatban van. |
| Korlátozott | Kapcsolódási probléma miatt. A hiba oka lehet a 8060-es HTTP-port, a Service Bus-kapcsolat problémája vagy a hitelesítő adatok szinkronizálása. |
| Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban található. |

Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz.

### <a name="status-overall-self-hosted-integration-runtime"></a>Állapot (teljes saját üzemeltetésű integrációs modul)

A következő táblázat a saját üzemeltetésű integrációs modul lehetséges állapotait tartalmazza. Ez az állapot a futtatókörnyezethez tartozó összes csomópont állapotától függ. 

| Állapot | Leírás |
| ------ | ----------- | 
| Regisztráció szükséges | Ehhez a saját üzemeltetésű integrációs modulhoz még nincs regisztrálva csomópont. |
| Online | Minden csomópont online állapotú. |
| Offline | Nincs online csomópont. |
| Korlátozott | A saját üzemeltetésű integrációs modulban lévő összes csomópont kifogástalan állapotban van. Ez az állapot azt a figyelmeztetést jeleníti meg, hogy egyes csomópontok leállnak. Ezt az állapotot egy hitelesítő adat szinkronizálási hibája okozhatja a diszpécser/feldolgozó csomóponton. |

A **Get-AzDataFactoryV2IntegrationRuntimeMetric** parancsmag használatával beolvashatja a részletes saját üzemeltetésű integrációs modul TULAJDONSÁGAIT tartalmazó JSON-adattartalmat, valamint a pillanatképek értékeit a parancsmag végrehajtásának időpontjában.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Minta kimenete (feltételezi, hogy két csomópont van társítva ehhez a saját üzemeltetésű integrációs modulhoz):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul

Azure-SSIS IR a SSIS-csomagok futtatásához dedikált Azure-beli virtuális gépek (VM-EK vagy csomópontok) teljes körűen felügyelt fürtje. Az SSIS-csomagok végrehajtásának Azure-SSIS IR különböző módszerekkel, például az Azure-kompatibilis SQL Server Data Tools (SSDT), a AzureDTExec parancssori segédprogram, a T-SQL és a SQL Server Management Studio (SSMS)/SQL-kiszolgáló ügynökének használatával, valamint a SSIS csomag tevékenységeinek az ADF-folyamatokban való végrehajtásával. A Azure-SSIS IR nem futtat más ADF-tevékenységeket. A kiépítés után az általános/csomópont-specifikus tulajdonságokat és állapotokat Azure PowerShell, Azure Portal és Azure Monitor segítségével figyelheti.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Az Azure SSIS Integration Runtime figyelése Azure PowerShell

A következő Azure PowerShell parancsmaggal figyelheti Azure-SSIS IR általános/csomópont-specifikus tulajdonságait és állapotait.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Tulajdonságok

A következő táblázat a Azure-SSIS IR a fenti parancsmag által visszaadott tulajdonságok leírásait tartalmazza.

| Tulajdonság/állapot              | Description                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | A Azure-SSIS IR létrehozásának UTC-ideje. |
| Csomópontok                        | A Azure-SSIS IR lefoglalt/rendelkezésre álló csomópontjai csomópont-specifikus állapotokkal (kezdő/elérhető/újrahasznosítási/nem elérhető) és végrehajtható hibákkal rendelkeznek. |
| OtherErrors                  | A nem csomópont-specifikus végrehajtható hibák a Azure-SSIS IR. |
| LastOperation                | A Azure-SSIS IR utolsó indítási/leállítási műveletének eredménye, ha a művelet sikertelen. |
| Állam                        | A Azure-SSIS IR teljes állapota (kezdeti/indítás/elindítva/leállítás/leállítva). |
| Hely                     | A Azure-SSIS IR helye. |
| NodeSize                     | A Azure-SSIS IR egyes csomópontjainak mérete. |
| NodeCount                    | A Azure-SSIS IR csomópontjainak száma. |
| MaxParallelExecutionsPerNode | A Azure-SSIS IR csomópontokon belüli párhuzamos végrehajtások maximális száma. |
| CatalogServerEndpoint        | A meglévő Azure SQL Database-kiszolgáló vagy felügyelt példány végpontja, amely a SSIS-katalógust (SSISDB) üzemelteti. |
| CatalogAdminUserName         | A meglévő Azure SQL Database-kiszolgáló vagy felügyelt példány rendszergazdai felhasználóneve. Az ADF ezeket az információkat használja az Ön nevében a SSISDB előkészítéséhez és kezeléséhez. |
| CatalogAdminPassword         | A meglévő Azure SQL Database-kiszolgáló vagy a felügyelt példány rendszergazdai jelszava. |
| CatalogPricingTier           | A Azure SQL Database-kiszolgáló által üzemeltetett SSISDB díjszabási szintje.  Nem alkalmazható az Azure SQL felügyelt példányának SSISDB. |
| VNetId                       | A Azure-SSIS IR virtuális hálózati erőforrás-azonosítója, amelyhez csatlakozni szeretne. |
| Alhálózat                       | Az a Azure-SSIS IR alhálózat neve, amelyhez csatlakozni szeretne. |
| ID (Azonosító)                           | A Azure-SSIS IR erőforrás-azonosítója. |
| Típus                         | A Azure-SSIS IR IR-típusa (felügyelt/saját üzemeltetésű). |
| ResourceGroupName            | Azon Azure-erőforráscsoport neve, amelyben az ADF és a Azure-SSIS IR létrejött. |
| DataFactoryName              | Az ADF neve. |
| Name                         | A Azure-SSIS IR neve. |
| Description                  | A Azure-SSIS IR leírása. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Állapot (Azure-SSIS IR csomóponton)

Az alábbi táblázat egy Azure-SSIS IR csomópont lehetséges állapotát tartalmazza:

| Csomópont-specifikus állapot | Description |
| -------------------- | ----------- | 
| Indítás             | A csomópont előkészítése folyamatban van. |
| Elérhető            | Ez a csomópont készen áll a SSIS-csomagok üzembe helyezésére és végrehajtására. |
| Újrahasznosítás            | A csomópont javítása/újraindítása folyamatban van. |
| Nem érhető el          | Ez a csomópont nem áll készen a SSIS-csomagok üzembe helyezésére és végrehajtására, valamint a feloldható hibákra/problémákra. |

#### <a name="status-overall-azure-ssis-ir"></a>Állapot (teljes Azure-SSIS IR)

Az alábbi táblázat egy Azure-SSIS IR lehetséges általános állapotait tartalmazza. A teljes állapot a Azure-SSIS IRhoz tartozó összes csomópont összesített állapotával függ. 

| Általános állapot | Description | 
| -------------- | ----------- | 
| Kezdeti        | A Azure-SSIS IR csomópontjai nem lettek kiosztva/előkészített állapotban. | 
| Indítás       | A Azure-SSIS IR csomópontjai vannak lefoglalva/elkészítve, és a számlázás megkezdődött. |
| Első lépések        | A Azure-SSIS IR csomópontjai kiosztottak/felkészültek, és készen állnak a SSIS-csomagok üzembe helyezésére/végrehajtására. |
| Leállítás       | A rendszer a Azure-SSIS IR csomópontjait szabadítja fel. |
| Leállítva        | A Azure-SSIS IR csomópontjai megjelentek, és a számlázás leállt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Az Azure SSIS integrációs modul figyelése Azure Portal

A Azure Portal Azure-SSIS IR figyeléséhez nyissa meg az ADF- **figyelési** központ **Integration Runtimes (integrációs** modulok) lapját, ahol az összes integrációs modult láthatja.

![Az összes integrációs modul monitorozása](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Ezután válassza ki a Azure-SSIS IR nevét a figyelési oldal megnyitásához, ahol megtekintheti a teljes/csomópont-specifikus tulajdonságokat és állapotokat. Ezen az oldalon attól függően, hogy hogyan konfigurálja a Azure-SSIS IR általános, üzembe helyezési és speciális beállításait, különböző információs/funkcionális csempéket talál.  A **típus** és a **régió** információs csempéje a Azure-SSIS IR típusát és régióját mutatja. A **csomópont mérete** tájékoztató csempe MEGJELENÍTI az SKU-t (SSIS edition_VM tier_VM sorozat), a processzorok számát és a memória méretét a Azure-SSIS IR csomópontban. A **futó/kért csomópont (ok)** tájékoztató csempe összehasonlítja a jelenleg futó csomópontok számát a Azure-SSIS IR számára korábban kért csomópontok számával. A funkcionális csempék alább olvashatók.

![A Azure-SSIS IR figyelése](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>ÁLLAPOT csempe

A Azure-SSIS IR figyelése oldal **állapot** csempén láthatja a teljes állapotát, például a **futó** vagy **leállított** állapotot. Ha kiválasztja a **futó** állapotot, megjelenik egy élő **Leállítás** gombbal rendelkező ablak, amely leállítja a Azure-SSIS IR. A **leállított** állapot kiválasztásakor megjelenik egy élő **Start** gombbal rendelkező ablak, amely elindítja a Azure-SSIS IR. Az előugró ablak is rendelkezik egy **SSIS-csomag végrehajtásával** , amely automatikusan létrehoz egy ADF-folyamatot a Azure-SSIS IR futtatott SSIS-csomag végrehajtásával (lásd: [SSIS csomagok futtatása az ADF-folyamatokban végrehajtható SSIS-előkészítési tevékenységekben](./how-to-invoke-ssis-package-ssis-activity.md)) és egy **erőforrás-azonosító** szövegmezőben, amelyből a Azure-SSIS IR erőforrás-azonosítót () másolhatja `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` . Az ADF-t és Azure-SSIS IR neveket tartalmazó Azure-SSIS IR erőforrás-azonosító utótagja olyan SSIS hoz létre, amely a független szoftvergyártók (ISV) további prémium/licenccel rendelkező összetevőinek megvásárlására és a Azure-SSIS IRhoz való kötésére használható (lásd a [prémium/licencelt összetevők telepítését a Azure-SSIS IR](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![A Azure-SSIS IR ÁLLAPOTú csempe figyelése](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>SSISDB kiszolgálói VÉGPONT csempe

Ha olyan projekt-telepítési modellt használ, amelyben a csomagok tárolása a Azure SQL Database-kiszolgáló vagy a felügyelt példány által üzemeltetett SSISDB történik, akkor a Azure-SSIS IR figyelési lapján megjelenik a **SSISDB-kiszolgáló végpontjának** csempe (lásd: [a Azure-SSIS IR központi telepítési beállítások konfigurálása](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Ezen a csempén választhat egy hivatkozást, amely kijelöli a Azure SQL Database kiszolgálóját vagy a felügyelt példányt egy ablak megnyitásához, ahol a kiszolgálói végpontot egy szövegmezőből másolhatja, és a SSMS-hez való kapcsolódáskor a csomagok üzembe helyezésére, konfigurálására, futtatására és kezelésére használhatja. Az előugró ablakban kiválaszthatja a **Azure SQL Database vagy a felügyelt példány beállításainak megjelenítése** HIVATKOZÁST a SSISDB újrakonfigurálásához/átméretezéséhez Azure Portalban.

![A Azure-SSIS IR SSISDB csempe monitorozása](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>PROXY/előkészítés csempe

Ha letölti, telepíti és konfigurálja Self-Hosted IR-t (a-t) proxyként a Azure-SSIS IR számára a helyszíni adateléréshez, akkor a Azure-SSIS IR figyelési lapján megjelenik a **proxy/előkészítés** csempéje (lásd: a Hálózatfigyelő [beállítása proxyként a Azure-SSIS IR számára](./self-hosted-integration-runtime-proxy-ssis.md)). Ezen a csempén kiválaszthatja azt a hivatkozást, amely kijelöli a szükséges elemet, hogy megnyissa a figyelési oldalát. Kiválaszthat egy másik hivatkozást is, amely kijelöli az Azure-Blob Storage az előkészítéshez a társított szolgáltatás újrakonfigurálásához.

#### <a name="validate-vnet--subnet-tile"></a>VNET/ALHÁLÓZAT csempe ellenőrzése

Ha a Azure-SSIS IRt egy VNet csatlakoztatja, a Azure-SSIS IR figyelése lapon megjelenik a **VNet/alhálózati csempe ellenőrzése** (lásd: [a Azure-SSIS IR csatlakoztatása egy VNet](./join-azure-ssis-integration-runtime-virtual-network.md)). Ezen a csempén kiválaszthat egy olyan hivatkozást, amely a VNet és az alhálózatot megjelöli egy ablak megnyitásához, ahol a VNet erőforrás-AZONOSÍTÓját () és az alhálózat nevét a szövegmezőből is ellenőrizheti, valamint `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` érvényesítheti a VNet és az alhálózati konfigurációkat, így biztosítva, hogy a szükséges bejövő/kimenő hálózati forgalom és a Azure-SSIS IR felügyelete ne legyen akadályban.

![A Azure-SSIS IR figyelése – csempe ellenőrzése](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>KAPCSOLATi csempe DIAGNOSZTIZÁLása

A Azure-SSIS IR figyelése lap **kapcsolat diagnosztizálása** csempén a **kapcsolat tesztelése** hivatkozásra kattintva megtekintheti az ablakokat, ahol ellenőrizheti a Azure-SSIS IR és a kapcsolódó csomag/konfiguráció/adattárolók közötti kapcsolatokat, valamint a felügyeleti szolgáltatásokat a teljes tartománynév (FQDN) IP MCDREAMSCENE-címével és a kijelölt porttal (lásd: [kapcsolatok tesztelése a Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![Képernyőkép, amely bemutatja, hol tesztelheti a Azure-SSIS IR és a kapcsolódó csomag/konfiguráció/adattárak közötti kapcsolatokat.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>STATIKUS nyilvános IP-címek csempe

Ha a saját statikus nyilvános IP-címeit a Azure-SSIS IR számára teszi elérhetővé, megjelenik a **statikus nyilvános IP** -címek csempe a Azure-SSIS IR monitorozási oldalon (lásd: [saját statikus nyilvános IP-címek létrehozása a Azure-SSIS IRhoz](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). Ezen a csempén kiválaszthatja az első/második statikus nyilvános IP-címeire mutató hivatkozásokat a Azure-SSIS IR számára egy ablak megnyitásához, ahol az erőforrás-azonosítót ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) egy szövegmezőből másolhatja. Az előugró ablakban bejelölheti az első/második statikus **nyilvános IP-cím beállításainak megjelenítése** hivatkozást is, amely az első/második statikus nyilvános IP-cím (Azure Portal) kezelésére használható.

![Képernyőfelvétel: az első/második statikus nyilvános IP-címek kijelölése.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>PACKAGE STOREs csempe

Ha olyan csomag-telepítési modellt használ, ahol a csomagok tárolása az Azure SQL felügyelt példánya által üzemeltetett, és Azure-SSIS IR csomagkezelő használatával felügyelt fájlrendszerben, Azure Files/SQL Server adatbázisban (MSDB) történik, akkor a **csomag tárolja** csempét a Azure-SSIS IR monitorozási lapján (lásd: [a Azure-SSIS IR központi telepítési beállításainak konfigurálása](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Ezen a csempén kiválaszthat egy hivatkozást, amely a Azure-SSIS IR csatolt csomagjainak számát jelöli egy ablak megnyitásához, ahol újrakonfigurálhatja a megfelelő társított szolgáltatásokat a Azure-SSIS IR-csomagok tárolói számára az Azure SQL felügyelt példánya által üzemeltetett fájlrendszer/Azure Files/MSDB tetején.

![A Azure-SSIS IR-csomag csempe figyelése](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>HIBA (ok) csempe

Ha problémák merülnek fel a Azure-SSIS IR indítása/leállítása/karbantartása/frissítése során, megjelenik egy további **hiba (ok)** csempe a Azure-SSIS IR figyelési oldalon. Ezen a csempén kiválaszthat egy hivatkozást, amely a Azure-SSIS IR által generált hibák számát jelöli egy ablak megnyitásához, ahol további részletekben láthatja a hibákat, és átmásolhatja őket a hibaelhárítási útmutatóban található ajánlott megoldások kereséséhez (lásd: [a Azure-SSIS IR hibaelhárítása](./ssis-integration-runtime-management-troubleshoot.md)).

![Azure-SSIS IR figyelése – csempe DIAGNOSZTIZÁLása](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Az Azure SSIS Integration Runtime figyelése Azure Monitor

A Azure-SSIS IR Azure Monitorsal való figyelésével kapcsolatban lásd: [SSIS-műveletek figyelése a Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)használatával.

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>További információ az Azure-SSIS Integration Runtime-ról

Az Azure-SSIS integrációs modulról további információt az alábbi cikkekben talál:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk általános információkat tartalmaz az integrációs modulokról, beleértve a Azure-SSIS IRt is. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](./tutorial-deploy-ssis-packages-azure.md). Ez a cikk részletes útmutatást nyújt a Azure-SSIS IR létrehozásához és a Azure SQL Database használatáról az SSIS-katalógus (SSISDB) futtatásához. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt az Azure SQL felügyelt példányának SSISDB való futtatásához. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan indíthatja el, állíthatja le vagy törölheti a Azure-SSIS IR. Emellett további csomópontok hozzáadásával is bemutatja, hogyan méretezheti azt ki. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk útmutatást nyújt a Azure-SSIS IR virtuális hálózathoz való csatlakoztatásához.

## <a name="next-steps"></a>Következő lépések
A folyamatok figyelésének különböző módjai a következő cikkekben találhatók: 

- Gyors útmutató [: adatelőállító létrehozása](quickstart-create-data-factory-dot-net.md).
- [Data Factory folyamatok figyelése Azure Monitor használatával](monitor-using-azure-monitor.md)
