---
title: Figyelési & diagnosztika hozzáadása egy Azure-beli virtuális géphez
description: Hozzon létre egy új Windowsos virtuális gépet az Azure Diagnostics bővítmény használatával Azure Resource Manager sablonnal.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 05/31/2017
ms.openlocfilehash: 6d365c7e927c11f52b97fbb0cc01a7aa37ad5afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560055"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Figyelés és diagnosztika használata Windows rendszerű virtuális gépekkel és Azure Resource Manager-sablonokkal
A Azure Diagnostics bővítmény a Windows-alapú Azure-beli virtuális gépek monitorozási és diagnosztikai funkcióit biztosítja. Ezeket a képességeket a virtuális gépen engedélyezheti, ha a bővítményt a Azure Resource Manager sablon részeként is engedélyezi. A virtuálisgép-sablonok részét képező bővítményekkel kapcsolatos további információkért lásd: [Azure Resource Manager sablonok létrehozása VM-bővítményekkel](../windows/template-description.md#extensions) . Ez a cikk azt ismerteti, hogyan adhatja hozzá a Azure Diagnostics bővítményt egy Windows rendszerű virtuálisgép-sablonhoz.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adja hozzá a Azure Diagnostics bővítményt a virtuális gép erőforrás-definícióhoz
Ha engedélyezni szeretné a diagnosztikai bővítményt egy Windows rendszerű virtuális gépen, hozzá kell adnia a bővítményt virtuálisgép-erőforrásként a Resource Manager-sablonban.

Egy egyszerű Resource Manager-alapú virtuális gép esetében adja hozzá a bővítmény konfigurációját a virtuális gép *erőforrás* -tömbhöz: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Egy másik gyakori szabály, hogy hozzáadja a bővítmény konfigurációját a sablon gyökérszintű erőforrások csomópontján ahelyett, hogy a virtuális gép erőforrásai csomópont alatt kellene meghatároznia. Ezzel a módszerrel explicit módon meg kell adnia a kiterjesztés és a virtuális gép közötti hierarchikus kapcsolatot a *név* és a *típus* értékével. Például: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A bővítmény mindig a virtuális géphez van társítva, közvetlenül a virtuális gép erőforrás-csomópontja alatt definiálhatja, vagy megadhatja azt az alapszinten, és a hierarchikus elnevezési konvenció használatával társíthatja azt a virtuális géphez.

Virtual Machine Scale Sets a bővítmények konfigurációját a *VirtualMachineProfile* *extensionProfile* tulajdonsága határozza meg.

A *Publisher* tulajdonság a **Microsoft. Azure. Diagnostics** és a *Type* tulajdonság értékeként a **IaaSDiagnostics** egyedi módon azonosítja a Azure Diagnostics-bővítményt.

A *Name (név* ) tulajdonság értéke használható az erőforráscsoport kiterjesztésére. Kifejezetten a **Microsoft. ininsights. VMDiagnosticsSettings** lehetővé teszi, hogy könnyen azonosítható legyen a Azure Portal annak biztosítására, hogy a figyelési diagramok helyesen jelenjenek meg a Azure Portalban.

A *typeHandlerVersion* meghatározza a használni kívánt bővítmény verzióját. A *autoUpgradeMinorVersion* alverziójának **true** értékre állításával biztosíthatja, hogy a bővítmény legújabb, másodlagos verziója elérhető legyen. Erősen ajánlott mindig a *autoUpgradeMinorVersion* beállítani, hogy mindig **igaz** legyen, hogy mindig a legújabb elérhető diagnosztikai bővítményt használja az új funkciókkal és hibajavításokkal. 

A *Settings (beállítások* ) elem a bővítmény konfigurációs tulajdonságait tartalmazza, amely beállítható és olvasható a bővítményből (más néven nyilvános konfiguráció). A *xmlcfg* tulajdonság a diagnosztikai naplók, a teljesítményszámlálók stb. XML-alapú konfigurációját tartalmazza, amelyeket a diagnosztika ügynöke gyűjt. Az XML-sémával kapcsolatos további információkért tekintse meg a [diagnosztika konfigurációs sémáját](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) . Az általános gyakorlat az, hogy a tényleges XML-konfigurációt változóként tárolja a Azure Resource Manager sablonban, majd összefűzi és base64 kódolja őket a *xmlcfg* értékének beállításához. Tekintse meg a [diagnosztikai konfigurációs változók](#diagnostics-configuration-variables) című szakaszt, és Ismerje meg, hogyan tárolhatja az XML-változókat. A *storageAccount* tulajdonság annak a Storage-fióknak a nevét adja meg, amelybe a diagnosztikai adatait át szeretné adni. 

A *protectedsettingsfromkeyvault* (más néven privát konfiguráció) tulajdonságai megadhatók, de a beállítás után nem olvasható vissza. A *protectedsettingsfromkeyvault* csak írható természete lehetővé teszi a titkos kulcsok, például a diagnosztikai adatok írására szolgáló Storage-fiók kulcsának tárolására.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Diagnosztikai Storage-fiók megadása paraméterekként
A fenti diagnosztikai kiterjesztésű JSON-kódrészlet feltételezi, hogy a *existingdiagnosticsStorageAccountName* és a *existingdiagnosticsStorageResourceGroup* két paraméterrel határozza meg a diagnosztikai adatok tárolására szolgáló diagnosztikai tárolási fiókot. A diagnosztikai Storage-fiók paraméterként való megadása lehetővé teszi a diagnosztikai tárolási fiók különböző környezetekben történő módosítását, például ha egy másik diagnosztikai Storage-fiókot szeretne használni a teszteléshez, és egy másikat az éles üzembe helyezéshez.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Az ajánlott eljárás egy diagnosztikai tárolási fiók megadása egy másik erőforráscsoporthoz, mint a virtuális gép erőforráscsoport. Az erőforráscsoportok saját élettartammal rendelkező központi telepítési egységnek tekinthetők, a virtuális gépek üzembe helyezhetők és újratelepíthetők új konfigurációk frissítéseiként, de előfordulhat, hogy továbbra is ugyanazon a Storage-fiókban szeretné tárolni a diagnosztikai adatait a virtuális gépek központi telepítései között. A Storage-fiók egy másik erőforrásban való használata lehetővé teszi, hogy a Storage-fiók fogadja a különböző virtuálisgép-példányok adatait, így egyszerűen elháríthatja a különböző verziókon keresztüli problémákat.

> [!NOTE]
> Ha Windows virtuálisgép-sablont hoz létre a Visual studióból, előfordulhat, hogy az alapértelmezett Storage-fiók úgy van beállítva, hogy ugyanazt a Storage-fiókot használja, ahol a virtuális gép VHD-jét feltöltötte. Ezzel egyszerűbbé válik a virtuális gép kezdeti beállítása. A sablon újraszámítása egy másik Storage-fiók használatára, amely paraméterként adható át. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnosztikai konfigurációs változók
Az előző diagnosztikai bővítmény JSON-kódrészlete egy *accountid* változót határoz meg, amely leegyszerűsíti a Storage-fiók kulcsainak lekérését a diagnosztikai tárolóhoz:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A diagnosztikai bővítmény *xmlcfg* tulajdonsága több, egymással összefűzött változó használatával van definiálva. A változók értékei XML-ben vannak, ezért a JSON-változók beállításakor helyesen kell elmenekülniük.

Az alábbi példa azt a diagnosztikai konfigurációs XML-t ismerteti, amely a szabványos rendszerszintű teljesítményszámlálókat gyűjti össze a Windows-eseménynaplókkal és a diagnosztikai infrastruktúra naplóival együtt. A rendszer megmenekült, és megfelelően formázott, így a konfiguráció közvetlenül beilleszthető a sablon változók szakaszába. Tekintse meg a [diagnosztikai konfigurációs sémát](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) a konfigurációs XML részletesebben olvasható példájának megjelenítéséhez.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

A fenti konfigurációban található mérőszámok definíciójának XML-csomópontja egy fontos konfigurációs elem, amely azt határozza meg, hogy az XML-ben a *PerformanceCounter* -csomópontban korábban definiált teljesítményszámlálók hogyan legyenek összesítve és tárolva. 

> [!IMPORTANT]
> Ezek a metrikák a figyelési diagramokat és riasztásokat irányítják a Azure Portal.  A *resourceID* és **MetricAggregation** **metrikák** CSOMÓPONTJÁNAK szerepelnie kell a virtuális gép diagnosztikai konfigurációjában, ha látni szeretné a virtuális gép figyelési adatait a Azure Portalban. 
> 
> 

Az alábbi példa az XML-t mutatja be a metrikák meghatározásához: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

A *resourceID* attribútum egyedi módon azonosítja a virtuális gépet az előfizetésében. Ügyeljen arra, hogy az előfizetés () és a resourceGroup () függvényt használja, hogy a sablon automatikusan frissítse ezeket az értékeket azon előfizetés és erőforráscsoport alapján, amelyre telepíteni kívánja.

Ha több Virtual Machines hoz létre egy hurokban, a *resourceID* értéket egy copyIndex () függvénnyel kell feltöltenie az egyes virtuális gépek megfelelő megkülönböztetéséhez. A *xmlCfg* érték a következőképpen módosítható:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

A *PT1M* és a *PT1H* MetricAggregation értéke egy percen belül összesíti az összesítést, illetve egy órán át összesítést.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-táblák a tárolóban
A fenti metrikai konfiguráció a diagnosztikai Storage-fiókban lévő táblákat a következő elnevezési konvenciókkal hozza létre:

* **WADMetrics**: standard előtag az összes WADMetrics-táblához
* **PT1H** vagy **PT1M**: azt jelzi, hogy a tábla 1 órán vagy 1 percen belül összesített adatokat tartalmaz
* **P10D**: azt jelzi, hogy a tábla 10 napig tartalmaz-e az adatok gyűjtésének megkezdése után.
* **V2S**: karakterlánc-állandó
* **ééééhhnn**: az a dátum, amikor a tábla elkezdte az adatok gyűjtését

Példa: a *WADMetricsPT1HP10DV2S20151108* olyan mérőszámokat tartalmaz, amelyek összesített száma egy óra alatt 10 nap, 11 – november – 2015    

Minden WADMetrics-tábla a következő oszlopokat tartalmazza:

* **PartitionKey**: a partíciós kulcs a *resourceID* érték alapján épül fel a virtuálisgép-erőforrás egyedi azonosítására. Például: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: a formátumot követi `<Descending time tick>:<Performance Counter Name>` . A csökkenő időtartamú Tick-számítás a maximális idő, amely az összesítési időszak kezdetének időpontját jelöli. Például, ha a mintavételi időszak 10 – Nov-2015 és 00:00Hrs UTC, akkor a számítás a következő lesz: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . A rendelkezésre álló memória bájtjainak teljesítményszámláló a sor kulcsa a következőképpen fog kinézni: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: a teljesítményszámláló neve. Ez megegyezik az XML-konfigurációban definiált *counterSpecifier* .
* **Maximum**: a teljesítményszámláló maximális értéke az összesítési időszakban.
* **Minimum**: a teljesítményszámláló minimális értéke az összesítési időszakban.
* **Összesen**: az összesítési időszakban jelentett teljesítményszámláló összes értékének összege.
* **Darabszám**: a teljesítményszámláló számára jelentett értékek teljes száma.
* **Average (átlag**): a teljesítményszámláló átlagos (teljes/darabszám) értéke az összesítési időszakban.

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai bővítménnyel rendelkező Windows rendszerű virtuális gépek teljes mintája: [201-VM-monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* A Azure Resource Manager sablon üzembe helyezése [Azure PowerShell](../windows/ps-template.md) vagy [Azure parancssor](../linux/create-ssh-secured-vm-from-template.md) használatával
* További információ a [Azure Resource Manager sablonok létrehozásáról](../../azure-resource-manager/templates/template-syntax.md)