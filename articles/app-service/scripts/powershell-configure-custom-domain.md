---
title: 'PowerShell: egyéni tartomány kiosztása'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan rendelhet hozzá egyéni tartományt egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 749250f4ee5e1a0a39c9dc36795fa07e9da743a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89072230"
---
# <a name="assign-a-custom-domain-to-a-web-app-using-powershell"></a>Egyéni tartomány társítása egy webalkalmazáshoz a PowerShell használatával

Ez a példaszkript egy webalkalmazást hoz létre az App Service szolgáltatásban a kapcsolódó erőforrásokkal együtt, majd leképezi rá a `www.<yourdomain>` tartományt. 

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/)található utasítással, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz. Emellett hozzáféréssel kell rendelkeznie a tartományregisztrálója DNS-konfigurációs oldalához.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

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
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
