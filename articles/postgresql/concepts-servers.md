---
title: Kiszolgálók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL – egyetlen kiszolgáló konfigurálásához és kezeléséhez szükséges szempontokat és irányelveket ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768163"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló
Ez a cikk a Azure Database for PostgreSQL egyetlen kiszolgálóval való használattal kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Mi az Azure Database for PostgreSQL-kiszolgáló?
A Azure Database for PostgreSQL egyetlen kiszolgálóból álló központi telepítési lehetőség egyik kiszolgálója a több adatbázis központi felügyeleti pontja. Ez ugyanaz a PostgreSQL-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A PostgreSQL szolgáltatás felügyelt, és teljesítménybeli garanciákat biztosít, elérhetővé teszi a hozzáférést és a szolgáltatásokat a kiszolgáló szintjén.

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

Egy Azure Database for PostgreSQL-kiszolgálón belül létrehozhat egy vagy több adatbázist is. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [díjszabási szintek](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hogyan a kapcsolódást és a hitelesítést egy Azure Database for PostgreSQL-kiszolgálón?
A következő elemek segítenek biztosítani az adatbázis biztonságos elérését:

|||
|:--|:--|
| **Hitelesítés és engedélyezés** | Azure Database for PostgreSQL-kiszolgáló támogatja a natív PostgreSQL-hitelesítést. A kiszolgáló rendszergazdai felhasználónevével csatlakozhat és hitelesítheti a kiszolgálót. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a PostgreSQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll több mint TCP/IP-cím, illetve UNIX-tartományú szoftvercsatorna esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében a tűzfalszabály megakadályozza az összes hozzáférést a kiszolgálóhoz és az adatbázisaihoz, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for PostgreSQL kiszolgáló tűzfalszabályok szabályait](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>A kiszolgáló kezelése
Azure Database for PostgreSQL-kiszolgálókat a [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](/cli/azure/postgres)használatával kezelheti.

A kiszolgáló létrehozásakor be kell állítania a rendszergazdai felhasználó hitelesítő adatait. A rendszergazda felhasználó a legmagasabb jogosultsággal rendelkező felhasználó a kiszolgálón. A szerepkör azure_pg_adminhoz tartozik. Ez a szerepkör nem rendelkezik teljes körű rendszergazdai jogosultságokkal. 

A PostgreSQL adminisztrátori attribútum hozzá van rendelve a azure_superuserhoz, amely a felügyelt szolgáltatáshoz tartozik. Nincs hozzáférése ehhez a szerepkörhöz.

Az Azure Database for PostgreSQL-kiszolgáló alapértelmezett adatbázisokkal rendelkezik: 
- **postgres** – egy alapértelmezett adatbázis, amelyet a kiszolgáló létrehozása után tud csatlakozni.
- **azure_maintenance** – ez az adatbázis a felügyelt szolgáltatást a felhasználói műveletekben biztosító folyamatok elkülönítésére szolgál. Nincs hozzáférése ehhez az adatbázishoz.
- **azure_sys** – a lekérdezési tároló adatbázisa. Ez az adatbázis nem gyűjti össze az adatmennyiséget, ha a lekérdezési tár ki van kapcsolva. Ez az alapértelmezett beállítás. További információ: a [lekérdezési tároló áttekintése](concepts-query-store.md).


## <a name="server-parameters"></a>Kiszolgálói paraméterek
A PostgreSQL-kiszolgáló paraméterei határozzák meg a kiszolgáló konfigurációját. A Azure Database for PostgreSQL a paraméterek listáját a Azure Portal vagy az Azure CLI használatával lehet megtekinteni és szerkeszteni. 

A postgres felügyelt szolgáltatásaként a Azure Database for PostgreSQL konfigurálható paramétereinek a helyi postgres-példány paramétereinek egy részhalmaza (a postgres paraméterekkel kapcsolatos további információért lásd a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). A Azure Database for PostgreSQL-kiszolgáló az egyes paraméterekhez tartozó alapértelmezett értékekkel van engedélyezve a létrehozáskor. A felhasználó nem konfigurálhat olyan paramétereket, amelyek szükségesek a kiszolgáló újraindításához vagy a rendszergazdai hozzáférés a módosítások életbe léptetéséhez.


## <a name="next-steps"></a>Következő lépések
- A szolgáltatás áttekintését itt tekintheti meg: [Azure Database for PostgreSQL Overview (áttekintés](overview.md)).
- A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](concepts-pricing-tiers.md).
- További információ a szolgáltatáshoz való csatlakozásról: [Azure Database for PostgreSQL kapcsolódási kódtárai](concepts-connection-libraries.md).
- A kiszolgálói paraméterek megtekintése és szerkesztése [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.
