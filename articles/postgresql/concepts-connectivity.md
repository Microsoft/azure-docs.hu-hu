---
title: Átmeneti kapcsolódási hibák kezelése – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan kezelheti az Azure Database for PostgreSQL-kiszolgáló átmeneti kapcsolódási hibáit.
keywords: PostgreSQL-kapcsolat, kapcsolati karakterlánc, csatlakozási problémák, átmeneti hiba, kapcsolódási hiba
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91704350"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Az Azure Database for PostgreSQL átmeneti csatlakozási hibáinak kezelése – Önálló kiszolgáló

Ez a cikk azt ismerteti, hogyan kezelhető az Azure Database for PostgreSQLhoz csatlakozó átmeneti hibák.

## <a name="transient-errors"></a>Átmeneti hibák

Egy átmeneti hiba (más néven átmeneti hiba) egy olyan hiba, amely magát a megoldást fogja megoldani. Ezek a hibák általában az eldobott adatbázis-kiszolgálóval létesített kapcsolatok. Nem nyitható meg a kiszolgálóval létesített új kapcsolatok. Átmeneti hibák merülhetnek fel, például ha hardveres vagy hálózati hiba történik. Egy másik ok lehet a Pásti-szolgáltatás új verziója. Az események többségét a rendszer automatikusan csökkenti a 60 másodpercnél kisebb mértékben. A Felhőbeli alkalmazások tervezéséhez és fejlesztéséhez ajánlott eljárás az átmeneti hibák elvárható. Tegyük fel, hogy bármikor megtörténhetnek bármely összetevőben, és az ilyen helyzetek kezeléséhez megfelelő logikával kell rendelkeznie.

## <a name="handling-transient-errors"></a>Átmeneti hibák elhárítása

Az átmeneti hibákat az újrapróbálkozási logikával kell kezelni. Olyan helyzetek, amelyeket figyelembe kell venni:

* Hiba történik a kapcsolatok megnyitásakor
* Az üresjárati kapcsolatok el lettek dobva a kiszolgáló oldalán. Amikor megpróbál kiadni egy parancsot, nem hajtható végre
* A rendszer eldobott egy olyan aktív kapcsolatokat, amely jelenleg egy parancsot futtat.

Az első és a második eset viszonylag azonnal továbbítható a kezelésére. Próbálja meg újra megnyitni a kapcsolódást. Ha a művelet sikeres, az átmeneti hibát a rendszeren enyhítjük. Újra használhatja a Azure Database for PostgreSQL. Azt javasoljuk, hogy várjon a kapcsolódás megkísérlése előtt. Vissza, ha a kezdeti újrapróbálkozások sikertelenek lesznek. Így a rendszeren az összes rendelkezésre álló erőforrás felhasználható a hiba helyzetének leküzdésére. A követendő példa a következő:

* Várjon 5 másodpercig az első újrapróbálkozás előtt.
* Minden egyes újrapróbálkozáskor a várakozás exponenciálisan növekszik, akár 60 másodpercre.
* Állítsa be az újrapróbálkozások maximális számát, amikor az alkalmazás nem tudta végrehajtani a műveletet.

Ha egy aktív tranzakcióval rendelkező kapcsolat meghiúsul, nehezebb a helyreállítás megfelelő kezelése. Két eset létezik: Ha a tranzakció csak olvasható, akkor a rendszer biztonságosan újra megnyithatja a kapcsolódást, és újrapróbálkozhat a tranzakcióval. Ha azonban a tranzakciót is megírta az adatbázisba, meg kell határoznia, hogy a tranzakció vissza lett-e állítva, vagy az átmeneti hiba miatt sikeres volt-e. Ebben az esetben előfordulhat, hogy nem fogadta el az adatbázis-kiszolgálóról a véglegesítő nyugtát.

Ennek egyik módja egy egyedi azonosító létrehozása az ügyfélen, amely az összes újrapróbálkozáshoz használatos. Ezt az egyedi azonosítót a tranzakció részeként adja át a kiszolgálónak, és egy egyedi korlátozással rendelkező oszlopban tárolja. Így biztonságosan újrapróbálkozhat a tranzakcióval. A művelet sikeres lesz, ha az előző tranzakció vissza lett állítva, és az ügyfél által generált egyedi azonosító még nem létezik a rendszeren. Ha az egyedi azonosítót korábban tárolta, a rendszer duplikált kulcs megsértését jelzi, mert az előző tranzakció sikeresen befejeződött.

Ha a program a Azure Database for PostgreSQL harmadik féltől származó middleware-n keresztül kommunikál, kérje meg a gyártót, hogy az átmeneti hibák esetén a middleware tartalmazza-e az újrapróbálkozási logikát.

Ügyeljen arra, hogy tesztelje újra a logikát. Például próbálja meg végrehajtani a kódot a Azure Database for PostgreSQL kiszolgáló számítási erőforrásainak felfelé vagy lefelé skálázásakor. Az alkalmazásnak gond nélkül kell kezelnie a művelet során észlelt rövid állásidőt.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Database for PostgreSQL-hez való csatlakozás hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
