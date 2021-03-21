---
title: A Robo 3T használata a Azure Cosmos DBhoz való kapcsolódáshoz
description: Ismerje meg, hogyan csatlakozhat a Azure Cosmos DB a Robo 3T és a Azure Cosmos DB API-MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096410"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Robo 3T használata a MongoDB-hez készült Azure Cosmos DB API-hoz
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ha a Robo 3T használatával szeretne csatlakozni a Cosmos-fiókhoz, a következőket kell tennie:

* A [Robo 3T](https://robomongo.org/) letöltése és telepítése
* A Cosmos DB a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információk

> [!NOTE]
> Jelenleg a Robo 3T v 1.2 és az alacsonyabb verziók a Cosmos DB API-ját támogatják a MongoDB.

## <a name="connect-using-robo-3t"></a>Csatlakozás a Robo 3T használatával

Az alábbi lépések végrehajtásával adhatja hozzá Cosmos-fiókját a Robo 3T-Csatlakozáskezelőhöz:

1. A Azure Cosmos DB API-MongoDB konfigurált Cosmos-fiókhoz tartozó kapcsolatok adatainak beolvasása az [itt](connect-mongodb-account.md)leírt utasítások alapján.

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Képernyőfelvétel a kapcsolatok sztring panelről":::
2. Futtassa a *Robomongo* alkalmazást.

3. Kattintson a kapcsolat gombra a **fájl** területen a kapcsolatok kezeléséhez. Ezután kattintson a **Létrehozás** elemre a **MongoDB-kapcsolatok** ablakban, amely megnyitja a **kapcsolati beállítások** ablakot.

4. A **kapcsolatbeállítások** ablakban válasszon egy nevet. Ezután keresse meg a **gazdagépet** és a **portot** az 1. lépésben megadott elérhetőségi adatokból, és adja meg őket a **címe** és a **port** mezőbe.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Képernyőkép a Robomongo kapcsolatok kezeléséről":::
5. A **hitelesítés** lapon kattintson a **hitelesítés elvégzése** lehetőségre. Ezután adja meg az adatbázisát (az alapértelmezett *rendszergazda*), a **felhasználónevet** és a **jelszót**.
Az 1. lépésben a **Felhasználónév** és a **jelszó** is megtalálható a kapcsolatok adatai között.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Képernyőkép a Robomongo-hitelesítés lapról":::
6. Az **SSL** lapon jelölje be az **SSL protokoll használata** lehetőséget, majd módosítsa a **hitelesítési módszert** **önaláírt tanúsítványra**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="A Robomongo SSL lapjának képernyőképe":::
7. Végül kattintson a **test (tesztelés** ) elemre annak ellenőrzéséhez, hogy tud-e csatlakozni, majd **mentse**.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
