---
title: Kiszolgálók – Azure Database for MariaDB
description: Ez a témakör a Azure Database for MariaDB-kiszolgálók használatának szempontjait és irányelveit ismerteti.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4d8293258083ea3e8d0172f510e5b41e91328736
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664129"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>A kiszolgálói fogalmak a Azure Database for MariaDB
Ez a cikk a Azure Database for MariaDB-kiszolgálók használatának szempontjait és irányelveit ismerteti.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Mi az Azure Database for MariaDB-kiszolgáló?

Az Azure Database for MariaDB-kiszolgáló több adatbázis központi felügyeleti pontja. Ez ugyanaz a MariaDB-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A Azure Database for MariaDB szolgáltatás felügyelt, teljesítménybeli garanciákat biztosít, és a kiszolgáló szintjén elérhetővé teszi a hozzáférést és a szolgáltatásokat.

Egy Azure Database for MariaDB-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisokhoz tartozó szülő erőforrás.
- Névteret biztosít az adatbázisok számára.
- Egy erős élettartamú szemantikaú tároló, amely töröl egy kiszolgálót, és törli a bennük lévő adatbázisokat.
- Collocates-erőforrások egy régióban.
- Kapcsolati végpontot biztosít a kiszolgáló és az adatbázis eléréséhez.
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- A MariaDB Engine 10,2-es verziójában érhető el. További információ: [támogatott Azure Database for MariaDB adatbázis-verziók](./concepts-supported-versions.md).

Az Azure Database for MariaDB-kiszolgálón egy vagy több adatbázist is létrehozhat. Dönthet úgy is, hogy kiszolgálónként létrehoz egy adatbázist az összes erőforrás használatára, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [díjszabási szintek](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hogyan biztonságos egy Azure Database for MariaDB-kiszolgálót?

A következő elemek segítenek biztosítani az adatbázis biztonságos elérését.

|||
| :--| :--|
| **Hitelesítés és engedélyezés** | Azure Database for MariaDB-kiszolgáló támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai felhasználónevével csatlakozhat és hitelesítheti a kiszolgálókat. |
| **Protokoll** | A szolgáltatás támogatja a MySQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll TCP/IP-és UNIX-tartományi szoftvercsatornák esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében egy tűzfalszabály megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for MariaDB kiszolgáló tűzfalszabályok szabályait](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatok kényszerítését. Lásd: az [SSL-kapcsolat konfigurálása az alkalmazásban a Azure Database for MariaDBhoz való biztonságos csatlakozáshoz](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Azure Database for MariaDB leállítása/elindítása (előzetes verzió)
Azure Database for MariaDB lehetővé teszi a kiszolgáló **leállítását** , ha nincs használatban, és **elindítja** a kiszolgálót a tevékenység folytatásakor. Ez lényegében az adatbázis-kiszolgálók költségeinek megtakarítására szolgál, és csak használat közben kell fizetnie az erőforrásért. Ez még fontosabbá válik a fejlesztési és tesztelési feladatokhoz, és ha csak a nap egy részében használja a kiszolgálót. Ha leállítja a kiszolgálót, az összes aktív kapcsolat el lesz dobva. Később, amikor újra online állapotba szeretné állítani a kiszolgálót, használhatja a [Azure Portal](../mysql/how-to-stop-start-server.md) vagy a [CLI](../mysql/how-to-stop-start-server.md)-t.

Ha a kiszolgáló **leállított** állapotban van, a kiszolgáló számítási felszámítása nem történik meg. A tárterület azonban továbbra is számlázásra kerül, mivel a kiszolgáló tárterülete továbbra is biztosítani fogja, hogy az adatfájlok elérhetők legyenek a kiszolgáló újraindításakor.

> [!IMPORTANT]
> Ha **leállítja** a kiszolgálót, akkor a következő 7 nap során marad ebben az állapotban. Ha ez idő alatt nem **indítja el** manuálisan, a rendszer 7 nap elteltével automatikusan elindítja a kiszolgálót. Ha nem használja a kiszolgálót, úgy is eldöntheti, hogy újra **leállítja** .

Az időkiszolgáló leállításakor nem hajthatók végre felügyeleti műveletek a kiszolgálón. A kiszolgáló konfigurációs beállításainak módosításához [el kell indítania a kiszolgálót](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>A leállítási/indítási művelet korlátai
- Olvasási replika-konfigurációk (forrás-és replikák) esetén nem támogatott.

## <a name="how-do-i-manage-a-server"></a>Hogyan felügyel egy kiszolgálót?
Azure Database for MariaDB-kiszolgálókat a Azure Portal vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>Következő lépések
- A szolgáltatás áttekintését lásd: [Azure Database for MariaDB áttekintése](./overview.md)
- A **szolgáltatási szintek** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
