---
title: A Azure Cosmos DB API Mongo-verziójának frissítése a MongoDB-fiókhoz
description: A meglévő Azure Cosmos DB API-k MongoDB-protokoll-verziójának frissítése zökkenőmentesen a MongoDB-fiókokhoz
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661171"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>A MongoDB-fiókhoz tartozó Azure Cosmos DB API API-verziójának frissítése
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk a Azure Cosmos DB API-MongoDB-fiók API-verziójának frissítését ismerteti. A frissítés után a Azure Cosmos DB API-MongoDB elérhető legújabb funkciókat használhatja. A frissítési folyamat nem szakítja meg a fiók rendelkezésre állását, és nem használja fel RU/s-t, vagy nem csökkenti az adatbázis kapacitását. Ez a folyamat nem érinti a meglévő és az indexeket. 

Új API-verzióra való frissítéskor a termelési feladatok frissítése előtt kezdjen fejlesztési/tesztelési feladatokkal. Fontos, hogy az ügyfeleket az API-verzióval kompatibilis verzióra frissítse, mielőtt frissítené a Azure Cosmos DB API-t a MongoDB-fiókhoz.

>[!Note]
> Jelenleg csak a 3,2-es verziójú kiszolgálóval rendelkező fiókokat lehet a 3,6-es vagy 4,0-os verzióra frissíteni. Ha a fiókja nem jeleníti meg a frissítési lehetőséget, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Frissítés 4,0-re vagy 3,6-ra

### <a name="benefits-of-upgrading-to-version-40"></a>Az 4,0-es verzióra való frissítés előnyei

A 4,0-es verzióban található új funkciók a következők:
- Többdokumentumos tranzakciók támogatása a nem horizontális gyűjteményeknél.
- Új aggregációs operátorok
- Továbbfejlesztett vizsgálati teljesítmény
- Gyorsabb, hatékonyabb tárolás

### <a name="benefits-of-upgrading-to-version-36"></a>A 3.6-os verzióra való frissítés előnyei

A 3,6-es verzióban található új funkciók a következők:
- Jobb teljesítmény és stabilitás
- Új adatbázisparancsok támogatása
- Összesítési folyamat támogatása alapértelmezés szerint és új összesítési szakaszok
- Streamek módosításának támogatása
- Összetett indexek támogatása
- Több partícióra kiterjedő támogatás a következő műveletekhez: frissítés, törlés, darabszám és rendezés
- Jobb teljesítmény a következő összesítési műveletek esetében: $count, $skip, $limit és $group
- Mostantól támogatott a helyettesítő karakterek indexelése

### <a name="changes-from-version-32"></a>Az 3,2-es verzió változásai

- Alapértelmezés szerint a [kiszolgálóoldali újrapróbálkozás (SSR)](prevent-rate-limiting-errors.md) funkció engedélyezve van, így az ügyfélalkalmazás kérelmei nem adnak vissza 16500 hibát. Ehelyett a kérelmek addig folytatódnak, amíg befejeződik, vagy elérik a 60 második időtúllépést.
- Az egyes kérések időkorlátja 60 másodperc.
- Az új vonalprotokoll-verzióval létrehozott MongoDB-gyűjteményekhez alapértelmezés szerint csak az `_id` tulajdonság lesz indexelve.

### <a name="action-required-when-upgrading-from-32"></a>A 3,2-es verzióra való frissítéskor szükséges művelet

Az 3,2-es verzióra való frissítéskor a rendszer az adatbázis-fiók végpontjának utótagját a következő formátumra frissíti:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Ha a 3,2-es verzióról frissít, akkor a meglévő végpontot le kell cserélnie az alkalmazásokban és az adatbázis-fiókkal csatlakozó illesztőprogramokban. **Csak az új végpontot használó kapcsolatok férhetnek hozzá a szolgáltatásokhoz az új API-verzióban**. Az előző 3,2 végpontnak utótaggal kell rendelkeznie `.documents.azure.com` .

>[!Note]
> Ez a végpont némileg eltérő lehet, ha a fiókja egy szuverén, kormányzati vagy korlátozott Azure-felhőben lett létrehozva.

## <a name="how-to-upgrade"></a>A frissítés módja

1. Nyissa meg a Azure Portalt, és navigáljon a MongoDB-fiók áttekintés paneljének Azure Cosmos DB API-hoz. Ellenőrizze, hogy az aktuális kiszolgáló verziója várható-e.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure Portal MongoDB-fiókkal – áttekintés" border="false":::

2. A bal oldali beállítások közül válassza ki a panelt `Features` . Ez az adatbázis-fiókhoz elérhető fiók szintű funkciókat mutatja be.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal MongoDB-fiókkal – áttekintés a funkciók panel kiemelésével" border="false":::

3. Kattintson a `Upgrade Mongo server version` sorra. Ha nem látja ezt a lehetőséget, előfordulhat, hogy a fiókja nem jogosult erre a frissítésre. Ha ez a helyzet, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Funkciók panel a beállításokkal." border="false":::

4. Tekintse át a frissítéssel kapcsolatban megjelenő információkat. Kattintson a gombra, amint `Enable` készen áll a folyamat elindítására.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Bővített frissítési útmutató." border="false":::

5. A folyamat elindítása után a `Features` menü megjeleníti a frissítés állapotát. Az állapot a, a `Pending` és a `In Progress` értékre fog esni `Upgraded` . Ez a folyamat nem befolyásolja az adatbázis-fiók meglévő funkcióit vagy műveleteit.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="A frissítés állapota az indítás után." border="false":::

6. A frissítés befejezése után az állapot a következő lesz: `Upgraded` . Kattintson ide, ha többet szeretne megtudni a folyamat véglegesítéséhez szükséges következő lépésekről és műveletekről. Ha hiba történt a kérelem feldolgozása során, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/en-us/support/create-ticket/) .

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="A fiók frissített állapota." border="false":::

7. 
    1. Ha 3,2-ról frissített, térjen vissza a panelre `Overview` , és másolja az alkalmazásban használandó új kapcsolódási karakterláncot. Az 3,2-et futtató régi kapcsolatok karakterlánca nem fog megszakadni. Az egységes élmény biztosítása érdekében minden alkalmazásnak az új végpontot kell használnia.
    2. Ha 3,6-ről frissített, a meglévő kapcsolódási sztring a megadott verzióra lesz frissítve, és továbbra is használatban kell lennie.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Új áttekintés panel." border="false":::


## <a name="how-to-downgrade"></a>Visszalépés
A fiókját a 4,0 – 3,6-as verzióra is leértékelheti a "hogyan frissíthet" szakaszban ismertetett lépések segítségével. 

Ha 3,2-ről (4,0 vagy 3,6) frissítette a frissítést, és vissza szeretne térni a 3,2-re, egyszerűen visszaválthat az előző (3,2) kapcsolódási karakterlánc használatára a gazdagépen, `accountname.documents.azure.com` amely továbbra is aktív frissítést követően fut az 3,2-os verzióban.


## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg az MongoDB 4,0-es [verziójának](mongodb-feature-support-40.md)támogatott és nem támogatott szolgáltatásaival.
- Ismerkedjen meg az MongoDB 3,6-es [verziójának](mongodb-feature-support-36.md)támogatott és nem támogatott szolgáltatásaival.
- További információ: [Mongo 3,6 version features](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
