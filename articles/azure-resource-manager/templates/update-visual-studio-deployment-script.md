---
title: A Visual Studio sablon-telepítési parancsfájljának frissítése az az PowerShell használatával
description: A Visual Studio sablon üzembe helyezési parancsfájljának frissítése a AzureRM-ből az PowerShell-be
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76963869"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>A Visual Studio sablon telepítési parancsfájljának frissítése az az PowerShell-modul használatával

A Visual Studio 16,4 a sablon üzembe helyezési parancsfájljában az az PowerShell modul használatával támogatott. A Visual Studio azonban nem telepíti automatikusan ezt a modult. Az az modul használatához négy lépést kell elvégeznie:

1. [AzureRM modul eltávolítása](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Telepítés az Module](/powershell/azure/install-az-ps)
1. A Visual Studio frissítése 16,4-re
1. Frissítse az üzembe helyezési parancsfájlt a projektben.

## <a name="update-visual-studio-to-164"></a>A Visual Studio frissítése 16,4-re

Frissítse a Visual Studio telepítését a 16,4-es vagy újabb verzióra. A frissítés során győződjön meg arról, hogy a Azure PowerShell összetevő nincs bejelölve. Mivel az az modult a katalóguson keresztül telepítette, nem szeretné újratelepíteni a AzureRM modult.

Ha már frissített a 16,4-es verzióra, és a Azure PowerShell összetevő be lett jelölve, akkor a Visual Studio telepítő futtatásával távolíthatja el. Ne válassza ki a Azure PowerShell összetevőt az Azure munkaterhelés vagy az egyes összetevők lapon.

## <a name="update-the-deployment-script-in-your-project"></a>Az üzembe helyezési parancsfájl frissítése a projektben

Cserélje le a "AzureRm" karakterlánc összes előfordulását az üzembe helyezési parancsfájlban az "az" értékre. A módosítások megtekintéséhez tekintse meg [a jelen](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) témakörben található változatokat. A parancsfájlok az az modulra történő frissítésével kapcsolatos további információkért lásd: [Azure PowerShell migrálása a AzureRM-ről az-](/powershell/azure/migrate-from-azurerm-to-az)ra.

## <a name="next-steps"></a>Következő lépések

A Visual Studio-projekt használatával kapcsolatos további információkért lásd: [Azure erőforráscsoport-projektek létrehozása és üzembe helyezése a Visual Studióval](create-visual-studio-deployment-project.md).
