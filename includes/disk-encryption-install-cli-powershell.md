---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: be6de283ed230a6e6a6b4986abb0a36386e36925
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211991"
---
Azure Disk Encryption engedélyezhető és felügyelhető az [Azure CLI](/cli/azure) -n és [Azure PowerShellon](/powershell/azure/new-azureps-module-az)keresztül. Ehhez telepítenie kell az eszközöket helyileg, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2,0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI rugalmas lekérdezési adatokra lett tervezve, támogatja a hosszan futó műveleteket a nem blokkoló folyamatokban, és megkönnyíti a parancsfájlok futtatását. Az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakör lépéseit követve helyileg is telepítheti.

Ha be [szeretné jelentkezni az Azure-fiókjába az Azure CLI-vel](/cli/azure/authenticate-azure-cli), használja az az [login](/cli/azure/reference-index#az-login) parancsot.

```azurecli
az login
```

Ha ki szeretné választani a bejelentkezni kívánt bérlőt, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott ilyet, szerezze be az előfizetések listáját az [az Account List](/cli/azure/account#az-account-list) paranccsal, és adja meg az az [Account set](/cli/azure/account#az-account-set)paranccsal.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: Ismerkedés [Az Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)-mel. 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) olyan parancsmagokat biztosít, amelyek a [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. A böngészőben a [Azure Cloud Shell](../articles/cloud-shell/overview.md)használatával is használhatja, vagy telepítheti a helyi gépre a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részben leírtak szerint. 

Ha már helyileg telepítette, győződjön meg arról, hogy a Azure Disk Encryption konfigurálásához a Azure PowerShell SDK legújabb verzióját használja. Töltse le [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases)legújabb verzióját.

Ha [Azure PowerShell használatával szeretne bejelentkezni az Azure-fiókjába](/powershell/azure/authenticate-azureps?view=azps-2.5.0), használja a [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmagot.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egyet, a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmaggal listázhatja őket, majd a [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmagot:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatásával ellenőrizheti, hogy a megfelelő előfizetés van-e kiválasztva.

A Azure Disk Encryption-parancsmagok telepítésének megerősítéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) parancsmagot:
     
```powershell
Get-command *diskencryption*
```
További információ: [Bevezetés a Azure PowerShell](/powershell/azure/get-started-azureps)használatába.