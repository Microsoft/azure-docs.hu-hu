---
title: Azure Stream Analytics-feladatok exportálása Azure Resource Manager sablon
description: Ez a cikk a Azure Stream Analytics feladatokhoz Azure Resource Manager sablon exportálását ismerteti.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968954"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics-feladatok exportálása Azure Resource Manager sablon

[Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md) lehetővé teszik az infrastruktúra kódként való megvalósítását. A sablon egy JavaScript Object Notation (JSON) fájl, amely meghatározza az erőforrások infrastruktúráját és konfigurációját. Megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

A Azure Resource Manager sablon exportálásával újra üzembe helyezhet egy Azure Stream Analytics feladatot.

## <a name="open-a-job-in-vs-code"></a>Feladatok megnyitása a VS Code-ban

Sablon exportálásához először meg kell nyitnia egy meglévő Stream Analytics feladatot a Visual Studio Code-ban. 

Ha a feladatot egy helyi projektbe szeretné exportálni, keresse meg az exportálni kívánt feladatot a Azure Portal **stream Analytics Explorerben** . A **lekérdezés** lapon válassza a **Megnyitás a Visual Studióban**lehetőséget. Ezután válassza ki a **Visual Studio Code**-ot.

![Stream Analytics-feladatok megnyitása a Visual Studio Code-ban](./media/resource-manager-export/open-job-vs-code.png)

A Visual Studio Code Stream Analytics feladatok kezelésével kapcsolatos további információkért tekintse meg a [Visual Studio Code](quick-create-vs-code.md)gyors útmutatóját.

## <a name="compile-the-script"></a>A szkript fordítása 

A következő lépés a feladatok parancsfájljának lefordítása egy Azure Resource Manager sablonhoz. A parancsfájl fordítása előtt győződjön meg arról, hogy a feladathoz legalább egy bemenet és egy kimenet konfigurálva van. Ha nincs megadva bemenet vagy kimenet, először konfigurálnia kell a bemenetet és a kimenetet.

1. A Visual Studio Code-ban navigáljon a feladatok *Transformation. asaql* fájljához.

   ![Transformation. asaql fájl a Visual Studio Code-ban](./media/resource-manager-export/transformation-asaql.png)

1. Kattintson a jobb gombbal a *Transformation. asaql* fájlra, és válassza az **ASA: szkript fordítása** lehetőséget a menüből.

1. Figyelje meg, hogy az **üzembe helyezési** mappa megjelenik a stream Analytics feladatok munkaterületen.

1. Fedezze fel a *JobTemplate. JSON* fájlt, amely az üzembe helyezéséhez használt Azure Resource Management-sablon.

## <a name="complete-the-parameters-file"></a>A parameters (paraméterek) fájl befejezése

Ezután fejezze be az Azure Resource Management-sablon paramétereit tartalmazó fájlt.

1. Nyissa meg a *JobTemplate. Parameters. JSON* fájlt, amely a Visual Studio Code-ban található stream Analytics feladatok munkaterületének **központi telepítése** mappájában található.

1. Figyelje meg, hogy a bemeneti és a kimeneti kulcsok null értékűek. Cserélje le a null értékeket a bemeneti és kimeneti erőforrások tényleges hozzáférési kulcsaival.

1. Mentse a paramétereket tartalmazó fájlt.

## <a name="deploy-using-templates"></a>Üzembe helyezés sablonok használatával

Készen áll a Azure Stream Analytics-feladatok üzembe helyezésére az előző szakaszban létrehozott Azure Resource Manager-sablonok használatával.

Futtassa a következő parancsot egy PowerShell-ablakban. Ügyeljen arra, hogy a *ResourceGroupName*, a *TemplateFile*és a *TemplateParameterFile* a tényleges reaplce, valamint a *JobTemplate. JSON* és a *JobTemplate. Parameters. JSON* fájl teljes elérési útját a feladatok munkaterület **központi telepítése mappájába** .

Ha nincs Azure PowerShell konfigurálva, kövesse az [Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-Az-ps)című témakör lépéseit.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics feladatok helyi tesztelése élő bevitelsel a Visual Studio Code használatával](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics feladatok megismerése a Visual Studio Code-ban (előzetes verzió)](visual-studio-code-explore-jobs.md)