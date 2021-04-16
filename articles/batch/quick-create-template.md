---
title: Azure rövid útmutató – Batch-fiók létrehozása – Azure Resource Manager sablon
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Batch-fiókot ARM-sablonnal.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: e3405e98e2753a308b6b34b392aa364a67f7fe5b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535591"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Rövid útmutató: Batch-fiók létrehozása ARM-sablonnal

Számítási erőforrások (számítási csomópontok készlete) és Batch-feladatok létrehozásához Batch-fiók szükséges. Összekapcsolhat egy Azure Storage-fiókot a Batch-fiókkal, ami hasznos lehet az alkalmazások üzembe helyezéséhez és a legtöbb valós számítási feladat bemeneti és kimeneti adatainak tárolásához. Ez a rövid útmutató bemutatja, hogyan használhatja Azure Resource Manager sablont (ARM-sablont) Batch-fiók létrehozásához, beleértve a tárolót is. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Aktív Azure-előfizetéssel kell lennie.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/) közül származik.

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

A sablonban két Azure-erőforrás van definiálva:

- [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)Létrehoz egy tárfiókot.
- [Microsoft.Batch/batchAccounts:](/azure/templates/microsoft.batch/batchaccounts)Létrehoz egy Batch-fiókot.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Batch fiókot és egy tárfiókot.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

   ![Resource Manager sablon, Batch-fiók létrehozása, portál üzembe helyezése](media/quick-create-template/batch-template.png)

   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport:** válassza az **Új létrehozása** lehetőséget, adjon egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.**
   - **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
   - **Batch-fiók neve:** Hagyja meg az alapértelmezett értéket.
   - **Tárfiókokku:** válassza ki a tárfiók típusát. Például: **Standard_LRS.**
   - **Hely:** Hagyja meg az alapértelmezett értéket, hogy az erőforrások az erőforráscsoporttal azonos helyen lesznek.
   - Elfogadom a fenti feltételeket és feltételeket: **Válassza a lehetőséget.**

1. Válassza a **Beszerzés** lehetőséget.

Néhány perc múlva megjelenik egy értesítés arról, hogy a Batch-fiók sikeresen létrejött.

Ebben a példában a Azure Portal a sablon üzembe helyezéséhez. A Azure Portal mellett használhatja a Azure PowerShell, az Azure CLI-t és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést a Azure Portal a létrehozott erőforráscsoportra navigálva ellenőrizheti. Az Áttekintés **képernyőn** ellenőrizze, hogy a Batch-fiók és a tárfiók megjelenik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik [tovább,](./tutorial-parallel-dotnet.md)akkor ezeket az erőforrásokat a helyén hagyhatja. Ha már nincs rájuk szüksége, [](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)törölheti a erőforráscsoportot, amely a Batch-fiókot és a létrehozott tárfiókot is törli.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Batch-fiókot és egy tárfiókot. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat.

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)
