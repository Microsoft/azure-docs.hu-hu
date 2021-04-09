---
title: 'PowerShell: üzembe helyezés helyi git-tárházból'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan helyezhet üzembe programkódot egy helyi git-adattárból.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070292"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd üzembe helyezi a webalkalmazás kódját egy helyi Git-adattárból.

Szükség esetén frissítsen az Azure PowerShell legújabb verziójára az [Azure PowerShell útmutatójának](/powershell/azure/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. Ezenkívül az alkalmazáskódot véglegesíteni kell egy helyi Git-adattárban.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy webalkalmazást a szükséges erőforráscsoporttal és App Service-csoporttal. Ha az aktuális könyvtár tartalmaz egy Git-adattárat, egy távoli `azure`-mappát is adjon hozzá. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
