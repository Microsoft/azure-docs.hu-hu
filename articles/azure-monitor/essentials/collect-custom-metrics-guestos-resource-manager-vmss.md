---
title: Windows méretezési csoport metrikáinak összegyűjtése a Azure Monitor sablonnal
description: Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windowsos virtuálisgép-méretezési csoportokhoz
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: c5f536295bce07e70606f1ff286b08f8465d59f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048947"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Windowsos virtuálisgép-méretezési csoport Azure Resource Manager-sablonjának használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor [Windows Azure Diagnostics (wad) bővítmény](../agents/diagnostics-extension-overview.md)használatával metrikákat és naplókat gyűjthet a virtuális gép, a Cloud Service vagy az Azure Service Fabric-fürt részeként futó vendég operációs rendszerből (vendég operációs rendszerből). A bővítmény a korábban csatolt cikkben felsorolt különböző helyszínekre is küldhet telemetria.  

Ez a cikk a vendég operációs rendszer teljesítményének mérőszámait ismerteti a Windows rendszerű virtuálisgép-méretezési csoportoknak a Azure Monitor adattárba való küldésének folyamata során. A Windows Azure Diagnostics 1,11-es verziótól kezdődően a metrikák közvetlenül a Azure Monitor metrikák tárolójába írhatók, ahol a standard platform metrikái már össze vannak gyűjtve. Ha ezen a helyen tárolja őket, akkor ugyanazokat a műveleteket érheti el, amelyek elérhetők a platform metrikái számára. A műveletek közé tartoznak a közel valós idejű riasztások, a diagramok, az Útválasztás, a REST API való hozzáférés és egyebek. A múltban a Windows Azure Diagnostics bővítmény az Azure Storage-ba írt, de nem a Azure Monitor adattárat.  

Ha most ismerkedik a Resource Manager-sablonokkal, ismerkedjen meg a [sablonok központi telepítésével](../../azure-resource-manager/management/overview.md) , valamint azok struktúrájával és szintaxisával.  

## <a name="prerequisites"></a>Előfeltételek

- Az előfizetést regisztrálni kell a [Microsoft. ininsights](../../azure-resource-manager/management/resource-providers-and-types.md)szolgáltatásban. 

- [Azure PowerShell](/powershell/azure) telepítve kell lennie, vagy használhatja a [Azure Cloud Shell](../../cloud-shell/overview.md). 

- A virtuális gép erőforrásának olyan régióban kell lennie [, amely támogatja az egyéni metrikákat](./metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor beállítása adatfogadóként 
A Azure Diagnostics bővítmény egy **adattároló** nevű funkciót használ a metrikák és naplók különböző helyszínekre való továbbításához. A következő lépések bemutatják, hogyan használható a Resource Manager-sablon és a PowerShell egy virtuális gép üzembe helyezéséhez az új Azure Monitor adatfogadó használatával. 

## <a name="author-a-resource-manager-template"></a>Resource Manager-sablon készítése 
Ebben a példában egy nyilvánosan elérhető [minta sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)használhat:  

- **AAzuredeploy.json** egy virtuálisgép-méretezési csoport üzembe helyezéséhez előre konfigurált Resource Manager-sablon.

- **AAzuredeploy.parameters.json** paraméter olyan fájl, amely a virtuális géphez beállítani kívánt felhasználónevet és jelszót tárolja. Az üzembe helyezés során a Resource Manager-sablon a fájlban megadott paramétereket használja. 

Töltse le és mentse a fájlokat helyileg. 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.jsmódosítása
Nyissa meg a **azuredeploy.parameters.js** fájlt:  
 
- Adjon meg egy **vmSKU** , amelyet központilag telepíteni szeretne. Javasoljuk, hogy Standard_D2_v3. 
- Adja meg a virtuálisgép-méretezési csoporthoz használni kívánt **windowsOSVersion** . A 2016-Datacenter használatát javasoljuk. 
- Nevezze el a virtuálisgép-méretezési csoport erőforrását, amelyet a **vmssName** tulajdonsággal kíván üzembe helyezni. Példa: **VMSS-wad-test**.    
- Adja meg a virtuálisgép-méretezési csoporton futtatni kívánt virtuális gépek számát a **instanceCount** tulajdonság használatával.
- Adja meg a virtuálisgép-méretezési csoport **adminUsername** és **adminPassword** értékeit. Ezek a paraméterek a méretezési csoportba tartozó virtuális gépek távoli elérésére szolgálnak. Ha el szeretné kerülni, hogy a virtuális gép eltérítve legyen, **ne használja a** sablonban szereplőket. A robotok a nyilvános GitHub-adattárakban keresik meg az internetet felhasználónevek és jelszavak számára. Valószínűleg ezekkel az alapértékekkel tesztelik a virtuális gépeket. 


###  <a name="modify-azuredeployjson"></a>azuredeploy.jsmódosítása
Nyissa meg a **azuredeploy.js** fájlt. 

Adjon hozzá egy változót a Storage-fiók adatainak tárolására a Resource Manager-sablonban. A diagnosztikai konfigurációs fájlban megadott naplók vagy teljesítményszámlálók a Azure Monitor metrika-tárolóba és az itt megadott Storage-fiókba is íródnak: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Keresse meg a virtuálisgép-méretezési csoport definícióját az erőforrások szakaszban, és adja hozzá az **Identity** szakaszt a konfigurációhoz. Ez a beállítás biztosítja, hogy az Azure rendszeridentitást rendeljen hozzá. Ez a lépés azt is biztosítja, hogy a méretezési csoportba tartozó virtuális gépek a saját magukról Azure Monitor a következőket:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

A virtuálisgép-méretezési csoport erőforrásában keresse meg a **virtualMachineProfile** szakaszt. Vegyen fel egy új, **extensionsProfile** nevű profilt a bővítmények kezeléséhez.  


A **extensionProfile** adjon hozzá egy új bővítményt a sablonhoz, ahogy az a **VMSS-wad-Extension** szakaszban látható.  Ez a szakasz az Azure-erőforrások bővítmény felügyelt identitásai, amelyek biztosítják, hogy a kibocsátott metrikákat Azure Monitor fogadja el. A **név** mező bármilyen nevet tartalmazhat. 

A következő kód az MSI bővítménnyel kiegészítve hozzáadja a diagnosztikai bővítményt és a konfigurációt a virtuálisgép-méretezési csoport erőforrásának kiterjesztési erőforrásként. A teljesítményszámlálók igény szerint adhatók hozzá vagy távolíthatók el: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
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
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Adjon hozzá egy **dependsOn** a Storage-fiókhoz, hogy a megfelelő sorrendben legyen létrehozva: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Hozzon létre egy Storage-fiókot, ha még nincs létrehozva a sablonban: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Mentse és zárjunk be mindkét fájlt. 

## <a name="deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése 

> [!NOTE]  
> Az Azure Diagnostics bővítmény 1,5-es vagy újabb verzióját kell futtatnia **, és** a **autoUpgradeMinorVersion:** tulajdonság értéke **true (igaz** ) a Resource Manager-sablonban. Az Azure ezután betölti a megfelelő bővítményt, amikor elindítja a virtuális gépet. Ha nem rendelkezik ezekkel a beállításokkal a sablonban, módosítsa őket, és telepítse újra a sablont. 


A Resource Manager-sablon üzembe helyezéséhez használja a Azure PowerShell:  

1. Indítsa el a PowerShellt. 
1. Jelentkezzen be az Azure-ba a használatával `Login-AzAccount` .
1. Az előfizetések listájának lekérése a használatával `Get-AzSubscription` .
1. Állítsa be a létrehozni kívánt előfizetést, vagy frissítse a virtuális gépet: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Hozzon létre egy új erőforráscsoportot az üzembe helyezett virtuális géphez. Futtassa az alábbi parancsot: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ne felejtse el használni az egyéni metrikák számára engedélyezett Azure-régiót. Ne felejtse el használni az [Egyéni metrikák számára engedélyezett Azure-régiót](./metrics-custom-overview.md#supported-regions).
 
1. Futtassa a következő parancsokat a virtuális gép üzembe helyezéséhez:  

   > [!NOTE]  
   > Ha egy meglévő méretezési csoport frissítését szeretné frissíteni, a rendszer a parancs végére **növekményt** ad hozzá. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Miután az üzembe helyezés sikeres volt, keresse meg a virtuálisgép-méretezési csoportját a Azure Portal. A Azure Monitornak metrikákat kell kibocsátania. 

   > [!NOTE]  
   > A kiválasztott **vmSkuSize** hibákba ütközhet. Ebben az esetben térjen vissza a **azuredeploy.js** fájlhoz, és frissítse a **vmSkuSize** paraméter alapértelmezett értékét. Javasoljuk, hogy próbálkozzon **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>A metrikák diagramja 

1. Jelentkezzen be az Azure Portalra. 

1. A bal oldali menüben válassza a **figyelő** elemet. 

1. A **figyelés** lapon válassza a **metrikák** lehetőséget. 

   ![Figyelő – mérőszámok lap](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Módosítsa az összesítési időszakot az **elmúlt 30 percre**.  

1. Az erőforrás legördülő menüben válassza ki a létrehozott virtuálisgép-méretezési készletet.  

1. A névterek legördülő menüben válassza az **Azure. VM. Windows. Guest** lehetőséget. 

1. A metrikák legördülő menüben válassza a **memória \% által véglegesített bájtok használatban** lehetőséget.  

Azt is megteheti, hogy a metrika dimenzióit használja egy adott virtuális géphez való diagramra, illetve a méretezési csoportba tartozó egyes virtuális gépek ábrázolására. 



## <a name="next-steps"></a>Következő lépések
- További információ az [Egyéni metrikákkal](./metrics-custom-overview.md)kapcsolatban.