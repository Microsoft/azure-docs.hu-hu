---
title: Azure PowerShell-példaszkript – Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz | Microsoft Docs
description: Azure PowerShell-példaszkript – Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c9214d8906ad00744f811782f732b15aeec3a7f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136540"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz, PowerShell-lel

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd hozzáköti egy egyéni tartománynév SSL-tanúsítványát. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Hozzáfér a tartományregisztrálója DNS-konfigurációs oldalához.
- Rendelkezik egy érvényes .PFX-fájllal, valamint a kapcsolódó jelszóval a feltölteni és hozzákötni kívánt SSL-tanúsítványhoz.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Létrehoz egy SSL-tanúsítványkötést egy webalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
