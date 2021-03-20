---
title: Laborok létrehozása vagy módosítása Azure Resource Manager sablonok használatával
description: Megtudhatja, hogyan használhatja Azure Resource Manager-sablonokat a PowerShell-lel a Labs automatikus létrehozására vagy módosítására egy DevTest-laborban
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87272640"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Labs automatikus létrehozása vagy módosítása Azure Resource Manager-sablonokkal és a PowerShell-lel

A DevTest Labs számos Azure Resource Manager sablont és PowerShell-parancsfájlt biztosít, amelyek segítségével gyorsan és automatikusan hozhat létre új laborokat, illetve módosíthatja a meglévő laborokat, majd telepítheti ezeket az erőforrásokat.

Ebből a cikkből megtudhatja, hogyan használhatja ezeket a sablonokat és parancsfájlokat a laborok létrehozásának, módosításának és üzembe helyezésének automatizálására. A cikk azt is bemutatja, hol találhat további információt arról, hogyan használható a PowerShell a DevTest Labs gyakori feladatainak végrehajtásához.

## <a name="step-1-gather-your-templates-and-scripts"></a>1. lépés: a sablonok és a parancsfájlok összegyűjtése
Előre elkészített [Azure Resource Manager sablonokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) és [PowerShell-parancsfájlokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) a nyilvános GitHub- [tárházban](https://github.com/Azure/azure-devtestlab)talál. Használhatja őket a-ként, vagy testre szabhatja őket az igényeinek megfelelően, és tárolhatja őket a saját [privát git](devtest-lab-add-artifact-repo.md)-tárházában.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2. lépés: a Azure Resource Manager-sablon módosítása
Ha még nem hozott létre sablont, kövesse az [első Azure Resource Manager-sablon létrehozása](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) című témakör lépéseit.

Emellett a [Azure Resource Manager sablonok létrehozásával kapcsolatos ajánlott eljárások](../azure-resource-manager/templates/template-best-practices.md) számos útmutatást és javaslatot nyújtanak a megbízható és könnyen használható Azure Resource Manager sablonok létrehozásához. Általában a megadott megközelítések vagy példák valamelyikének egy változatát fogja használni, és módosítania kell a sablont az igényeinek megfelelően.

## <a name="step-3-deploy-resources-with-powershell"></a>3. lépés: erőforrások üzembe helyezése a PowerShell-lel
Miután testreszabta a sablonokat és a parancsfájlokat, kövesse az [erőforrások Resource Manager-sablonokkal és Azure PowerShell való üzembe helyezéséhez](../azure-resource-manager/templates/deploy-powershell.md)szükséges lépéseket. A cikk általános információkat nyújt a Azure PowerShell használatáról Azure Resource Manager sablonokkal az erőforrások Azure-ba történő telepítéséhez.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>A DevTest Labs-ben elvégezhető gyakori feladatok a PowerShell használatával
A PowerShell használatával számos más gyakori feladat is automatizálható. A dokumentáció következő fejezetei a feladatok végrehajtásához szükséges lépéseket ismertetik.

* [Egyéni rendszerkép létrehozása VHD-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-fájl feltöltése a labor Storage-fiókjába a PowerShell használatával](devtest-lab-upload-vhd-using-powershell.md)
* [Külső felhasználó hozzáadása laborhoz a PowerShell használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Egyéni labor-szerepkör létrehozása a PowerShell használatával](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan hozhat létre egy [privát git-tárházat](devtest-lab-add-artifact-repo.md) , ahol a testreszabott sablonokat vagy parancsfájlokat fogja tárolni.
* Ismerkedjen meg a [Azure Resource Manager-sablonokkal az Azure Gyorsindítás sablon-galériájában](https://github.com/Azure/azure-quickstart-templates).
