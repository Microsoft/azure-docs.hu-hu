---
title: 'PowerShell: Kapcsolódás SQL Databasehoz'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan csatlakoztatható egy alkalmazás egy SQL Databasehoz.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3b355a830703b51264d2f4b819d5be7f2627b4e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077789"
---
# <a name="connect-an-app-service-app-to-sql-database"></a>App Service-alkalmazás összekötése SQL Database

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre adatbázist Azure SQL Database és egy App Service alkalmazásban. Ezután az alkalmazást az alkalmazás beállításainak használatával fogja összekapcsolni.

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/)található utasítással, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő paranccsal távolíthatja el az erőforráscsoportot, App Service alkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy App Service alkalmazást. |
| [Új – AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy kiszolgálót. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy kiszolgálói szintű tűzfalszabály-szabályt. |
| [Új – AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist vagy egy rugalmas adatbázist. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy App Service alkalmazás konfigurációját. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A Azure App Service további Azure PowerShell-mintákat talál a [Azure PowerShell mintákban](../samples-powershell.md).
