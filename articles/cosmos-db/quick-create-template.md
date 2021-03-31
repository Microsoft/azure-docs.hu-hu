---
title: Rövid útmutató – Azure Cosmos DB és tároló létrehozása Azure Resource Manager sablon használatával
description: Útmutató az Azure Cosmos-adatbázisok és-tárolók Azure Resource Manager sablon használatával történő használatáról
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339665"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Rövid útmutató: Azure Cosmos DB és tároló létrehozása ARM-sablonnal
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB a Microsoft gyors NoSQL-adatbázisa, amely bármilyen méretű nyitott API-val rendelkezik. A Azure Cosmos DB a kulcs/érték adatbázisok, dokumentum-adatbázisok és Graph-adatbázisok gyors létrehozásához és lekérdezéséhez használható. Ez a rövid útmutató egy Azure Resource Manager sablon (ARM-sablon) üzembe helyezésének folyamatát tárgyalja egy Azure Cosmos-adatbázis létrehozásához és az adatbázison belüli tároló létrehozásához. A tárolóban később tárolhatók az adattárolók.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbaverziós fiók

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) közül származik.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Három Azure-erőforrás van definiálva a sablonban:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): hozzon létre egy Azure Cosmos-fiókot.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): hozzon létre egy Azure Cosmos-adatbázist.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): hozzon létre egy Azure Cosmos-tárolót.

További Azure Cosmos DB-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)katalógusában találhatók.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="ARM-sablon, Azure Cosmos DB integráció, portál üzembe helyezése":::

    Ha meg van adva, használja az alapértelmezett értékeket az Azure Cosmos-erőforrások létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
    * **Hely**: válasszon ki egy helyet.  Például: **USA középső régiója**.
    * **Fiók neve**: adja meg az Azure Cosmos-fiók nevét. Globálisan egyedinek kell lennie.
    * **Hely**: adjon meg egy helyet, ahol létre szeretné hozni az Azure Cosmos-fiókot. Az Azure Cosmos-fiók ugyanazon a helyen lehet, mint az erőforráscsoport.
    * **Elsődleges régió**: az Azure Cosmos-fiók elsődleges replika régiója.
    * **Másodlagos régió**: az Azure Cosmos-fiók másodlagos replika régiója.
    * **Alapértelmezett konzisztencia-szint**: az Azure Cosmos-fiók alapértelmezett konzisztencia-szintje.
    * **Maximális elavultság előtagja**: az elavult kérelmek maximális száma. A BoundedStaleness szükséges.
    * **Maximális időköz (másodperc**): maximális késési idő. A BoundedStaleness szükséges.
    * **Adatbázis neve**: az Azure Cosmos-adatbázis neve.
    * **Tároló neve**: az Azure Cosmos-tároló neve.
    * **Átviteli sebesség**: a tároló átviteli sebessége, a minimális átviteli sebesség 400 ru/s.
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.

3. Válassza a **Beszerzés** lehetőséget. Miután sikeresen telepítette az Azure Cosmos-fiókot, értesítést kap:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="ARM-sablon, Cosmos DB integráció, portál-értesítés üzembe helyezése":::

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A Azure Portal használatával megtekintheti az Azure Cosmos-fiókot, az adatbázist és a tárolót, vagy a következő Azure CLI-vagy Azure PowerShell-szkripttel listázhatja a létrehozott titkos kulcsot.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, érdemes lehet ezeket az erőforrásokat helyben hagyni.
Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az Azure Cosmos-fiókot és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót egy ARM-sablon használatával, és ellenőrizte az üzemelő példányt. Ha többet szeretne megtudni a Azure Cosmos DB és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [A Azure Cosmos db áttekintése](introduction.md)
- További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
- Más [Azure Cosmos db Resource Manager-sablonok](./templates-samples-sql.md) beszerzése