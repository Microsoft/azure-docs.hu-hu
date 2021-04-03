---
title: Szintetikus partíciós kulcs létrehozása a Azure Cosmos DBban
description: Megtudhatja, hogyan használhatja a szintetikus partíciós kulcsokat az Azure Cosmos-tárolókban az adatok és a számítási feladatok egyenletes elosztása érdekében a partíciós kulcsok között.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 6b8bc44f1ba5624c37620205aaa574e618ef395f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340651"
---
# <a name="create-a-synthetic-partition-key"></a>Szintetikus partíciókulcs létrehozása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az ajánlott eljárás egy olyan partíciós kulcs, amely számos különböző értékkel rendelkezik, például több száz vagy ezer. A cél az adatok és a számítási feladatok egyenletes elosztása a partíciós kulcs értékeihez társított elemek között. Ha egy ilyen tulajdonság nem létezik az adataiban, létrehozhat egy *szintetikus partíciós kulcsot*. Ez a dokumentum több alapvető technikát ismertet a Cosmos-tároló szintetikus partíciós kulcsának létrehozásához.

## <a name="concatenate-multiple-properties-of-an-item"></a>Egy elem több tulajdonságának összefűzése

A partíciós kulcsot úgy alakíthatja ki, hogy több tulajdonság értékét egyetlen mesterséges `partitionKey` tulajdonságba fűzi össze. Ezeket a kulcsokat szintetikus kulcsoknak nevezzük. Vegyük például a következő dokumentumot:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Az előző dokumentum esetében az egyik lehetőség a/deviceId vagy a/Date beállítása partíciós kulcsként. Akkor használja ezt a lehetőséget, ha az eszköz azonosító vagy dátum alapján szeretné particionálni a tárolót. Egy másik lehetőség, hogy összefűzi a két értéket egy olyan szintetikus `partitionKey` tulajdonságba, amelyet a partíciós kulcsként használ.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Valós idejű forgatókönyvekben több ezer elemet is megadhat egy adatbázisban. Ahelyett, hogy manuálisan hozzáadja a szintetikus kulcsot, definiálja az ügyféloldali logikát az értékek összefűzéséhez, és illessze be a szintetikus kulcsot a Cosmos-tárolók elemeibe.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Partíciós kulcs használata véletlenszerű utótaggal

Egy másik lehetséges stratégia a számítási feladatok egyenletes elosztására a partíciós kulcs értékének végén található véletlenszerű szám hozzáfűzésével. Ha ily módon terjeszt el elemeket, párhuzamos írási műveleteket hajthat végre a partíciók között.

Ilyen eset például, ha egy partíciós kulcs egy dátumot jelöl. Kiválaszthat egy 1 és 400 közötti véletlenszerű számot, és összefűzheti azt a dátumhoz. Ez a metódus a (z),  `2018-08-09.1` `2018-08-09.2` , stb. partíciós kulcs értékeit eredményezi  `2018-08-09.400` . Mivel a partíciós kulcs véletlenszerű, a tárolón lévő írási műveletek mindegyike egyenletesen oszlik el több partíció között. Ez a módszer jobb párhuzamosságot és összességében nagyobb átviteli sebességet eredményez.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Előre kiszámított utótagokkal rendelkező partíciós kulcs használata 

A véletlenszerű utótaggal kapcsolatos stratégia nagy mértékben javíthatja az írási sebességet, de egy adott elem nehezen olvasható. Nem tudja, hogy milyen utótagot használt a rendszer az adott elemmel. Az egyes elemek olvasásának egyszerűbbé tételéhez használja az előre kiszámított utótagok stratégiát. Ahelyett, hogy véletlenszerű számmal terjessze az elemeket a partíciók között, egy számot kell használnia, amelyet a lekérdezni kívánt valami alapján kell kiszámítani.

Vegye figyelembe az előző példát, ahol a tároló egy dátumot használ a partíciós kulcsként. Most tegyük fel, hogy minden egyes tételhez tartozik egy  `Vehicle-Identification-Number` ( `VIN` ) attribútum, amelyet szeretnénk elérni. Továbbá tegyük fel, hogy gyakran futtat lekérdezéseket az elemek a `VIN` dátumon felüli kereséséhez. Mielőtt az alkalmazás beírja az elemeket a tárolóba, kiszámíthatja a VIN alapján egy kivonatoló utótagot, és hozzáfűzheti a partíciós kulcs dátumához. Előfordulhat, hogy a számítás egy 1 és 400 közötti számot állít elő, amely egyenletes eloszlású. Ez az eredmény hasonló a véletlenszerű utótagú stratégiai módszer által létrehozott eredményekhez. A partíciós kulcs értéke ezután a számított eredménnyel összefűzött dátum.

Ezzel a stratégiával az írások egyenletesen oszlanak el a partíciós kulcs értékein és a partíciók között. Egy adott elem és dátum könnyen olvasható, mert kiszámíthatja egy adott értékhez tartozó partíciós kulcs értékét `Vehicle-Identification-Number` . Ennek a módszernek az előnye, hogy el kell kerülnie egy egyszerű, például egy olyan partíciós kulcs létrehozását, amely az összes munkaterhelést elvégzi. 

## <a name="next-steps"></a>Következő lépések

A particionálási koncepcióról további információt a következő cikkekben talál:

* További információ a [logikai partíciókhoz](partitioning-overview.md).
* További információ az [átviteli sebesség Azure Cosmos-tárolók és-adatbázisok létesítéséről](set-throughput.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
