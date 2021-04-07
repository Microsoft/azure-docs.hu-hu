---
title: Service Fabric alkalmazás frissítése a PowerShellben
description: Azure PowerShell szkript minta – Azure Service Fabric-alkalmazás frissítése és figyelése a PowerShell használatával.
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
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784697"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric alkalmazás frissítése

Ez a minta parancsfájl egy futó Service Fabric alkalmazás-példányt frissít a 1.3.0 verzióra. A szkript átmásolja az új alkalmazáscsomag a fürt rendszerkép-tárolójába, regisztrálja az alkalmazás típusát, és eltávolítja a szükségtelen alkalmazáscsomag-csomagot.  A szkript elindít egy figyelt frissítést, és folyamatosan ellenőrzi a frissítési állapotot, amíg a frissítés be nem fejeződik vagy vissza nem áll. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | A Service Fabric-fürtben vagy egy adott alkalmazásban lévő összes alkalmazás beolvasása.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Lekéri egy Service Fabric alkalmazás frissítésének állapotát. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Beolvassa a Service Fabric-fürtön regisztrált Service Fabric alkalmazás-típusokat. |
| [Regisztráció törlése – ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Service Fabric-alkalmazás típusának törlése.  |
| [Másolás – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Service Fabric-alkalmazáscsomag másolása a rendszerkép-tárolóba.  |
| [Regisztráció – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Service Fabric alkalmazás típusának regisztrálása. |
| [Start – ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Egy Service Fabric alkalmazás frissítése a megadott Application Type-verzióra. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Eltávolít egy Service Fabric alkalmazáscsomag a rendszerkép-tárolóból.|


## <a name="next-steps"></a>Következő lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/overview).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
