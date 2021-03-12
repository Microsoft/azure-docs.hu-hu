---
title: Események összesítése Windows Azure Diagnostics
description: Ismerje meg, hogy az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához a WAD használatával hogyan összesítheti és gyűjtheti az eseményeket.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: eaf40bafb68820b56fc9bf00dd4c11fbb337406f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613459"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Események összesítése és gyűjtése a Windows Azure Diagnostics használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Ha Azure Service Fabric-fürtöt futtat, érdemes összegyűjteni a naplókat egy központi helyen lévő összes csomópontról. A központi helyen található naplók segítségével elemezheti és elháríthatja a fürtben felmerülő problémákat, illetve a fürtben futó alkalmazások és szolgáltatások hibáit.

A naplók feltöltésének és összegyűjtésének egyik módja a Windows Azure Diagnostics (WAD) bővítmény használata, amely a naplókat feltölti az Azure Storage-ba, és lehetővé teszi a naplók küldését az Azure-ba Application Insights vagy Event Hubs. Külső folyamattal is elolvashatja az eseményeket a tárolóból, és elhelyezheti azokat egy Analysis platform termékében, például [Azure monitor naplókban](./service-fabric-diagnostics-oms-setup.md) vagy egy másik naplózási megoldásban.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
A cikk a következő eszközöket használja:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Azure Resource Manager-sablon](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric platform eseményei
Service Fabric beállítja a beépített [naplózási csatornákat](service-fabric-diagnostics-event-generation-infra.md), amelyek a következő csatornákat előre konfigurálták a bővítménnyel, hogy a figyelési és diagnosztikai adatait egy Storage-táblába vagy máshová küldje el:
  * [Operatív események](service-fabric-diagnostics-event-generation-operational.md): a Service Fabric platform által végrehajtott magasabb szintű műveletek. Ilyenek például az alkalmazások és a szolgáltatások létrehozása, a csomópont-állapot módosítása és a frissítési információk. Ezeket Windows esemény-nyomkövetés (ETW) naplókból bocsátják ki
  * [Reliable Actors programozási modell eseményei](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>A diagnosztikai bővítmény üzembe helyezése a portálon
A naplók összegyűjtésének első lépéseként telepítenie kell a diagnosztikai bővítményt a Service Fabric fürt virtuálisgép-méretezési csoportjának csomópontjaira. A diagnosztikai bővítmény összegyűjti a naplókat az egyes virtuális gépeken, és feltölti azokat a megadott Storage-fiókba. Az alábbi lépések azt ismertetik, hogyan valósítható meg ez az új és a meglévő fürtökhöz a Azure Portal és Azure Resource Manager-sablonokon keresztül.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>A diagnosztikai bővítmény üzembe helyezése a fürt létrehozásának részeként Azure Portal
A fürt létrehozásakor a fürtkonfiguráció lépésben bontsa ki a választható beállításokat, és győződjön meg arról, hogy a diagnosztika be van **kapcsolva** (az alapértelmezett beállítás).

![Azure Diagnostics beállítások a portálon a fürt létrehozásához](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Javasoljuk, hogy töltse le a sablont, **mielőtt rákattint a Létrehozás gombra** az utolsó lépésben. További részletekért tekintse [meg a Service Fabric-fürt beállítása Azure Resource Manager sablon használatával](service-fabric-cluster-creation-via-arm.md)című témakört. Ahhoz, hogy a (fent felsorolt) csatornákon adatokat gyűjtsön, a sablonnak meg kell változtatnia.

![Fürtözött sablon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Most, hogy az Azure Storage-ban összesíti az eseményeket, [állítson be Azure monitor naplókat](service-fabric-diagnostics-oms-setup.md) az elemzések megszerzéséhez és a lekérdezéshez a Azure monitor naplók portálon

>[!NOTE]
>Jelenleg nincs mód a táblákba eljuttatott események szűrésére vagy a vőlegényre. Ha nem valósít meg eseményeket a táblából, a tábla továbbra is növekedni fog (az alapértelmezett sapka 50 GB). A módosítással kapcsolatos utasítások a jelen [cikkben olvashatók](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Emellett van egy példa arra, hogy egy adatápolási szolgáltatás fut a [watchdog mintában](https://github.com/Azure-Samples/service-fabric-watchdog-service), és azt javasoljuk, hogy saját magának is írjon egyet, hacsak nem jó oka, hogy a naplókat 30 vagy 90 napos időkereten belül tárolja.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>A diagnosztikai bővítmény üzembe helyezése Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Hozzon létre egy fürtöt a diagnosztikai bővítménnyel
Ha a Resource Manager használatával szeretne fürtöt létrehozni, hozzá kell adnia a diagnosztikai konfiguráció JSON-t a teljes Resource Manager-sablonhoz. A Resource Manager-sablonok mintáinak részeként egy öt virtuális gépre kiterjedő Resource Manager-sablont biztosítunk, amely diagnosztikai konfigurációval bővült. Ezt a helyet az Azure Samples katalógusában tekintheti meg: [öt csomópontos fürt diagnosztika Resource Manager-sablonnal](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Ha a Resource Manager-sablonban szeretné megtekinteni a diagnosztikai beállítást, nyissa meg a azuredeploy.jsfájlt, és keresse meg a **IaaSDiagnostics**. Ha a sablon használatával szeretne fürtöt létrehozni, válassza az előző hivatkozáson elérhető **üzembe helyezés az Azure-** ban gombot.

Azt is megteheti, hogy letölti a Resource Manager-mintát, módosítja azt, és létrehoz egy fürtöt a módosított sablonnal a `New-AzResourceGroupDeployment` paranccsal egy Azure PowerShell ablakban. Tekintse meg a következő kódot a parancshoz megadott paraméterekhez. Az erőforráscsoportok PowerShell használatával történő üzembe helyezésével kapcsolatos részletes információkért tekintse meg az [erőforráscsoport üzembe helyezése a Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md)című cikket.

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>A diagnosztikai bővítmény hozzáadása egy meglévő fürthöz
Ha olyan meglévő fürttel rendelkezik, amelyen nincs telepítve a diagnosztika, a fürt sablonján keresztül is hozzáadhatja vagy frissítheti azt. Módosítsa a meglévő fürt létrehozásához használt Resource Manager-sablont, vagy töltse le a sablont a portálról a korábban leírtak szerint. Módosítsa a fájl template.jsa következő feladatok végrehajtásával:

Vegyen fel egy új tárolási erőforrást a sablonba az erőforrások szakasz hozzáadásával.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Ezután adja hozzá a paramétereket szakaszhoz közvetlenül a Storage-fiókok definíciói között `supportLogStorageAccountName` . Cserélje le a helyőrző szöveges *Storage-fiók nevét* a kívánt Storage-fiók nevére.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Ezután frissítse a `VirtualMachineProfile` template.jsfájljának szakaszát úgy, hogy hozzáadja a következő kódot a bővítmények tömbben. Ügyeljen arra, hogy a beillesztés helyétől függően egy vesszőt adjon hozzá a kezdethez vagy a végéhez.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Miután a leírtak szerint módosította a template.jsfájlt, tegye közzé újra a Resource Manager-sablont. Ha a sablon exportálása megtörtént, a deploy.ps1 fájl futtatása újból közzéteszi a sablont. A telepítést követően győződjön meg arról, hogy a **ProvisioningState** **sikeres** volt.

> [!TIP]
> Ha tárolókat helyez üzembe a fürtön, engedélyezze a WAD-t a Docker-statisztika felvételéhez, ha hozzáadja ezt a **WadCfg > DiagnosticMonitorConfiguration** szakaszhoz.

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Tárolási kvóta frissítése

Mivel a bővítmény által kitöltött táblák a kvóta megtalálata előtt növekednek, érdemes megfontolni a kvóta méretének csökkentését. Az alapértelmezett érték 50 GB, és a sablon alatt, a `overallQuotaInMB` alatti területen konfigurálható `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Napló-gyűjtemények konfigurációi
A további csatornákból származó naplók is elérhetők gyűjteményként, íme néhány gyakori konfiguráció, amelyet az Azure-ban futó fürtök sablonjában tehet meg.

* Operatív csatorna – alap: alapértelmezés szerint engedélyezve van a Service Fabric és a fürt által végrehajtott magas szintű műveletek, beleértve az esetlegesen megjelenő csomópontok eseményeit, egy új alkalmazás üzembe helyezését, vagy egy frissítés visszaállítását stb. Az események listáját az [operatív csatorna eseményei](./service-fabric-diagnostics-event-generation-operational.md)című rész tartalmazza.
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Működési csatorna – részletes: Ez magában foglalja az állapotadatok és a terheléselosztási döntések, valamint az alapszintű működési csatorna összes adatát. Ezeket az eseményeket a rendszer vagy a kód hozza létre a Health vagy a Load Reporting API-k, például a [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) vagy a [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100))használatával. Ha ezeket az eseményeket a Visual Studio diagnosztikai Eseménynaplóban szeretné megtekinteni, adja hozzá a "Microsoft-ServiceFabric: 4:0x4000000000000008" kifejezést a ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Adat-és üzenetkezelési csatorna – alap: az üzenetküldés során generált kritikus naplók és események (jelenleg csak a ReverseProxy) és az adatelérési út, valamint a részletes működési csatorna naplói. Ezek az események a kérelmek feldolgozásának hibáit és a ReverseProxy kapcsolatos egyéb kritikus problémákat, valamint a feldolgozott kérelmeket igénylik. **Ez a javaslat az átfogó naplózásra**. Ha ezeket az eseményeket a Visual Studio diagnosztikai Eseménynaplójában szeretné megtekinteni, adja hozzá a "Microsoft-ServiceFabric: 4:0x4000000000000010" kifejezést a ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Adatok & üzenetkezelési csatorna – részletes: részletes csatorna, amely tartalmazza az adatokból és üzenetküldésből származó összes nem kritikus naplót a fürtben és a részletes operatív csatornán. Az összes fordított proxy esemény részletes hibaelhárításáról a [fordított proxy diagnosztikai útmutatójában](service-fabric-reverse-proxy-diagnostics.md)olvashat.  Ha ezeket az eseményeket a Visual Studio diagnosztikai eseménynaplójában szeretné megtekinteni, adja hozzá a "Microsoft-ServiceFabric: 4:0x4000000000000020" kifejezést a ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ez a csatorna nagyon nagy mennyiségű eseményt tartalmaz, így a részletes csatornán keresztüli események gyűjtése lehetővé teszi a gyors nyomkövetést, és a tárolási kapacitást is felhasználhatja. Csak akkor kapcsolja be ezt a beállítást, ha feltétlenül szükséges.


Ahhoz, hogy az **alapszintű operatív csatorna** a lehető legkevesebb zajjal rendelkező átfogó naplózásra legyen ajánlva, a `EtwManifestProviderConfiguration` `WadCfg` sablonban a következőhöz hasonlóan fog kinézni:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Adatgyűjtés az új EventSource csatornákról

Ha frissíteni szeretné a diagnosztikát, hogy olyan új EventSource-csatornákból gyűjtse be a naplókat, amelyek új alkalmazást kívánnak telepíteni, hajtsa végre a meglévő fürthöz tartozó diagnosztika beállításához korábban ismertetett lépéseket.

A `EtwEventSourceProviderConfiguration` konfigurációs frissítés a PowerShell-paranccsal való alkalmazása előtt frissítse a fájl template.jsjának szakaszát az új EventSource-csatornák bejegyzéseinek hozzáadásához `New-AzResourceGroupDeployment` . Az eseményforrás neve a kód részeként van definiálva a Visual Studio által generált ServiceEventSource.cs fájlban.

Ha például az eseményforrás neve My-EventSource, adja hozzá a következő kódot, hogy az eseményeket My-Eventsource egy MyDestinationTableName nevű táblába helyezze.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

A teljesítményszámlálók és az eseménynaplók összegyűjtéséhez módosítsa a Resource Manager-sablont a [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai funkciókkal Azure Resource Manager sablon segítségével](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Ezután tegye közzé újra a Resource Manager-sablont.

## <a name="collect-performance-counters"></a>Teljesítményszámlálók gyűjtése

A teljesítmény-mérőszámok fürtből való összegyűjtéséhez adja hozzá a teljesítményszámlálókat a "WadCfg > DiagnosticMonitorConfiguration" a fürt Resource Manager-sablonjában. Tekintse meg a [teljesítmény figyelése a wad](service-fabric-diagnostics-perf-wad.md) -mel című témakört, amely az `WadCfg` adott teljesítményszámlálók gyűjtésének módosítására szolgál. A begyűjtést javasolt teljesítményszámlálók listáját [Service Fabric teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) .
  
Ha Application Insights fogadót használ az alábbi részben leírtak szerint, és szeretné, hogy ezek a metrikák megjelenjenek a Application Insightsban, akkor ügyeljen arra, hogy a fent látható módon adja hozzá a fogadó nevét a "mosogatók" szakaszban. Ez automatikusan elküldi a Application Insights erőforráshoz egyedileg konfigurált teljesítményszámlálókat.


## <a name="send-logs-to-application-insights"></a>Naplók elküldése a Application Insightsba

### <a name="configuring-application-insights-with-wad"></a>Application Insights konfigurálása a WAD-sel

>[!NOTE]
>Ez jelenleg csak a Windows-fürtökre vonatkozik.

Az adatok a WAD-ből az Azure Application Insightsba való küldésének két fő módja van, amely egy Application Insights-gyűjtőnek a WAD-konfigurációhoz való hozzáadásával érhető el, a Azure Portal vagy egy Azure Resource Manager sablonon keresztül.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Application Insights kialakítási kulcs hozzáadása fürt létrehozásakor Azure Portal

![AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Fürt létrehozásakor, ha a diagnosztika "bekapcsolva" értékre van kapcsolva, egy Application Insights rendszerállapot-kulcs megadására szolgáló opcionális mező jelenik meg. Ha itt illeszti be a Application Insights kulcsot, a Application Insights fogadó automatikusan konfigurálva lesz a fürt üzembe helyezéséhez használt Resource Manager-sablonban.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>A Application Insights-gyűjtő hozzáadása a Resource Manager-sablonhoz

A Resource Manager-sablon "WadCfg" vegyen fel egy "fogadót" a következő két módosítással együtt:

1. Adja hozzá a fogadó konfigurációt közvetlenül a deklarálása után `DiagnosticMonitorConfiguration` :

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. A fogadót a `DiagnosticMonitorConfiguration` következő sor hozzáadásával adja meg a `DiagnosticMonitorConfiguration` `WadCfg` (közvetlenül a `EtwProviders` deklarált előtt):

    ```json
    "sinks": "applicationInsights"
    ```

A fenti kódrészletekben az "applicationInsights" nevet is használták a fogadó leírására. Ez nem követelmény, és mindaddig, amíg a fogadó neve szerepel a "mosogatók" között, bármilyen sztringre beállíthatja a nevet.

Jelenleg a fürtből származó naplók **nyomkövetésként** jelennek meg Application Insights "log Viewerben. Mivel a platformról érkező nyomok többsége "tájékoztató" jellegű, a fogadó konfigurációjának módosítását is megteheti, hogy csak "figyelmeztetés" vagy "hiba" típusú naplókat küldjön. Ezt úgy teheti meg, hogy "csatornákat" ad hozzá a fogadóhoz, ahogy azt a [jelen cikk](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)mutatja.

>[!NOTE]
>Ha a portálon vagy a Resource Manager-sablonban helytelen Application Insights kulcsot használ, akkor manuálisan kell módosítania a kulcsot, és frissítenie kell a fürtöt, majd újra kell telepítenie.

## <a name="next-steps"></a>Következő lépések

Az Azure Diagnostics megfelelő konfigurálása után a ETW és a EventSource naplókból származó adatok jelennek meg a tároló tábláiban. Ha úgy dönt, hogy Azure Monitor naplókat, Kibana vagy bármely más adatelemzési és vizualizációs platformot használ, amely nem közvetlenül a Resource Manager-sablonban van konfigurálva, ügyeljen arra, hogy a kívánt platformot állítsa be a tárolási táblákból származó adatok olvasásához. Ezt Azure Monitor naplóknál viszonylag triviálisan használják, és az [esemény-és naplózási elemzésekben](service-fabric-diagnostics-event-analysis-oms.md)is ismertetjük. Application Insights ebben az értelemben egy kis különleges eset, mivel a diagnosztikai bővítmény konfigurációjának részeként konfigurálható, ezért a [megfelelő cikkben](service-fabric-diagnostics-event-analysis-appinsights.md) tájékozódhat, ha úgy dönt, hogy az AI-t használja.

>[!NOTE]
>Jelenleg nincs mód a táblába eljuttatott események szűrésére vagy a vőlegényre. Ha nem valósít meg eseményeket a táblából, a tábla továbbra is növekedni fog. Jelenleg a [watchdog mintában](https://github.com/Azure-Samples/service-fabric-watchdog-service)futó adatápolási szolgáltatásra van példa, ezért azt javasoljuk, hogy saját magának is írjon egyet, hacsak nem jó oka, hogy a naplókat 30 vagy 90 napos időkereten belül tárolja.

* [Ismerje meg, hogyan gyűjthet teljesítményszámlálókat vagy naplókat a diagnosztikai bővítmény használatával](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Események elemzése és vizualizáció Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Események elemzése és vizualizáció Azure Monitor naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
* [Események elemzése és vizualizáció Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Események elemzése és vizualizáció Azure Monitor naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
