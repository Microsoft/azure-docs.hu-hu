---
title: A Studio 3T használata Azure Cosmos DB API-MongoDB való kapcsolódáshoz
description: Megtudhatja, hogyan csatlakozhat a MongoDB Azure Cosmos DB API-hoz a Studio 3T használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 6c9ea401a4a62f617b5acf6f96c60532d929ff1c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167967"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Kapcsolódás Azure Cosmos-fiókhoz a Studio 3T használatával

Ha a Studio 3T használatával szeretne csatlakozni egy Azure Cosmos DB API-hoz a MongoDB-hez, a következőket kell tennie:

* Töltse le és telepítse a [Studio 3T](https://studio3t.com/).
* Adja meg az Azure Cosmos-fiókja a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információkat.

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolatok létrehozása a Studio 3T

Az alábbi lépéseket követve adhatja hozzá az Azure Cosmos-fiókot a Studio 3T-Csatlakozáskezelőhöz:

1. Kérje le a Azure Cosmos DB API-MongoDB-fiókjának kapcsolati információit a MongoDB- [alkalmazás csatlakoztatása a Azure Cosmos db](connect-mongodb-account.md) cikk utasításait követve.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::

2. Kattintson a **Kapcsolódás** gombra a Csatlakozáskezelő megnyitásához, majd kattintson az **új kapcsolat** elemre.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
3. Az **új csatlakozás** ablak **kiszolgáló** lapján adja meg az Azure Cosmos-fiók gazdagépét (FQDN) és a portot.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
4. Az **új kapcsolatok** ablakban a **hitelesítés** lapon válassza az **alapszintű hitelesítési mód (MONGODB-CR vagy SCARM-SHA-1)** lehetőséget, és adja meg a felhasználónevet és a jelszót.  Fogadja el az alapértelmezett hitelesítési adatbázist (rendszergazda), vagy adja meg a saját értékét.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
5. Az **új kapcsolat** ablak **SSL** LAPJÁN jelölje be az **SSL protokoll használata a kapcsolódáshoz** jelölőnégyzetet, valamint a **kiszolgáló önaláírt SSL-tanúsítványok fogadása** választógombot.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
6. Kattintson a **Kapcsolódás tesztelése** gombra a kapcsolódási adatok érvényesítéséhez, kattintson az **OK** gombra az új kapcsolódási ablakhoz való visszatéréshez, majd kattintson a **Mentés**gombra.

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Adatbázis, gyűjtemény és dokumentumok létrehozása a Studio 3T használatával
Adatbázis, gyűjtemény és dokumentumok létrehozásához a Studio 3T használatával hajtsa végre a következő lépéseket:

1. A **Csatlakozáskezelő**területen jelölje ki a kapcsolatot, majd kattintson a **Csatlakoztatás**gombra.

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
2. Kattintson a jobb gombbal a gazdagépre, majd válassza az **adatbázis hozzáadása**elemet.  Adja meg az adatbázis nevét, majd kattintson **az OK gombra**.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
3. Kattintson a jobb gombbal az adatbázisra, és válassza a **gyűjtemény hozzáadása**elemet.  Adja meg a gyűjtemény nevét, és kattintson a **Létrehozás**gombra.

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::
4. Kattintson a **gyűjtemény** menüpontra, majd a **dokumentum hozzáadása**elemre.

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról" vezetéknevű családot, és adja vissza a szülők és az állam mezőket.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
