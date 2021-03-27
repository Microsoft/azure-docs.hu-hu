---
title: Kiszolgálók Azure Database for PostgreSQL – rugalmas kiszolgáló (előzetes verzió)
description: Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló konfigurálásához és kezeléséhez szükséges szempontokat és irányelveket ismerteti.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 60edbcf97b43747246e84517cc65bb0dca4ff546
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626843"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Kiszolgálók – Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgálóval való használattal kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Mi az Azure Database for PostgreSQL-kiszolgáló?

A Azure Database for PostgreSQL-rugalmas kiszolgáló üzembe helyezési lehetőség egyik kiszolgálója a több adatbázis központi felügyeleti pontja. Ez ugyanaz a PostgreSQL-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A PostgreSQL szolgáltatás felügyelt, és teljesítménybeli garanciákat biztosít, elérhetővé teszi a hozzáférést és a szolgáltatásokat a kiszolgáló szintjén.

Egy Azure Database for PostgreSQL-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisokhoz tartozó szülő erőforrás.
- Névteret biztosít az adatbázisok számára.
- Egy erős élettartamú szemantikaú tároló, amely töröl egy kiszolgálót, és törli a bennük lévő adatbázisokat.
- Collocates-erőforrások egy régióban.
- Kapcsolati végpontot biztosít a kiszolgáló-és adatbázis-hozzáféréshez
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- Több verzióban is elérhető. További információ: a [PostgreSQL-adatbázisok támogatott verziói](concepts-supported-versions.md).
- A felhasználók bővíthetők. További információ: PostgreSQL- [bővítmények](concepts-extensions.md).

Egy Azure Database for PostgreSQL-kiszolgálón belül létrehozhat egy vagy több adatbázist is. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [számítási és tárolási beállítások](concepts-compute-storage.md).

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Hogyan a kapcsolódást és a hitelesítést az adatbázis-kiszolgálóval?

A következő elemek segítenek biztosítani az adatbázis biztonságos elérését:

| Biztonsági koncepció | Description |
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Az Azure Database for PostgreSQL-kiszolgáló támogatja a natív PostgreSQL-hitelesítést. A kiszolgálóhoz történő csatlakozás és a hitelesítés rendszergazdai bejelentkezéssel végezhető el. |
| **Protokoll** | A szolgáltatás támogatja a PostgreSQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll több mint TCP/IP-cím, illetve UNIX-tartományú szoftvercsatorna esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében a tűzfalszabály megakadályozza az összes hozzáférést a kiszolgálóhoz és az adatbázisaihoz, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for PostgreSQL kiszolgáló tűzfalszabályok szabályait](how-to-manage-firewall-portal.md). |

## <a name="managing-your-server"></a>A kiszolgáló kezelése

Azure Database for PostgreSQL-kiszolgálókat a [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](/cli/azure/postgres)használatával kezelheti.

A kiszolgáló létrehozásakor be kell állítania a rendszergazdai felhasználó hitelesítő adatait. A rendszergazda felhasználó a legmagasabb jogosultsággal rendelkező felhasználó a kiszolgálón. A szerepkör azure_pg_adminhoz tartozik. Ez a szerepkör nem rendelkezik teljes körű rendszergazdai jogosultságokkal. 

A PostgreSQL adminisztrátori attribútum hozzá van rendelve a azure_superuserhoz, amely a felügyelt szolgáltatáshoz tartozik. Nincs hozzáférése ehhez a szerepkörhöz.

Az Azure Database for PostgreSQL-kiszolgáló alapértelmezett adatbázisokkal rendelkezik: 

- **postgres** – egy alapértelmezett adatbázis, amelyet a kiszolgáló létrehozása után tud csatlakozni.
- **azure_maintenance** – ez az adatbázis a felügyelt szolgáltatást a felhasználói műveletekben biztosító folyamatok elkülönítésére szolgál. Nincs hozzáférése ehhez az adatbázishoz.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

A PostgreSQL-kiszolgáló paraméterei határozzák meg a kiszolgáló konfigurációját. A Azure Database for PostgreSQL a paraméterek listáját a Azure Portal vagy az Azure CLI használatával lehet megtekinteni és szerkeszteni.

A postgres felügyelt szolgáltatásaként a Azure Database for PostgreSQL konfigurálható paramétereinek a helyi postgres-példány paramétereinek egy részhalmaza (a postgres paraméterekkel kapcsolatos további információért lásd a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/12/static/runtime-config.html)). A Azure Database for PostgreSQL-kiszolgáló az egyes paraméterekhez tartozó alapértelmezett értékekkel van engedélyezve a létrehozáskor. A felhasználó nem konfigurálhat olyan paramétereket, amelyek szükségesek a kiszolgáló újraindításához vagy a rendszergazdai hozzáférés a módosítások életbe léptetéséhez.

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás áttekintését itt tekintheti meg: [Azure Database for PostgreSQL Overview (áttekintés](overview.md)).
- További információ a **konfiguráció** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatban: [számítási és tárolási beállítások](concepts-compute-storage.md).
- A kiszolgálói paraméterek megtekintése és szerkesztése [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.
