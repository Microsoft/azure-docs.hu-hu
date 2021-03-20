---
title: Infrastruktúra-kettős titkosítás – Azure Database for MySQL
description: Ismerje meg, hogyan használható az infrastruktúra kettős titkosítása egy második titkosítási réteg hozzáadásához egy szolgáltatás által felügyelt kulcsokkal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: e9182a2a0b88f85af5305f5794fec2ffe7935701
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631733"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL infrastruktúra kettős titkosítása

A Azure Database for MySQL a Microsoft által felügyelt kulcsok használatával adattárolási [titkosítást használ a REST-](concepts-security.md#at-rest) adatokon. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva vannak, és ez a titkosítás mindig be van kapcsolva, és nem tiltható le. A titkosítás FIPS 140-2 ellenőrzött titkosítási modult és egy AES 256-bites titkosítást használ az Azure Storage-titkosításhoz.

Az infrastruktúra-dupla titkosítás a szolgáltatás által felügyelt kulcsok használatával egy második titkosítási réteget helyez el. FIPS 140-2 hitelesített titkosítási modult használ, de egy másik titkosítási algoritmussal. Ez egy további védelmi réteget biztosít az adatokhoz a nyugalmi állapotban. Az infrastruktúra kettős titkosításához használt kulcsot a Azure Database for MySQL szolgáltatás is kezeli. Az infrastruktúra kettős titkosítása alapértelmezés szerint nincs engedélyezve, mert a további titkosítási réteg hatással lehet a teljesítményre.

> [!NOTE]
> Ez a funkció csak a "általános célú" és a "memória-optimalizált" árképzési szintek esetén támogatott a Azure Database for MySQLban.

Az infrastruktúra-réteg titkosításának előnye az, hogy a tárolóeszközhöz vagy a hálózati vezetékekhez legközelebb eső rétegben valósul meg. Azure Database for MySQL megvalósítja a két titkosítási réteget a szolgáltatás által felügyelt kulcsok használatával. Bár a szolgáltatási rétegben még mindig technikailag van, nagyon közel van az adatok tárolására szolgáló hardverekhez. Továbbra is engedélyezheti az inaktív adatok titkosítását az [ügyfél által felügyelt kulcs](concepts-data-encryption-mysql.md) használatával a kiépített MySQL-kiszolgálóhoz. 

Az infrastruktúra-rétegekben való megvalósítás a kulcsok sokféleségét is támogatja. Az infrastruktúrának tisztában kell lennie a gépek és hálózatok különböző csoportjaival. Ennek megfelelően a különböző kulcsokat a rendszer az infrastruktúra-támadások és a különböző hardver-és hálózati hibák minimalizálására használja. 

> [!NOTE]
> Az infrastruktúra-kettős titkosítás használata a további titkosítási folyamat miatt hatással lesz a Azure Database for MySQL-kiszolgálóra.

## <a name="benefits"></a>Előnyök

A Azure Database for MySQL infrastruktúra-kettős titkosítása a következő előnyöket nyújtja:

1. A **kriptográfiai megvalósítás további sokfélesége** – a hardveres titkosítás tervezett áthelyezése tovább bővíti a megvalósításokat azáltal, hogy hardveres implementációt biztosít a szoftveres implementáción kívül.
2. **Megvalósítási hibák** – az infrastruktúra szintjén két titkosítási réteg védi a gyorsítótárazási vagy a memória-kezelés hibáit, amelyek lehetővé teszik az egyszerű szöveges adatmennyiséget. Emellett a két réteg a titkosítás megvalósításában általában is biztosítja a hibákat.

Ezek kombinációja erős védelmet nyújt a titkosítás elleni támadásokkal kapcsolatos gyakori fenyegetések és gyengeségek ellen.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Infrastruktúra-kettős titkosítással támogatott forgatókönyvek

A Azure Database for MySQL által biztosított titkosítási képességek együtt használhatók. Az alábbiakban a különböző felhasználási helyzetek összegzése látható:

|  ##   | Alapértelmezett titkosítás | Infrastruktúra kettős titkosítása | Adattitkosítás az ügyfél által felügyelt kulcsokkal  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Igen*              | *Nem*                             | *Nem*                                         |
| 2     | *Igen*              | *Igen*                            | *Nem*                                         |
| 3     | *Igen*              | *Nem*                             | *Igen*                                        |
| 4     | *Igen*              | *Igen*                            | *Igen*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - A 2. és a 4. forgatókönyv a Azure Database for MySQL kiszolgáló munkaterhelés-típusa alapján 5-10%-os csökkenést tud bevezetni az infrastruktúra további titkosítási rétege miatt.
> - Az infrastruktúra dupla titkosításának konfigurálása Azure Database for MySQL esetén csak a kiszolgáló létrehozásakor engedélyezett. A kiszolgáló üzembe helyezését követően a tároló titkosítása nem módosítható. Azonban továbbra is engedélyezheti az adattitkosítást az ügyfél által felügyelt kulcsokkal az infrastruktúra kettős titkosításával/nélkül létrehozott kiszolgáló esetében.

## <a name="limitations"></a>Korlátozások

Azure Database for MySQL esetében a szolgáltatás által felügyelt kulccsal történő kettős titkosítás támogatása a következő korlátozásokkal jár:

* A funkció támogatása a **általános célú** és a **memória optimalizált** díjszabási szintjeire korlátozódik.
* Ez a funkció csak a 16 TB-ig terjedő tárterületet támogató régiókban és kiszolgálókon támogatott. A tárterületet 16 TB-ot támogató Azure-régiók listájáért tekintse meg a [Storage dokumentációját](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes **új** MySQL-kiszolgáló támogatja az ügyfél-kezelő kulcsaival való adattitkosítást is. Ebben az esetben az időponthoz tartozó visszaállítás (PITR) vagy az olvasási replikák használatával létrehozott kiszolgálók nem minősülnek "új" értéknek.
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló legfeljebb 16 TB-ot támogat-e, nyissa meg az árképzési rétegek panelt a portálon, és ellenőrizze, hogy a tárolási csúszka akár 16 TB-ra is áthelyezhető-e. Ha a csúszkát legfeljebb 4 TB-ra tudja mozgatni, előfordulhat, hogy a kiszolgáló nem támogatja a titkosítást az ügyfél által felügyelt kulcsokkal. az adatai azonban mindig a szolgáltatás által felügyelt kulcsokkal vannak titkosítva. AskAzureDBforMySQL@service.microsoft.comHa bármilyen kérdése van, lépjen kapcsolatba.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [állíthatja be az infrastruktúra kettős titkosítását a MySQL](howto-double-encryption.md)-hez készült Azure Database-hez.
