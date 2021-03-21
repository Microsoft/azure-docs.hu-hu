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
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096512"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Kapcsolódás Azure Cosmos-fiókhoz a Studio 3T használatával
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ha a Studio 3T használatával szeretne csatlakozni egy Azure Cosmos DB API-hoz a MongoDB-hez, a következőket kell tennie:

* Töltse le és telepítse a [Studio 3T](https://studio3t.com/).
* Adja meg az Azure Cosmos-fiókja a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információkat.

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolatok létrehozása a Studio 3T

Az alábbi lépéseket követve adhatja hozzá az Azure Cosmos-fiókot a Studio 3T-Csatlakozáskezelőhöz:

1. Kérje le a Azure Cosmos DB API-MongoDB-fiókjának kapcsolati információit a MongoDB- [alkalmazás csatlakoztatása a Azure Cosmos db](connect-mongodb-account.md) cikk utasításait követve.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Képernyőfelvétel a kapcsolatok karakterláncának oldaláról":::

2. Kattintson a **Kapcsolódás** gombra a Csatlakozáskezelő megnyitásához, majd kattintson az **új kapcsolat** elemre.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Képernyőkép a Studio 3T Csatlakozáskezelő eszközről, amely kiemeli az új kapcsolatok gombot.":::
3. Az **új csatlakozás** ablak **kiszolgáló** lapján adja meg az Azure Cosmos-fiók gazdagépét (FQDN) és a portot.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="A Studio 3T Csatlakozáskezelő kiszolgáló lapjának képernyőképe":::
4. Az **új kapcsolatok** ablakban a **hitelesítés** lapon válassza az **alapszintű hitelesítési mód (MONGODB-CR vagy SCARM-SHA-1)** lehetőséget, és adja meg a felhasználónevet és a jelszót.  Fogadja el az alapértelmezett hitelesítési adatbázist (rendszergazda), vagy adja meg a saját értékét.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="A Studio 3T Csatlakozáskezelő hitelesítés lapjának képernyőképe":::
5. Az **új kapcsolat** ablak **SSL** LAPJÁN jelölje be az **SSL protokoll használata a kapcsolódáshoz** jelölőnégyzetet, valamint a **kiszolgáló önaláírt SSL-tanúsítványok fogadása** választógombot.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Képernyőfelvétel a Studio 3T-Csatlakozáskezelő SSL-lapjáról":::
6. Kattintson a **Kapcsolódás tesztelése** gombra a kapcsolódási adatok érvényesítéséhez, kattintson az **OK** gombra az új kapcsolódási ablakhoz való visszatéréshez, majd kattintson a **Mentés** gombra.

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Képernyőfelvétel a Studio 3T-tesztelési kapcsolatok ablakáról":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Adatbázis, gyűjtemény és dokumentumok létrehozása a Studio 3T használatával
Adatbázis, gyűjtemény és dokumentumok létrehozásához a Studio 3T használatával hajtsa végre a következő lépéseket:

1. A **Csatlakozáskezelő** területen jelölje ki a kapcsolatot, majd kattintson a **Csatlakoztatás** gombra.

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="A Studio 3T-Csatlakozáskezelő képernyőképe":::
2. Kattintson a jobb gombbal a gazdagépre, majd válassza az **adatbázis hozzáadása** elemet.  Adja meg az adatbázis nevét, majd kattintson **az OK gombra**.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Képernyőfelvétel a Studio 3T adatbázis hozzáadása lehetőségéről":::
3. Kattintson a jobb gombbal az adatbázisra, és válassza a **gyűjtemény hozzáadása** elemet.  Adja meg a gyűjtemény nevét, és kattintson a **Létrehozás** gombra.

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Képernyőfelvétel a Studio 3T-gyűjtemény hozzáadása lehetőségről":::
4. Kattintson a **gyűjtemény** menüpontra, majd a **dokumentum hozzáadása** elemre.

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Képernyőfelvétel a Studio 3T hozzáadása dokumentum menüelem":::
5. A dokumentum hozzáadása párbeszédpanelen illessze be a következőt, majd kattintson a **dokumentum hozzáadása** lehetőségre.

    ```json
    {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
            {
                "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
                "pets": [{ "givenName": "Fluffy" }]
            }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. Adjon hozzá egy másik dokumentumot, ezúttal a következő tartalommal:

    ```json
    {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                "givenName": "Lisa",
                "gender": "female",
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }
    ```

7. Minta lekérdezés végrehajtása. Keressen például egy "Andersen" vezetéknevű családot, és adja vissza a szülők és az állam mezőket.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Képernyőkép a Mongo Chef lekérdezési eredményeiről":::

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
