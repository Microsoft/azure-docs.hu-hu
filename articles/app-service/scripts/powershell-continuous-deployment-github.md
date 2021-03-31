---
title: 'PowerShell: folyamatos üzembe helyezés a GitHubról'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan hozhat létre egy alkalmazást CI/CD-vel a GitHubról.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0ccb108b55844f822a42a3f1bccbb182a5658289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073862"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Webalkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel

Ez a minta parancsfájl egy webalkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd beállítja a [folyamatos üzembe helyezést](../deploy-continuous-deployment.md) egy GitHub-tárházból. A folyamatos üzembe helyezés nélküli GitHubos üzembe helyezésről lásd: [Webalkalmazás létrehozása és kód üzembe helyezése a GitHubról](powershell-deploy-github.md).

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/)található utasítással, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz. Emellett győződjön meg a következőkről is:

- Az alkalmazás kódja egy, az Ön tulajdonában álló nyilvános vagy privát GitHub-adattárban található. Az automatikus buildek beszerzéséhez strukturálja a tárházat az [adattár előkészítése](../deploy-continuous-deployment.md#prepare-your-repository) táblázat alapján.
- [Létrehozott egy személyes hozzáférési jogkivonatot a GitHub-fiókjában](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosít egy erőforrást egy erőforráscsoportban. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
