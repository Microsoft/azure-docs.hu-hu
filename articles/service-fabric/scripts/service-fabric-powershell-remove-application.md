---
title: Alkalmazás eltávolítása egy fürtről a PowerShellben
description: Azure PowerShell parancsfájl-minta – alkalmazás eltávolítása egy Service Fabric-fürtből.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 686afa791df88382e3e5e1b2d233317c36bf1dd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791306"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Alkalmazás eltávolítása Service Fabric-fürtről a PowerShell használatával

Ez a parancsfájl töröl egy futó Service Fabric alkalmazás-példányt, és törli a fürtből az alkalmazás típusának és verziójának regisztrációját.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) | Egy futó Service Fabric alkalmazás példányának eltávolítása a fürtből.  |
| [Regisztráció törlése – ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Egy Service Fabric alkalmazás típusának és verziójának a fürtből való regisztrációjának törlése. |

## <a name="next-steps"></a>Következő lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/overview).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
