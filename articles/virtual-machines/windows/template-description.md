---
title: Azure Resource Manager sablonban található virtuális gépek | Microsoft Azure
description: További információ arról, hogyan van definiálva a virtuális gép erőforrása egy Azure Resource Manager sablonban.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 32b73be3faf6eedb92220725b292a3e69cf7f965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555992"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuális számítógépek egy Azure Resource Manager-sablonban

Ez a cikk egy Azure Resource Manager sablonnak a virtuális gépekre vonatkozó aspektusait ismerteti. Ez a cikk nem ismerteti a virtuális gépek létrehozásának teljes sablonját. Ehhez a Storage-fiókok, hálózati adapterek, nyilvános IP-címek és virtuális hálózatok erőforrás-definíciói szükségesek. További információt ezekről az erőforrásokról a [Resource Manager-sablonok áttekintése](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című témakörben talál.

A katalógusban sok olyan [sablon található](https://azure.microsoft.com/documentation/templates/?term=VM) , amely tartalmazza a virtuális gép erőforrását. A sablonban nem szereplő összes elem leírását itt találja.

 

Ebben a példában egy sablon egy tipikus erőforrás szakasza látható egy adott számú virtuális gép létrehozásához:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Ez a példa egy korábban létrehozott Storage-fiókra támaszkodik. A Storage-fiókot a sablonból való üzembe helyezésével hozhatja létre. A példa egy hálózati adapterre és a sablonban definiált függő erőforrásokra is támaszkodik. Ezek az erőforrások nem jelennek meg a példában.
>
>

## <a name="api-version"></a>API-verzió

Ha sablon használatával telepít erőforrásokat, meg kell adnia a használni kívánt API-verziót. A példa a virtuális gép erőforrását mutatja a következő apiVersion elem használatával:

```json
"apiVersion": "2016-04-30-preview",
```

A sablonban megadott API-verzió befolyásolja a sablonban megadható tulajdonságokat. Általánosságban a sablonok létrehozásakor ki kell választani a legújabb API-verziót. Meglévő sablonok esetében eldöntheti, hogy szeretné-e tovább használni a korábbi API-verziót, vagy a legújabb verzióra frissíti a sablont, hogy kihasználhassa az új funkciókat.

A legújabb API-verziók beszerzéséhez használja a következő lehetőségeket:

- REST API – [az összes erőforrás-szolgáltató listázása](/rest/api/resources/providers)
- PowerShell – [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI – [az Provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Paraméterek és változók

A [Paraméterek](../../azure-resource-manager/templates/template-syntax.md) megkönnyítik a sablon értékének megadását a futtatásakor. A következő példában a paraméterek szakaszt használjuk:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

A példában szereplő sablon telepítésekor adja meg a rendszergazdai fiók nevét és jelszavát az egyes virtuális gépeken, valamint a létrehozandó virtuális gépek számát. Lehetősége van paraméterek értékének megadására egy külön fájlban, amelyet a sablon felügyel, vagy ha a rendszer kéri, adja meg az értékeket.

A [változók](../../azure-resource-manager/templates/template-syntax.md) segítségével könnyedén állíthatja be a sablonban azokat az értékeket, amelyeket a rendszer ismételten használ, vagy amelyek idővel változhatnak. A következő példában a változók szakaszt használjuk:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

A példában szereplő sablon telepítésekor a rendszer változó értékeket használ a korábban létrehozott Storage-fiók nevének és azonosítójának megadásához. A változók a diagnosztikai bővítmény beállításainak megadására is használhatók. Az [ajánlott eljárásokkal Azure Resource Manager sablonokat hozhat létre](../../azure-resource-manager/templates/template-best-practices.md) , amelyek segítségével eldöntheti, hogyan szeretné felstrukturálni a sablonban lévő paramétereket és változókat.

## <a name="resource-loops"></a>Erőforrás-hurkok

Ha több virtuális gépre van szüksége az alkalmazáshoz, használhat egy másolási elemet a sablonban. Ez a választható elem hurkokat mutat be a paraméterként megadott virtuális gépek számának létrehozásával:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Emellett figyelje meg a példában, hogy a hurok indexe az erőforrás egyes értékeinek megadásakor használatos. Ha például három példányban adta meg a példányszámot, az operációsrendszer-lemezek neve myOSDisk1, myOSDisk2 és myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Ez a példa felügyelt lemezeket használ a virtuális gépekhez.
>
>

Ne feledje, hogy a sablonban lévő egyik erőforráshoz tartozó hurok létrehozásához szükség lehet a hurok használatára más erőforrások létrehozásakor vagy azokhoz való hozzáféréskor. Például több virtuális gép nem használhatja ugyanazt a hálózati adaptert, így ha a sablon három virtuális gép létrehozásán keresztül hurkokat használ, a három hálózati adapter létrehozásával is hurkot kell létrehoznia. Amikor hálózati adaptert rendel egy virtuális géphez, a rendszer a hurok indexét használja a következő azonosítására:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Függőségek

A legtöbb erőforrás attól függ, hogy a többi erőforrás megfelelően működik-e. A virtuális gépeket egy virtuális hálózattal kell társítani, és az informatikai részlegnek hálózati adapterre van szüksége. A [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) elem használatával meggyőződhet arról, hogy a hálózati adapter készen áll a virtuális gépek létrehozása előtt való használatra:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

A Resource Manager párhuzamosan helyez üzembe minden olyan erőforrást, amely nem függ más üzembe helyezett erőforrástól. A függőségek beállításakor körültekintően járjon el, mert a szükségtelen függőségek megadásával véletlenül lelassíthatja a telepítést. A függőségek több erőforráson keresztül is elhelyezhetők. A hálózati adapter például a nyilvános IP-címről és a virtuális hálózati erőforrástól függ.

Honnan tudható, hogy szükséges-e függőség? Tekintse meg a sablonban megadott értékeket. Ha a virtuális gép erőforrás-definíciójában lévő elem egy másik, ugyanabban a sablonban üzembe helyezett erőforrásra mutat, függőségre van szüksége. Például a virtuális gép egy hálózati profilt definiál:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

A tulajdonság beállításához a hálózati adapternek léteznie kell. Ezért függőségre van szükség. Akkor is meg kell adnia egy függőséget, ha egy erőforrás (gyermek) egy másik erőforrásban (szülő) van definiálva. A diagnosztikai beállítások és az egyéni parancsfájl-bővítmények például a virtuális gép alárendelt erőforrásaiként vannak meghatározva. Nem hozhatók létre, amíg a virtuális gép nem létezik. Ezért mindkét erőforrás a virtuális gépről függőként van megjelölve.

## <a name="profiles"></a>Profilok

A virtuális gépek erőforrásának definiálásakor több profil elemet is használ a rendszer. Néhány szükséges, néhányat pedig nem kötelező megadni. Például a hardwareProfile, a osProfile, a storageProfile és a networkProfile elem megadása kötelező, de a diagnosticsProfile megadása nem kötelező. Ezek a profilok olyan beállításokat határoznak meg, mint például:
   
- [méret](../sizes.md)
- [név](/azure/architecture/best-practices/resource-naming) és hitelesítő adatok
- lemez-és [operációsrendszer-beállítások](cli-ps-findimage.md)
- [hálózati adapter](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- rendszerindítási diagnosztika

## <a name="disks-and-images"></a>Lemezek és lemezképek
   
Az Azure-ban a VHD-fájlok [lemezek vagy lemezképek](../managed-disks-overview.md)ábrázolására használhatók. Ha egy VHD-fájlban lévő operációs rendszer speciális virtuális géphez van kiépítve, akkor azt lemeznek nevezzük. Ha egy VHD-fájlban lévő operációs rendszer általánosítva számos virtuális gép létrehozásához használatos, azt képként kell megadnia.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Új virtuális gépek és új lemezek létrehozása egy platform rendszerképből

Amikor létrehoz egy virtuális gépet, el kell döntenie, hogy milyen operációs rendszert kíván használni. A imageReference elem az új virtuális gép operációs rendszerének definiálására szolgál. A példa egy Windows Server operációs rendszer definícióját mutatja be:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Ha Linux operációs rendszert szeretne létrehozni, akkor használhatja ezt a definíciót:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Az operációs rendszer lemezének konfigurációs beállításai a osDisk elemmel vannak társítva. A példa egy új felügyelt lemezt határoz meg a gyorsítótárazási móddal, amely **READWRITE** van beállítva, és hogy a lemez egy [platform-rendszerképből](cli-ps-findimage.md)jön létre:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Új virtuális gépek létrehozása meglévő felügyelt lemezekről

Ha a virtuális gépeket meglévő lemezekről szeretné létrehozni, távolítsa el a imageReference és a osProfile elemeket, és adja meg a lemez beállításait:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Új virtuális gépek létrehozása felügyelt rendszerképből

Ha a virtuális gépet felügyelt lemezképből szeretné létrehozni, módosítsa a imageReference elemet, és adja meg a lemez beállításait:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Adatlemezek csatolása

Szükség esetén adatlemezeket is hozzáadhat a virtuális gépekhez. A [lemezek száma](../sizes.md) a használt operációsrendszer-lemez méretétől függ. Ha a virtuális gépek mérete Standard_DS1_v2 értékre van állítva, akkor a hozzájuk tartozó adatlemezek maximális száma kettő. A példában egy felügyelt adatlemez kerül be az egyes virtuális gépekbe:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Bővítmények

Bár a [bővítmények](../extensions/features-windows.md) különálló erőforrások, a virtuális gépekhez szorosan kötődnek. A bővítmények a virtuális gép alárendelt erőforrásaiként vagy külön erőforrásként vehetők fel. A példa a virtuális gépekhez hozzáadott [diagnosztikai bővítményt](../extensions/diagnostics-template.md) mutatja be:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Ez a kiterjesztési erőforrás a storageName változót és a diagnosztikai változókat használja az értékek megadásához. Ha módosítani szeretné a bővítmény által összegyűjtött adatokat, további teljesítményszámlálókat adhat hozzá a wadperfcounters változóhoz. Azt is megteheti, hogy a diagnosztikai adatait egy másik Storage-fiókba helyezi, mint ahol a virtuális gépek lemezeit tárolja.

Számos bővítmény telepíthető egy virtuális gépre, de a leghasznosabb valószínűleg az [Egyéni szkriptek bővítménye](../extensions/custom-script-windows.md). A példában egy start.ps1 nevű PowerShell-szkript fut az egyes virtuális gépeken, amikor először indul el:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

A start.ps1 szkript számos konfigurációs feladatot képes végrehajtani. A példában szereplő virtuális gépekhez hozzáadott adatlemezek például nem lettek inicializálva; az inicializáláshoz egyéni szkriptet is használhat. Ha több indítási feladattal rendelkezik, a start.ps1 fájllal hívhat meg más PowerShell-parancsfájlokat az Azure Storage szolgáltatásban. A példa a PowerShellt használja, de bármilyen, a használt operációs rendszeren elérhető parancsfájl-módszert használhat.

A telepített bővítmények állapotát a portál bővítmények beállításainál tekintheti meg:

![Bővítmény állapotának beolvasása](./media/template-description/virtual-machines-show-extensions.png)

A bővítmény információit a **Get-AzVMExtension** PowerShell-paranccsal, a virtuálisgép- **BŐVÍTMÉNY Get** Azure CLI-paranccsal vagy a **bővítmény információinak beolvasása** REST API is lekérheti.

## <a name="deployments"></a>Központi telepítés

A sablonok központi telepítésekor az Azure nyomon követi a csoportként telepített erőforrásokat, és automatikusan hozzárendel egy nevet ehhez a telepített csoporthoz. A központi telepítés neve megegyezik a sablon nevével.

Ha kíváncsi a telepítésben lévő erőforrások állapotára, tekintse meg az erőforráscsoportot a Azure Portalban:

![Üzembe helyezési információk beolvasása](./media/template-description/virtual-machines-deployment-info.png)
    
Nem probléma, hogy ugyanazt a sablont használja az erőforrások létrehozásához vagy a meglévő erőforrások frissítéséhez. Amikor parancsokat használ a sablonok üzembe helyezéséhez, lehetősége van megmondani, hogy melyik [módot](../../azure-resource-manager/templates/deploy-powershell.md) kívánja használni. A mód beállítható **teljes** vagy **növekményes** értékre is. Az alapértelmezett érték a növekményes frissítések használata. Ügyeljen arra, hogy a **teljes** módot használja, mert véletlenül törölheti az erőforrásokat. Ha a módot a **befejezésre** állítja, a Resource Manager törli a sablonban nem szereplő összes erőforrást.

## <a name="next-steps"></a>Következő lépések

- Hozzon létre saját sablont a [szerzői Azure Resource Manager sablonok](../../azure-resource-manager/templates/template-syntax.md)használatával.
- Telepítse a létrehozott sablont a [Windows rendszerű virtuális gép Resource Manager-sablonnal történő létrehozásával](ps-template.md).
- Megtudhatja, hogyan kezelheti a létrehozott virtuális gépeket a [Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell modullal](tutorial-manage-vm.md)című témakör áttekintésével.
- A sablonokban található erőforrástípusok JSON-szintaxisához és tulajdonságaihoz lásd: [Azure Resource Manager sablon referenciája](/azure/templates/).