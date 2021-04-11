---
title: Azure Data Factory létrehozása Azure Resource Manager sablon használatával (ARM-sablon)
description: Hozzon létre egy minta Azure Data Factory folyamatot egy Azure Resource Manager sablon (ARM-sablon) használatával.
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: c579b6d723533e751e08a80a578195c03e945607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783336"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Rövid útmutató: Azure Data Factory létrehozása ARM-sablonnal

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuális verzió](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy Azure-beli adatelőállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatAzure Data Factory használatával történő **átalakításával** kapcsolatos oktatóanyagért lásd [: oktatóanyag: az adatátalakítás a Spark használatával](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

### <a name="create-a-file"></a>Fájl létrehozása

Nyisson meg egy szövegszerkesztőt, például a **jegyzettömböt**, és hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal:

```emp.txt
John, Doe
Jane, Doe
```

Mentse a fájlt a **C:\ADFv2QuickStartPSH** mappába. (Ha a mappa még nem létezik, hozza létre.)

## <a name="review-template"></a>Sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/) közül származik.

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

A sablonban vannak definiálva Azure-erőforrások:

- [Microsoft. Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): definiál egy Storage-fiókot.
- [Microsoft. DataFactory/gyárak](/azure/templates/microsoft.datafactory/factories): hozzon létre egy Azure Data Factory.
- [Microsoft. DataFactory/gyárak/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): hozzon létre egy Azure Data Factory társított szolgáltatást.
- [Microsoft. DataFactory/gyárak/adatkészletek](/azure/templates/microsoft.datafactory/factories/datasets): hozzon létre egy Azure Data Factory adatkészletet.
- [Microsoft. DataFactory/gyárak/folyamatok](/azure/templates/microsoft.datafactory/factories/pipelines): Azure Data Factory folyamat létrehozása.

További Azure Data Factory-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)katalógusában találhatók.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Data Factory fiókot, egy Storage-fiókot és egy BLOB-tárolót.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Az ADF ARM-sablon üzembe helyezése":::

    Ha meg van adva, a Azure Data Factory erőforrások létrehozásához használja az alapértelmezett értékeket:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**.
    - **Régió**: válasszon helyet.  Például az *USA keleti* régiója.
    - **Data Factory neve**: használja az alapértelmezett értéket.
    - **Hely**: az alapértelmezett érték használata.
    - **Storage-fiók neve**: használja az alapértelmezett értéket.
    - **Blob-tároló**: használja az alapértelmezett értéket.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Válassza **az Ugrás az erőforrás-csoportba** lehetőséget.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Erőforráscsoport":::

2.  Ellenőrizze, hogy létrejött-e a Azure Data Factory.
    1. A Azure Data Factory neve a Format-DataFactory \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Példa Data Factory":::

2. Ellenőrizze, hogy létrejött-e a Storage-fiók.
    1. A Storage-fiók neve formátuma: Storage \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Storage-fiók":::

3. Válassza ki a létrehozott Storage-fiókot, majd válassza a **tárolók** lehetőséget.
    1. A **tárolók** lapon válassza ki a létrehozott BLOB-tárolót.
        1. A blob-tároló neve a-blob formátumban van \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Blobtároló":::

### <a name="upload-a-file"></a>Fájl feltöltése

1. A **tárolók** lapon válassza a **feltöltés** lehetőséget.

2. A jobb oldali ablaktáblán kattintson a **fájlok** mezőre, majd keresse meg és válassza ki a korábban létrehozott **emp.txt** fájlt.

3. Bontsa ki a **speciális** fejlécet.

4. A **feltöltés mappába** mezőbe írja be a *bemenet* értéket.

5. Kattintson a **Feltöltés** gombra. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának.

6. Válassza a **Bezárás** ikont ( **X**) a **blob feltöltése** oldal bezárásához.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Fájl feltöltése a bemeneti mappába":::

Tartsa megnyitva a tároló oldalt, mert ezzel ellenőrizheti a rövid útmutató végén található kimenetet.

### <a name="start-trigger"></a>Trigger elindítása

1. Navigáljon az **adatgyárak** lapra, és válassza ki a létrehozott adatelőállítót. 

2. Válassza ki a **szerző & figyelés** csempét. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="&-figyelő szerzője":::

2. Válassza a **Szerző** fület :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Válassza ki a létrehozott folyamatot – ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM-sablon folyamata":::

4. Válassza az **aktiválási**  >  **trigger hozzáadása most** lehetőséget.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Eseményindító":::

5. A jobb oldali ablaktábla **folyamat futtatása** területén kattintson az **OK gombra**.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Válassza a **figyelés** fület :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. Itt a folyamat futtatásához kapcsolódó tevékenységfuttatások láthatóak. Ebben a rövid útmutatóban a folyamat csak egyetlen tevékenységtípussal rendelkezik: Másolás. Ilyenkor az adott tevékenységhez tartozó Futtatás jelenik meg.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Sikeres Futtatás":::

### <a name="verify-the-output-file"></a>A kimeneti fájl ellenőrzése

A folyamat automatikusan létrehoz egy kimeneti mappát a blob-tárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. 

1. A Azure Portal a **tárolók** lapon kattintson a **frissítés** elemre a kimeneti mappa megtekintéséhez. 

2. Válassza a **kimenet** lehetőséget a mappalistában.

3. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Kimenet":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. [Törölheti az Azure-erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md)is, amely tartalmazza az erőforráscsoport összes erőforrását. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Egy erőforráscsoport törlése a csoportban található összes erőforrást törli, beleértve az adat-előállítókat is. Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Ha csak az adatelőállítót szeretné törölni, és nem a teljes erőforráscsoportot, futtassa a következő parancsot: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Data Factory egy ARM-sablonnal, és ellenőrizte az üzemelő példányt. Ha többet szeretne megtudni a Azure Data Factory és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [Azure Data Factory dokumentáció](index.yml)
- További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
- Más [Azure Data Factory ARM-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular) beszerzése