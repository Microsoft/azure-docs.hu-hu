---
title: Linux rendszerű virtuális gép létrehozása a REST API
description: Megtudhatja, hogyan hozhat létre olyan linuxos virtuális gépet az Azure-ban, amely az Azure REST API Managed Disks és SSH-hitelesítést használ.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 519939445e67f0f993662e2faf506eb186686156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554564"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>SSH-hitelesítést használó linuxos virtuális gép létrehozása a REST API

Az Azure-beli linuxos virtuális gépek különböző erőforrásokból (például lemezekről és hálózati adapterekről) állnak, és olyan paramétereket határoznak meg, mint például a hely, a méret és az operációs rendszer rendszerképe és a hitelesítési beállítások.

Linux rendszerű virtuális gépet a Azure Portalon, az Azure CLI 2,0-ban, számos Azure SDK-n, Azure Resource Manager sablonon és számos külső eszközön (például Ansible vagy Terraform) is létrehozhat. Ezek az eszközök végül a REST API használatával hozza létre a linuxos virtuális gépet.

Ez REST API a cikk bemutatja, hogyan hozhat létre az Ubuntu 18,04-LTS rendszert futtató linuxos virtuális gépet a felügyelt lemezekkel és az SSH-hitelesítéssel.

## <a name="before-you-start"></a>Előkészületek

A kérelem létrehozása és elküldése előtt a következőkre lesz szüksége:

* Az `{subscription-id}` előfizetéséhez tartozó
  * Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli) című témakört.
* Az `{resourceGroupName}` Ön által létrehozott idő előtt
* Azonos erőforráscsoporthoz tartozó [virtuális hálózati adapter](../../virtual-network/virtual-network-network-interface.md)
* SSH-kulcspár (ha még nem rendelkezik ilyennel, létrehozhat [egy újat](mac-create-ssh-keys.md) )

## <a name="request-basics"></a>Kérések alapjai

Virtuális gép létrehozásához vagy frissítéséhez használja a következő *put* műveletet:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

A `{subscription-id}` és a paraméterek mellett `{resourceGroupName}` meg kell adnia a `{vmName}` ( `api-version` nem kötelező, de a cikket tesztelte `api-version=2017-12-01` )

A következő fejlécek megadása kötelező:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type* (Tartalomtípus):  | Kötelező. Állítsa `application/json` értékre. |
| *Authorization* (Engedélyezés): | Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |

REST API kérelmekkel kapcsolatos általános információkért tekintse meg [a REST API kérelem/válasz összetevőit](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók:

| Name                       | Kötelező | Típus                                                                                | Leírás  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True (Igaz)     | sztring                                                                              | Erőforrás helye. |
| name                       |          | sztring                                                                              | A virtuális gép neve. |
| Properties. hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Megadja a virtuális gép hardveres beállításait. |
| Properties. storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Megadja a virtuális gépek lemezeinek tárolási beállításait. |
| Properties. osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Megadja a virtuális gép operációs rendszerének beállításait. |
| Properties. networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Megadja a virtuális gép hálózati adaptereit. |

Egy példa a kérelem törzsére. Győződjön meg róla, hogy megadta a virtuális gép nevét a `{computerName}` és a `{name}` paraméterek között, a létrehozott hálózati adapter nevét `networkInterfaces` , a felhasználónevét `adminUsername` és `path` az SSH-kulcspár *nyilvános* részét (például: `~/.ssh/id_rsa.pub` ) `keyData` . A módosítani kívánt egyéb paraméterek közé tartoznak `location` a és a is `vmSize` .  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

A kérelem törzsében elérhető definíciók teljes listáját a [virtuális gépek kérelem törzs-definícióinak létrehozása vagy frissítése](/rest/api/compute/virtualmachines/createorupdate#definitions)című témakörben tekintheti meg.

## <a name="sending-the-request"></a>A kérelem küldése

A HTTP-kérelem elküldéséhez használhatja a kívánt ügyfelet. Egy [böngészőbeli eszközt](/rest/api/compute/virtualmachines/createorupdate) is használhat a **kipróbálás** gombra kattintva.

### <a name="responses"></a>Válaszok

A műveletnek két sikeres válasza van a virtuális gép létrehozásához vagy frissítéséhez:

| Név        | Típus                                                                              | Leírás |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 létrehozva | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Létrehozva     |

Az előző példában szereplő, egy virtuális gépet létrehozó ProvisioningState által létrehozott, tömörített *201* -as válasz egy *vmId* -hozzárendelést tartalmaz, és *létrehozta* a  :

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

További információ a REST API válaszokról: [a válaszüzenet feldolgozása](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Következő lépések

Az Azure REST API-kkal vagy más felügyeleti eszközökkel, például az Azure CLI-vel vagy a Azure PowerShell-vel kapcsolatos további információkért tekintse meg a következőket:

- [Azure számítási szolgáltató REST API](/rest/api/compute/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/)
