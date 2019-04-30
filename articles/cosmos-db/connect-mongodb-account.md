---
title: Egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása
description: Ismerje meg, a MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 737e179c2c16937d00bc9b6601f12ebe392c1906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892520"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása
Ismerje meg, a MongoDB-alkalmazás csatlakoztatása egy Azure Cosmos DB MongoDB-kapcsolati karakterlánc használatával. Ezután használhatja az Azure Cosmos DB-adatbázis az adatokat, a MongoDB-alkalmazást az áruházból. 

Ebben az oktatóanyagban a kapcsolati sztring adatainak lekéréséhez két lehetőséget biztosít:

- [A rövid útmutató metódus](#QuickstartConnection), .NET, Node.js, a MongoDB Shell, Java és Python-illesztőprogramok való használatra
- [Az egyéni kapcsolati karakterlánc mód](#GetCustomConnection), más illesztőprogramok való használatra

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik Azure-fiókkal, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) most. 
- Cosmos-fiók. Útmutatásért lásd: [-webalkalmazás létrehozása Azure Cosmos DB API a mongodb-hez és a .NET SDK használatával](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>A gyors üzembe helyezési használatával a MongoDB-kapcsolati sztring lekérése
1. Egy böngészőből, jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API-t. 
3. A fiók panel bal oldali panelen kattintson a **gyors üzembe helyezési**. 
4. Válassza ki a platformot (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Ha nem látja, hogy az illesztőprogram vagy eszköz szerepel a listán, ne aggódjon – a Microsoft folyamatosan a dokumentum további kapcsolat-kódtöredékek. Várjuk a megjegyzéseket alább az Önnek tetszőt megtekintéséhez. Ismerje meg, hogyan írhat saját kapcsolatot, olvassa el [a fiók kapcsolati sztringek adatainak lekérése](#GetCustomConnection).
5. Másolja és illessze be a kódrészletet a MongoDB-alkalmazást.

    ![Gyors üzembe helyezési panel](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Testreszabhatja a MongoDB kapcsolati sztring lekérése
1. Egy böngészőből, jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API-t. 
3. A fiók panel bal oldali panelen kattintson a **kapcsolati karakterlánc**. 
4. A **kapcsolati karakterlánc** panel nyílik meg. Egy illesztőprogram a mongodb-hez, beleértve a preconstructed kapcsolati karakterlánc használatával kapcsolódni a fiókjához szükséges összes információt tartalmaz.

    ![Kapcsolati sztring panel](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Kapcsolati karakterlánc követelmények
> [!Important]
> Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Az Azure Cosmos DB-fiókokhoz hitelesítés és a biztonságos kommunikáció keresztül szükséges *SSL*. 
>
>

Az Azure Cosmos DB támogatja a standard szintű MongoDB kapcsolati URI karakterláncként, van azonban néhány konkrét követelmények: Az Azure Cosmos DB-fiókokhoz hitelesítés és biztonságos SSL-kommunikációra van szükség. Így a kapcsolati karakterlánc formátuma a következő:

    mongodb://username:password@host:port/[database]?ssl=true

Ez a karakterlánc értékét érhetők el a **kapcsolati karakterlánc** korábban bemutatott panelen:

* Username (kötelező): Cosmos-fióknév.
* Jelszó (kötelező): Cosmos-fiók jelszava.
* A gazdagép (kötelező): A Cosmos-fiók teljes Tartományneve.
* Port (kötelező): 10255.
* Az adatbázis (nem kötelező): Az adatbázis, amely a kapcsolatot használ. Ha nincs adatbázis van megadva, az alapértelmezett adatbázis-e a "teszt".
* ssl=true (required)

Vegyük példaként a fiók látható a **kapcsolati karakterlánc** panelen. Egy érvényes kapcsolati karakterláncot a következő:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerje meg, hogyan [Robo 3T használata](mongodb-robomongo.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.
