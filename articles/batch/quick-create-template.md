---
title: Azure rövid útmutató – batch-fiók létrehozása – Azure Resource Manager sablon
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy batch-fiókot ARM-sablon használatával.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: subject-armqs
ms.openlocfilehash: fc6f1537b32c8ced2cfc2bf09f16106f791001fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106580"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Rövid útmutató: batch-fiók létrehozása ARM-sablon használatával

A számítási erőforrások (számítási csomópontok készletei) és a Batch-feladatok létrehozásához batch-fiók szükséges. Létrehozhat egy Azure Storage-fiókot a Batch-fiókjával, amely hasznos az alkalmazások üzembe helyezéséhez és a bemeneti és kimeneti adatok tárolásához a legtöbb valós számítási feladathoz. Ez a rövid útmutató bemutatja, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy batch-fiók, például a Storage létrehozásához. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Aktív Azure-előfizetéssel kell rendelkeznie.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/) közül származik.

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): létrehoz egy Storage-fiókot.
- [Microsoft.BatCH/batchAccounts](/azure/templates/microsoft.batch/batchaccounts): létrehoz egy batch-fiókot.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Batch fiókot és egy Storage-fiókot.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

   ![Resource Manager-sablon, batch-fiók létrehozása, portál üzembe helyezése](media/quick-create-template/batch-template.png)

   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
   - **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
   - **Batch-fiók neve**: hagyja meg az alapértelmezett értéket.
   - **Tárolási Accountsku**: válassza ki a Storage-fiók típusát. Például **Standard_LRS**.
   - **Hely**: hagyja meg az alapértelmezett értéket, hogy az erőforrások ugyanabban a helyen legyenek, mint az erőforráscsoport.
   - Elfogadom a fenti feltételeket és kikötéseket: **Select**.

1. Válassza a **Beszerzés** lehetőséget.

Néhány perc elteltével megjelenik egy értesítés arról, hogy a Batch-fiók létrehozása sikeresen megtörtént.

Ebben a példában a sablon telepítéséhez a Azure Portal használjuk. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítést a létrehozott erőforráscsoporthoz való navigálással ellenőrizheti Azure Portal. Az **Áttekintés** képernyőn ellenőrizze, hogy a Batch-fiók és a Storage-fiók található-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő [oktatóanyagokkal](./tutorial-parallel-dotnet.md)dolgozik tovább, akkor előfordulhat, hogy ezeket az erőforrásokat helyben szeretné hagyni. Ha már nincs szüksége rájuk, [törölheti az erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)is, amely törli a Batch-fiókot és a létrehozott Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy batch-fiókot és egy Storage-fiókot. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat.

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)
