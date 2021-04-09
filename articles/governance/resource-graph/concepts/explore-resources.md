---
title: Az Azure-erőforrások áttekintése
description: Ismerje meg, hogyan használhatja az erőforrás-diagram lekérdezési nyelvét, és hogyan derítheti fel, hogyan kapcsolódnak egymáshoz.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: a76ce2bb34fd6e99fedf5030c4723b6dd5acbb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917724"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Az Azure-erőforrások felfedezése a Resource Graph használatával

Az Azure Resource Graph lehetővé teszi az Azure-erőforrások gyors és méretezhető feltárását és felderítését. Gyors válaszokra tervezve, nagyszerű módszer a környezet megismerésére és az Azure-erőforrásokkal kapcsolatos tulajdonságaira is.

## <a name="explore-virtual-machines"></a>A virtuális gépek megismerése

Az Azure-ban egy közös erőforrás egy virtuális gép. Erőforrástípusként a virtuális gépek számos olyan tulajdonsággal rendelkeznek, amely lekérdezhető. Minden tulajdonság egy beállítást biztosít a keresett erőforrás szűréséhez vagy megkereséséhez.

### <a name="virtual-machine-discovery"></a>Virtuális gépek felderítése

Kezdjük egy egyszerű lekérdezéssel, amely egyetlen virtuális gépet kap a környezetből, és megtekintheti a visszaadott tulajdonságokat.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> A Azure PowerShell `Search-AzGraph` parancsmag alapértelmezés szerint egy **pscustomobject formájában kapja** ad vissza. Ahhoz, hogy a kimenet ugyanúgy nézzen ki, mint amit az Azure CLI ad vissza, a `ConvertTo-Json` rendszer a parancsmagot használja. A **mélység** alapértelmezett értéke _2_. A _100_ értékre állításával az összes visszaadott szintet át kell alakítani.

A JSON-eredmények a következő példához hasonló módon vannak strukturálva:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

A tulajdonságok további információkat is közölnek a virtuális gép erőforrásával kapcsolatban. Ezek a tulajdonságok többek között a következők: operációs rendszer, lemezek, címkék és az erőforráscsoport és az előfizetés, amely a tagja.

### <a name="virtual-machines-by-location"></a>Virtuális gépek hely szerint

A Virtual Machines erőforrással kapcsolatos tudnivalókat a **Location (hely** ) tulajdonság használatával számítjuk fel. A lekérdezés frissítéséhez el fogjuk távolítani a korlátot, és összegezjük a hely értékeinek számát.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

A JSON-eredmények a következő példához hasonló módon vannak strukturálva:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Most láthatjuk, hogy hány virtuális gép van az egyes Azure-régiókban.

### <a name="virtual-machines-by-sku"></a>Virtuális gépek SKU szerint

Lépjen vissza az eredeti virtuális gép tulajdonságaiba, és próbálja meg megkeresni az összes olyan virtuális gépet, amelynek az SKU mérete **Standard_B2s**. A visszaadott JSON-t láthatjuk, hogy az a **Properties. hardwareprofile. vmsize** fájlban van tárolva. A lekérdezés frissítésével megkeresheti az összes olyan virtuális gépet, amely megfelel az adott méretnek, és csak a virtuális gép és a régió nevét adja vissza.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Prémium szintű felügyelt lemezekhez csatlakoztatott virtuális gépek

A **Standard_B2s** virtuális gépekhez csatolt prémium szintű felügyelt lemezek részleteinek beszerzéséhez kiterjesztjük a lekérdezést, hogy visszaállítsa a felügyelt lemezek erőforrás-azonosítóját.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Az eredmény a lemez-azonosítók listája.

### <a name="managed-disk-discovery"></a>Felügyelt lemezek felderítése

Az előző lekérdezés első rekordjával megvizsgáljuk az első virtuális géphez csatolt felügyelt lemezen található tulajdonságokat. A frissített lekérdezés a lemez AZONOSÍTÓját használja, és megváltoztatja a típust.

Példa az előző lekérdezés kimenetére például:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

A lekérdezés futtatása előtt honnan tudtuk, hogy a **típusnak** most a **Microsoft. számítás/lemezek**? Ha megtekinti a teljes azonosítót, a **/providers/Microsoft.Compute/Disks/** a karakterlánc részeként fog megjelenni.
Ez a karakterlánc-kódrészlet a keresendő típussal kapcsolatos célzást nyújt. Alternatív módszerként a korlátot a típus alapján távolíthatja el, ehelyett csak az azonosító mező alapján kereshet. Mivel az azonosító egyedi, a rendszer csak egy rekordot ad vissza, és a **Type (típus** ) tulajdonsága ezt a részletet adja meg.

> [!NOTE]
> Ahhoz, hogy ez a példa működjön, a saját környezetből származó eredménnyel kell helyettesítenie az azonosító mezőt.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

A JSON-eredmények a következő példához hasonló módon vannak strukturálva:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>A virtuális gépek megismerése nyilvános IP-címek kereséséhez

Ez a lekérdezés először megkeresi és tárolja a virtuális gépekhez csatlakozó összes hálózati adapter (NIC) erőforrását. Ezután a lekérdezések a hálózati adapterek listájának használatával keresik meg a nyilvános IP-címekhez tartozó IP-címek erőforrásait, és tárolják ezeket az értékeket. Végül a lekérdezések megadják a nyilvános IP-címek listáját.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

A következő lekérdezésben a fájl (Azure CLI) vagy a változó (Azure PowerShell) segítségével lekérheti a kapcsolódó hálózati adapterek erőforrásait, és megtudhatja, hogy van-e nyilvános IP-cím a hálózati ADAPTERhez csatlakoztatva.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Végül a fájlban (az Azure CLI-ben) vagy a változóban (Azure PowerShell) tárolt nyilvános IP-cím-erőforrások listáját használja a tényleges nyilvános IP-cím lekéréséhez a kapcsolódó objektumból és a megjelenítésből.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Ha szeretné megtudni, hogyan hajthatja végre ezeket a lépéseket egyetlen lekérdezésben az `join` operátorral, tekintse meg a [virtuális gépek listázása hálózati interfésszel és nyilvános IP-](../samples/advanced.md#join-vmpip) címmel című mintát.

## <a name="next-steps"></a>Következő lépések

- További információ a [lekérdezési nyelvről](query-language.md).
- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
