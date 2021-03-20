---
title: Egyedi kulcsok használata a Azure Cosmos DBban
description: Ismerje meg, hogyan határozhat meg és használhat egyedi kulcsokat egy Azure Cosmos-adatbázishoz. A cikk azt is leírja, hogy az egyedi kulcsok hogyan adhatnak hozzá adatintegritási réteget.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685720"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Egyedi kulcsokra vonatkozó korlátozások a Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az egyedi kulcsok adatintegritási réteget vesznek fel egy Azure Cosmos-tárolóba. Egy Azure Cosmos-tároló létrehozásakor egyedi kulcs-szabályzatot kell létrehoznia. Egyedi kulcsokkal gondoskodhat arról, hogy a logikai partíción belül egy vagy több érték egyedi legyen. Emellett a [partíciós kulcs](partitioning-overview.md)egyediségét is garantálhatja.

Miután létrehozta az egyedi kulcs-házirenddel rendelkező tárolót, a rendszer az egyedi kulcs megkötése által megadott módon megakadályozza a meglévő elemek új vagy frissítésének létrehozását egy logikai partíción belül. Az egyedi kulccsal összevont partíciós kulcs garantálja a tároló hatókörén belüli elem egyediségét.

Vegyünk például egy Azure Cosmos-tárolót `Email address` az egyedi kulcs megkötésével és `CompanyID` a partíciós kulccsal. Ha a felhasználó e-mail-címét egyedi kulccsal konfigurálja, minden elemnek egyedi e-mail-címe van az adott területen belül `CompanyID` . Két elem nem hozható létre duplikált e-mail-címmel, és ugyanazzal a partíciós kulcs értékkel. Azure Cosmos DB SQL (Core) API-ban az elemek JSON-értékként vannak tárolva. Ezeket a JSON-értékeket megkülönbözteti a kis-és nagybetűk Ha egy tulajdonságot egyedi kulcsként választ, a tulajdonsághoz beillesztheti a kis-és nagybetűket megkülönböztető értékeket is. Ha például egy egyedi kulcs van definiálva a Name (név) tulajdonságban, a "Gaby" kifejezés eltér a "Gaby" típustól, és a tárolóba is beilleszthető.

Ha ugyanazzal az e-mail-címmel szeretne elemeket létrehozni, de nem ugyanaz az utónév, a vezetéknév és az e-mail-cím, adjon hozzá további elérési utakat az egyedi kulcs házirendjéhez. Ahelyett, hogy csak az e-mail-cím alapján hozzon létre egyedi kulcsot, létrehozhat egy egyedi kulcsot is az utónév, a vezetéknév és az e-mail cím kombinációjával. Ezt a kulcsot összetett egyedi kulcsnak nevezzük. Ebben az esetben a három érték egy adott egyedi kombinációja `CompanyID` engedélyezett. 

A tároló tartalmazhat például olyan elemeket, amelyek a következő értékekkel rendelkeznek, ahol mindegyik elem kiértékeli az egyedi kulcs megkötését.

|CompanyID|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Ha egy másik, az előző táblázatban felsorolt kombinációval rendelkező tételt próbál beszúrni, hibaüzenet jelenik meg. A hiba azt jelzi, hogy az egyedi kulcs megkötése nem teljesült. `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` Visszaküldési üzenetet kap. 

## <a name="define-a-unique-key"></a>Egyedi kulcs definiálása

Egyedi kulcsokat csak akkor adhat meg, ha létrehoz egy Azure Cosmos-tárolót. Az egyedi kulcs hatóköre egy logikai partíció. Az előző példában, ha a tárolót a ZIP-kód alapján particionálja, az egyes logikai partíciókban duplikált elemek jelennek meg. Egyedi kulcsok létrehozásakor vegye figyelembe a következő tulajdonságokat:

* Egy meglévő tároló nem frissíthető más egyedi kulcs használatára. Más szóval, miután egy tároló egyedi kulcs-házirenddel lett létrehozva, a szabályzat nem módosítható.

* Egy meglévő tároló egyedi kulcsának beállításához hozzon létre egy új tárolót az egyedi kulcs korlátozásával. A megfelelő áttelepítési eszköz használatával helyezze át az adatait a meglévő tárolóból az új tárolóba. SQL-tárolók esetén az [adatáttelepítési eszköz](import-data.md) használatával helyezze át az adatátvitelt. MongoDB-tárolók esetén [mongoimport.exe vagy mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) használatával helyezheti át az adatáthelyezést.

* Az egyedi kulcsokra vonatkozó házirend legfeljebb 16 elérésiút-értéket tartalmazhat. Az értékek például a következőek lehetnek:, `/firstName` `/lastName` és `/address/zipCode` . Az egyes egyedi kulcsokra vonatkozó szabályzatok legfeljebb 10 egyedi kulcs megkötést vagy kombinációt tartalmazhatnak. Az egyes egyedi indexek korlátozásának kombinált elérési útjai nem haladhatják meg a 60 bájtot. Az előző példában az utónév, a vezetéknév és az e-mail-cím együtt egy megkötés. Ez a megkötés 3 lehetőséget használ a 16 lehetséges útvonalon.

* Ha egy tároló egyedi kulcsokra vonatkozó szabályzattal rendelkezik, az elemek létrehozásához, frissítéséhez és törléséhez szükséges használati [egység (ru)](request-units.md) díja valamivel nagyobb.

* A ritka egyedi kulcsok nem támogatottak. Ha néhány egyedi elérésiút-érték hiányzik, a rendszer null értékként kezeli őket, amelyek az egyediségi megkötésben részt vesznek. Emiatt csak egyetlen elem lehet null értékű, hogy kielégítse ezt a korlátozást.

* Az egyedi kulcsnév megkülönbözteti a kis-és nagybetűket. Vegyünk például egy olyan tárolót, amelyben az egyedi kulcs megszorítása van beállítva `/address/zipcode` . Ha az adatai egy nevű mezővel rendelkeznek `ZipCode` , Azure Cosmos db a "NULL" értéket szúrja be egyedi kulcsként, mert `zipcode` nem ugyanaz, mint `ZipCode` . Ennek az esetnek az eltérése miatt az irányítószámmal nem szúrható be minden más rekord, mert az ismétlődő "NULL" megsérti az egyedi kulcs megkötését.

## <a name="next-steps"></a>Következő lépések

* További információ a [logikai partíciókhoz](partitioning-overview.md)
* Ismerje [meg, hogyan határozhat meg egyedi kulcsokat](how-to-define-unique-keys.md) tároló létrehozásakor
