---
title: Az Azure PowerShell-szkript mintát - alkalmazás létrehozása és üzembe helyezése helyi Git-adattárból |} A Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 80d1b295db7d1eb1e0ea0dda9522aa3875ea882d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136439"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd üzembe helyezi a webalkalmazás kódját egy helyi Git-adattárból.

Szükség esetén frissítsen az Azure PowerShell legújabb verziójára az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. Ezenkívül az alkalmazáskódot véglegesíteni kell egy helyi Git-adattárban.

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

| Parancs | Megjegyzések |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy webalkalmazást a szükséges erőforráscsoporttal és App Service-csoporttal. Ha az aktuális könyvtár tartalmaz egy Git-adattárat, egy távoli `azure`-mappát is adjon hozzá. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
