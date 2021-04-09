---
title: Stackify az Azure Linux-ügynök bővítményének újranyomkövetése
description: Telepítse a Stackify retrace Linux-ügynököt egy linuxos virtuális gépre.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559001"
---
# <a name="stackify-retrace-linux-agent-extension"></a>A Linux-ügynök bővítményének Stackify újranyomkövetése

## <a name="overview"></a>Áttekintés

A Stackify olyan termékeket biztosít, amelyek nyomon követhetik az alkalmazás részleteit, így gyorsan megtalálhatja és elháríthatja a problémákat. A fejlesztői csapatok esetében az újbóli nyomkövetés egy teljes körűen integrált, több környezetből álló, az alkalmazások teljesítményének növelésére használható. Számos eszközt egyesít, minden fejlesztési csapatnak szüksége van.

Az újbóli nyomkövetés az egyetlen eszköz, amely a következő képességeket biztosítja az összes környezetben egyetlen platformon.

* Alkalmazások teljesítményének kezelése (APM)
* Alkalmazás-és kiszolgáló-naplózás
* Hibák nyomon követése és figyelése
* Kiszolgáló, alkalmazás és egyéni metrikák

**Tudnivalók a Stackify Linux-ügynök bővítménnyel kapcsolatban**

Ez a bővítmény telepítési útvonalat biztosít a Linux-ügynök újranyomkövetéshez. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer 

Az újranyomkövetési ügynök futtatható a Linux-disztribúciók ellen

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 és 17,04 |
| Debian | 7,9 + és 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Linux Stackify-ügynökének kiterjesztése megköveteli, hogy a célként megadott virtuális gép csatlakozik az internethez. 

Előfordulhat, hogy módosítania kell a hálózati konfigurációt, hogy engedélyezze a kapcsolódást a Stackify számára. lásd: https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Bővítményséma

---

A következő JSON a Stackify újranyomkövetési ügynök bővítményének sémáját mutatja be. A kiterjesztéshez a `environment` és a szükséges `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Sablonalapú telepítés 

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager sablonban használható a Stackify retrace Linux-ügynök bővítmény futtatásához Azure Resource Manager sablon központi telepítése során.  

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: a gyermek erőforrások nevének és típusának beállítása.

Az alábbi példa azt feltételezi, hogy a Stackify retrace Linux-bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a JSON a virtuális gép "erőforrások": [] objektumára kerül.

A kiterjesztéshez a `environment` és a szükséges `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Ha a bővítmény JSON-fájlját a sablon gyökerébe helyezi, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMExtension` parancs segítségével telepítheti a Stackify retrace Linux-ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni.

A kiterjesztéshez a `environment` és a szükséges `activationKey` .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése 

Az Azure CLI-eszköz használatával üzembe helyezheti a Stackify retrace Linux-ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre.  

A kiterjesztéshez a `environment` és a szükséges `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="error-codes"></a>Hibakódok

| Hibakód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 10 | Telepítési hiba | a wget megadása kötelező |
| 20 | Telepítési hiba | Python szükséges |
| 30 | Telepítési hiba | a sudo megadása kötelező |
| 40 | Telepítési hiba | a activationKey megadása kötelező |
| 51 | Telepítési hiba | Az operációs rendszer disztribúciója nem támogatott |
| 60 | Telepítési hiba | a környezet megadása kötelező |
| 70 | Telepítési hiba | Ismeretlen |
| 80 | Hiba engedélyezése | A szolgáltatás telepítése nem sikerült |
| 90 | Hiba engedélyezése | A szolgáltatás indítása nem sikerült |
| 100 | Hiba letiltása | A szolgáltatás leállítása sikertelen |
| 110 | Hiba letiltása | A szolgáltatás eltávolítása nem sikerült |
| 120 | Eltávolítási hiba | A szolgáltatás leállítása sikertelen |

Ha további segítségre van szüksége, vegye fel a kapcsolatot a Stackify támogatási szolgálatával https://support.stackify.com .