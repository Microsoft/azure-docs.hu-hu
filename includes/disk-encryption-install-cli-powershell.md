---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 3d8cd9891329e86ce47dac6d8d44af529c104b61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386585"
---
Azure Disk Encryption az [Azure CLI](/cli/azure) és a használatával engedélyezhetők és [kezelhetők Azure PowerShell.](/powershell/azure/new-azureps-module-az) A telepítéshez helyileg kell telepítenie az eszközöket, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2.0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI rugalmasan lekérdezi az adatokat, támogatja a hosszú ideig futó műveleteket nem blokkoló folyamatokként, és megkönnyíti a szkriptek futtatását. Helyileg telepítheti az Azure CLI telepítéséhez szükséges [lépéseket követve.](/cli/azure/install-azure-cli)

Az [Azure-fiókba az Azure CLI-val](/cli/azure/authenticate-azure-cli)való bejelentkezéshez használja az az [login](/cli/azure/reference-index#az-login) parancsot.

```azurecli
az login
```

Ha ki szeretne választani egy bérlőt, amelybe be szeretne jelentkezni, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és szeretne megadni egy adott előfizetést, az [az account listával](/cli/azure/account#az-account-list) szerezze be az előfizetési listáját, és adja meg az [az account set értéket.](/cli/azure/account#az-account-set)
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: [Az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)használatának első lépések. 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) olyan parancsmagokat biztosít, [](../articles/azure-resource-manager/management/overview.md) amelyek a Azure Resource Manager használatával kezelik az Azure-erőforrásokat. Használhatja a böngészőjében a [Azure Cloud Shell,](../articles/cloud-shell/overview.md)vagy telepítheti a helyi gépére az Install the Azure PowerShell module (Az Azure PowerShell [telepítése) Azure PowerShell használatával.](/powershell/azure/install-az-ps) 

Ha már telepítve van helyileg, győződjön meg arról, hogy az Azure PowerShell SDK legújabb verzióját használja a Azure Disk Encryption. Töltse le a legújabb Azure PowerShell [kiadását.](https://github.com/Azure/azure-powershell/releases)

Az [Azure-fiókba való bejelentkezéshez használja Azure PowerShell](/powershell/azure/authenticate-azureps) [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és szeretne megadni egyet, használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmagot a listához, majd a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmagot:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatása ellenőrzi, hogy a megfelelő előfizetés lett-e kiválasztva.

Annak megerősítéséhez Azure Disk Encryption hogy a parancsmagok telepítve vannak, használja a [Get-command](/powershell/module/microsoft.powershell.core/get-command) parancsmagot:
     
```powershell
Get-command *diskencryption*
```
További információ: [Ismerkedés a Azure PowerShell.](/powershell/azure/get-started-azureps)