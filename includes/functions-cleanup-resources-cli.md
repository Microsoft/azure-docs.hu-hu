---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0f560e292ecba8a1186a400e4a40b0552c394298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425041"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáadja az Azure Storage-várólista kimeneti kötését, az összes erőforrást helyben kell tartania, ahogy a már elvégzett műveletekre épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---