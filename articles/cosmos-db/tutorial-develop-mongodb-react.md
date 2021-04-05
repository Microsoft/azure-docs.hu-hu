---
title: MongoDB, reagálás és Node.js oktatóanyag az Azure-hoz
description: Ebben a videóalapú oktatóanyag-sorozatban megtudhatja, hogyan hozhat létre MongoDB-alkalmazásokat Azure Cosmos DB adatbázison React és Node.js használatával a MongoDB-hez használt API-kkal.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 1425b89e42450123c1696ddcee4458e1f69b8a6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348568"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>MongoDB-alkalmazás létrehozása a React és az Azure Cosmos DB használatával  
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes videóalapú oktatóanyag bemutatja, hogyan hozhat létre főképkövető alkalmazást React-előtérrendszerrel. Az alkalmazás a-kiszolgálóhoz a Node és a Expresst használta, a [Azure Cosmos db API-MongoDB](mongodb-introduction.md)konfigurált Cosmos-adatbázishoz csatlakozik, majd a reakciót az alkalmazás kiszolgálói részére csatlakoztatja. Az oktatóanyag azt is bemutatja, hogyan végezheti el a Cosmos DB méretezését a Azure Portalban, és hogyan helyezheti üzembe az alkalmazást az interneten, így mindenki nyomon követheti kedvenc hőseit. 

A [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) támogatja a MongoDB-kompatibilitást, és lehetővé teszi, hogy az ügyfelek a Azure Cosmos DBT használják a MongoDB helyett.  

Ez a többrészes oktatóanyag a következő feladatokon vezet végig:

> [!div class="checklist"]
> * Bevezetés
> * A projekt beállítása
> * A felhasználói felület létrehozása a React használatával
> * Azure Cosmos DB-fiók létrehozása az Azure Portal használatával
> * A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz
> * React-, létrehozási, frissítési és törlési műveletek hozzáadása az alkalmazáshoz

Ugyanezt az alkalmazást szeretné felépíteni az Angular használatával? Lásd az [Angular videós oktatóanyag-sorozatot](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Előfeltételek
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Befejezett projekt
Töltse le a befejezett alkalmazást a [GitHubról](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Bevezetés 

Ebben a videóban Burke Holland bevezeti az Azure Cosmos DB használatába, és végigvezeti a videósorozat során létrehozott alkalmazáson. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Projekt beállítása

A videó bemutatja, hogyan állítható be az Express és a React ugyanabban a projektben. Burke ezután bemutatja a projekt kódját az elejétől a végéig.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>A felhasználói felület létrehozása

A videó bemutatja, hogyan lehet létrehozni az alkalmazás felhasználói felületét (UI) a React használatával. 

> [!NOTE]
> A videóban említett CSS a [react-cosmosdb GitHub-adattárból](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css) tölthető le.

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Csatlakozás az Azure Cosmos DB-hez

Ez a videó bemutatja, hogyan hozhat létre egy Azure Cosmos DB-fiókot az Azure Portal használatával, hogyan telepítheti a MongoDB- és Mongoose-csomagokat, és hogyan csatlakoztathatja az újonnan létrehozott fiókot az Azure Cosmos DB kapcsolati sztring használatával. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Fő képek olvasása és létrehozása az alkalmazásban

Ebből a videóból megtudhatja, hogyan olvashatja el a hősöket, és hogyan hozhat létre a Cosmos adatbázisban, valamint hogyan tesztelheti ezeket a metódusokat a Poster és a felhasználói felület megválaszolása használatával. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Fő képek törlése és frissítése az alkalmazásban

Ez a videó bemutatja, hogyan törölhetők és frissíthetők a fő képek az alkalmazásban, és hogyan jeleníthetők meg a frissítések a felhasználói felületen. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Az alkalmazás befejezése

Ez a videó bemutatja, hogyan fejezhető be az alkalmazás, és hogyan csatlakoztatható végül a felhasználói felület a háttérrendszeri API-hoz. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon. 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Alkalmazás létrehozása a React, a Node, az Express és az Azure Cosmos DB használatával 
> * Azure Cosmos DB-fiók létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB-fiókhoz
> * Az alkalmazás tesztelése a Postman segítségével
> * Az alkalmazás futtatása, és fő képek felvétele az adatbázisba

Továbbléphet a következő oktatóanyagra, amelyben megismerheti a MongoDB-adatok az Azure Cosmos DB-be való importálásának módját.  

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
