---
title: Angular-alkalmazás létrehozása Azure Cosmos DB MongoDB API-jának használatával (1. rész)
description: A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 4. része
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js, devx-track-azurecli
ms.reviewer: sngun
ms.openlocfilehash: 99edeff93bdf75596eea80a238e159548f00679c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790220"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Angular-alkalmazás létrehozása Azure Cosmos DB MongoDB API-jának használatával – Cosmos-fiók létrehozása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre az Node.js-ban írt új alkalmazást az Express és az Angular használatával, majd hogyan csatlakoztathatja azt az Cosmos DB API-val konfigurált Cosmos-fiókjához a [MongoDB-hez.](mongodb-introduction.md)

Az oktatóanyag 4. része a [3. részre](tutorial-develop-mongodb-nodejs-part3.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Azure-erőforráscsoport létrehozása az Azure CLI használatával
> * Cosmos-fiók létrehozása az Azure CLI használatával

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [3. részében](tutorial-develop-mongodb-nodejs-part3.md) ismertetett lépéseket. 

Az oktatóanyag ezen szakaszában az Azure Cloud Shellt (a webböngészőben) vagy a helyileg telepített [Azure CLI-t](/cli/azure/install-azure-cli) eszközt használhatja.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB fiókot az [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) paranccsal.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* `<cosmosdb-name>` esetén használja a saját egyedi Azure Cosmos DB-fióknevét, a névnek egyedinek kell lennie az összes Azure-beli Azure Cosmos DB-fióknév között.
* A `--kind MongoDB` beállítás lehetővé teszi, hogy az Azure Cosmos DB MongoDB-ügyfélkapcsolatokkal rendelkezzen.

A parancs végrehajtása egy-két percet is igénybe vehet. Amint befejeződött, a terminálablakban megjelennek az új adatbázissal kapcsolatos információk. 

Amint az Azure Cosmos DB-fiók létrejött:
1. Nyisson meg egy új böngészőablakot, és nyissa meg a [https://portal.azure.com](https://portal.azure.com)
1. Kattintson a Azure Cosmos DB az emblémára a bal oldali sávon, és megjelenik az összes :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png"::: Azure Cosmos-fájl.
1. Kattintson az imént létrehozott Azure Cosmos DB-fiókra, válassza az **Overview** (Áttekintés) lapot, és görgessen le a térképig, amelyen az adatbázis található. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="Az Azure Cosmos D B-fiók áttekintését bemutató képernyőkép.":::

4. Görgessen le a bal oldali navigációs sávon, majd kattintson az **Replicate data globally** (Adatok globális replikálása) lapra. Ezzel megjelenít egy térképet, amelyen azokat a különböző területeket láthatja, ahová replikálhat. Például Délkelet-Ausztráliára vagy Kelet-Ausztráliára kattintva az adatokat Ausztráliába replikálhatja. A globális replikációval kapcsolatos további információkért lásd: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md). Egyelőre azonban elég lesz egyetlen példány, de majd ha replikálni szeretnénk, már tudjuk, hogyan kell.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="Képernyőkép egy Azure Cosmos D B-fiókról, globálisan kijelölt Adatok replikálása lehetőségével.":::

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Azure-erőforráscsoportot az Azure CLI használatával
> * Létrehozott egy Azure Cosmos DB-fiókot az Azure CLI használatával

Továbbléphet az oktatóanyag következő részére, amelyben az Azure Cosmos DB-adatbázist az alkalmazásához kapcsolja a Mongoose használatával.

> [!div class="nextstepaction"]
> [A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz](tutorial-develop-mongodb-nodejs-part5.md)