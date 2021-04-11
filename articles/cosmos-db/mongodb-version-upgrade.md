---
title: A Azure Cosmos DB API Mongo-verziójának frissítése a MongoDB-fiókhoz
description: A meglévő Azure Cosmos DB API-k MongoDB-protokoll-verziójának frissítése zökkenőmentesen a MongoDB-fiókokhoz
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771809"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>A MongoDB-fiókhoz tartozó Azure Cosmos DB API API-verziójának frissítése
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk a Azure Cosmos DB API-MongoDB-fiók API-verziójának frissítését ismerteti. A frissítés után a Azure Cosmos DB API-MongoDB elérhető legújabb funkciókat használhatja. A frissítési folyamat nem szakítja meg a fiók rendelkezésre állását, és nem használja fel RU/s-t, vagy nem csökkenti az adatbázis kapacitását. Ez a folyamat nem érinti a meglévő és az indexeket. 

Új API-verzióra való frissítéskor a termelési feladatok frissítése előtt kezdjen fejlesztési/tesztelési feladatokkal. Fontos, hogy az ügyfeleket az API-verzióval kompatibilis verzióra frissítse, mielőtt frissítené a Azure Cosmos DB API-t a MongoDB-fiókhoz.

>[!Note]
> Jelenleg csak a 3,2-es verziójú kiszolgálóval rendelkező fiókokat lehet a 3,6-es vagy 4,0-os verzióra frissíteni. Ha a fiókja nem jeleníti meg a frissítési lehetőséget, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Frissítés a 4.0-s vagy 3.6-os verzióra

### <a name="benefits-of-upgrading-to-version-40"></a>A 4.0-s verzióra való frissítés előnyei

A 4,0-es verzióban található új funkciók a következők:
- Többdokumentumos tranzakciók támogatása a nem horizontális gyűjteményeknél.
- Új aggregációs operátorok
- Jobb vizsgálati teljesítmény
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

### <a name="changes-from-version-32"></a>Változások a 3.2-es verzióhoz képest

- A [kiszolgálóoldali újrapróbálkozás (SSR)](prevent-rate-limiting-errors.md) funkció alapértelmezés szerint engedélyezve van, így az ügyfélalkalmazásból érkező kérések nem adnak vissza 16500-as hibát. Ehelyett a kérelmek addig folytatódnak, amíg befejeződnek vagy túllépik a 60 másodperces időkorlátot.
- Az egyes kérések időkorlátja 60 másodperc.
- Az új vonalprotokoll-verzióval létrehozott MongoDB-gyűjteményekhez alapértelmezés szerint csak az `_id` tulajdonság lesz indexelve.

### <a name="action-required-when-upgrading-from-32"></a>Beavatkozás szükséges a 3.2-es verzióról való frissítéskor

A 3.2-es verzióról való frissítéskor az adatbázisfiók-végpont utótagja a következő formátumra lesz frissítve:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Ha a 3,2-es verzióról frissít, akkor a meglévő végpontot le kell cserélnie az alkalmazásokban és az adatbázis-fiókkal csatlakozó illesztőprogramokban. **Csak az új végpontot használó kapcsolatoknak lesz hozzáférésük az API új verziójának funkcióihoz**. A korábbi, 3.2-es verziójú végpontnak a következő utótaggal kell rendelkeznie: `.documents.azure.com`.

>[!Note]
> Ez a végpont némileg eltérő lehet, ha a fiókja egy szuverén, kormányzati vagy korlátozott Azure-felhőben lett létrehozva.

## <a name="how-to-upgrade"></a>A frissítés lépései

1. Jelentkezzen be a [Azure Portalba.](https://portal.azure.com/)

1. Navigáljon a MongoDB-fiókhoz tartozó Azure Cosmos DB API-hoz. Nyissa meg az **Áttekintés** ablaktáblát, és ellenőrizze, hogy az aktuális **kiszolgáló verziója** vagy 3,2 vagy 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="A MongoDB-fiók aktuális verzióját a Azure Portalban találja." border="true":::

1. A bal oldali menüben nyissa meg a `Features` panelt. Ez a panel az adatbázis-fiókjához elérhető fiók szintű funkciókat jeleníti meg.

1. Válassza ki a következő sort: `Upgrade MongoDB server version`. Ha nem jelenik meg ez a lehetőség, előfordulhat, hogy a fiók nem jogosult erre a frissítésre. Ha ez a helyzet, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Nyissa meg a szolgáltatások panelt, és frissítse a fiókját." border="true":::

1. Tekintse át a frissítéssel kapcsolatban megjelenő információkat. Válasszon `Set server version to 4.0` (vagy 3,6 a jelenlegi verziótól függően).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Tekintse át a frissítési útmutatót, és válassza a frissítés lehetőséget." border="true":::

1. A frissítés megkezdése után a **funkció** menü szürkén jelenik meg, és az állapot *függőre* van állítva. A frissítés elvégzése körülbelül 15 percet vesz igénybe. Ez a folyamat nem befolyásolja az adatbázis-fiókja meglévő funkcióit vagy műveleteit. Miután befejeződött, a **frissítés MongoDB-kiszolgáló verziójának** állapota megjeleníti a frissített verziót. Ha hiba történt a kérelem feldolgozása során, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/en-us/support/create-ticket/) .

1. A fiók frissítése után a következő szempontokat érdemes figyelembe venni:

    1. Ha 3,2-ról frissített, térjen vissza az **Áttekintés** panelre, és másolja az alkalmazásban használandó új kapcsolódási karakterláncot. A korábbi, 3.2-es verziójú kapcsolati sztring nem szakad meg. A konzisztens felhasználói élmény érdekében az összes alkalmazásnak az új végpontot kell használnia.

    1. Ha a 3.6-os verzióról frissített, a meglévő kapcsolati sztring a megadott verzióra lesz frissítve, és továbbra is ezt kell használni.

## <a name="how-to-downgrade"></a>Váltás korábbi verzióra

A fiókját a 4,0 – 3,6-as verzióra is leértékelheti a "hogyan frissíthet" szakaszban ismertetett lépések segítségével.

Ha 3,2-ről (4,0 vagy 3,6) frissítette a frissítést, és vissza szeretne térni a 3,2-re, egyszerűen visszaválthat az előző (3,2) kapcsolódási karakterlánc használatára a gazdagépen, `accountname.documents.azure.com` amely továbbra is aktív frissítést követően fut az 3,2-os verzióban.

## <a name="next-steps"></a>Következő lépések

- További információk [a MongoDB 4.0-s verziójának támogatott és nem támogatott funkcióiról](mongodb-feature-support-40.md).
- További információk [a MongoDB 3.6-os verziójának támogatott és nem támogatott funkcióiról](mongodb-feature-support-36.md).
- További információkért tekintse meg [a Mongo 3.6-os verziójának funkcióit](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
