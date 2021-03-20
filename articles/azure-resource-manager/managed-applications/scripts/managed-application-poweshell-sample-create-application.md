---
title: Azure PowerShell parancsfájl-minta – felügyelt alkalmazás üzembe helyezése
description: Azure PowerShell minta parancsfájl-mintát biztosít, amely egy felügyelt alkalmazás definícióját telepíti az előfizetésre.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86055885"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Felügyelt alkalmazás üzembe helyezése egy szolgáltatás-katalógusban a PowerShell használatával

Ez a szkript üzembe helyezi egy felügyelt alkalmazás definícióját a szolgáltatáskatalógusból.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsot használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | Létrehoz egy felügyelt alkalmazást. Megadja a sablonhoz szükséges definícióazonosítót és paramétereket. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](/powershell/azure/get-started-azureps).
