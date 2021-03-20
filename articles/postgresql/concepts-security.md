---
title: Biztonság Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egy kiszolgáló biztonsági funkcióinak áttekintése.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be042a0ec076538cf0f0d155667acea6f1ae19cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710481"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Biztonság Azure Database for PostgreSQL – egyetlen kiszolgáló

Több biztonsági réteg is rendelkezésre áll, amelyek a Azure Database for PostgreSQL-kiszolgálón lévő adatvédelmet biztosítanak. Ez a cikk ezeket a biztonsági beállításokat ismerteti.

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="in-transit"></a>Átvitel közben
A Azure Database for PostgreSQL az adatátvitelt a Transport Layer Security segítségével titkosítja. A titkosítás (SSL/TLS) alapértelmezés szerint ki van kényszerítve.

### <a name="at-rest"></a>Nyugalmi állapotban
A Azure Database for PostgreSQL szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva vannak, beleértve a lekérdezések futtatásakor létrehozott ideiglenes fájlokat is. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A Azure Database for PostgreSQL-kiszolgálóval létesített kapcsolatok először egy regionális átjárón keresztül irányíthatók. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, a kiszolgáló IP-címei pedig védettek. Az átjáróval kapcsolatos további információkért tekintse meg a [kapcsolati architektúra című cikket](concepts-connectivity-architecture.md).  

Az újonnan létrehozott Azure Database for PostgreSQL-kiszolgáló rendelkezik egy tűzfallal, amely blokkolja az összes külső kapcsolatot. Az átjáró elérése azonban nem engedélyezett a kiszolgálóhoz való kapcsolódáshoz. 

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok az egyes kérelmek feladó IP-címe alapján biztosítanak hozzáférést a kiszolgálókhoz. További információért tekintse meg a [Tűzfalszabályok áttekintését](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A Virtual Network szolgáltatás végpontja kiterjeszti a virtuális hálózati kapcsolatot az Azure-gerincen. A virtuális hálózati szabályok használatával engedélyezheti a Azure Database for PostgreSQL-kiszolgáló számára a virtuális hálózatban lévő kijelölt alhálózatok kapcsolatainak engedélyezését. További információt a [Virtual Network szolgáltatás végpontjának áttekintése](concepts-data-access-and-security-vnet.md)című témakörben talál.

### <a name="private-ip"></a>Magánhálózati IP-cím
A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli Azure Database for PostgreSQL egyetlen kiszolgálóhoz. Az Azure Private Link lényegében elérhetővé teszi az Azure-szolgáltatásokat a virtuális magánhálózaton belül. A PaaS-erőforrások a magánhálózati IP-cím használatával érhetők el, ugyanúgy, mint a virtuális hálózat más erőforrásai. További információt a [privát hivatkozások áttekintése](concepts-data-access-and-security-private-link.md) című témakörben talál.


## <a name="access-management"></a>Hozzáférés-kezelés

A Azure Database for PostgreSQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai szerepkör hitelesítő adatait. Ez a rendszergazdai szerepkör további [PostgreSQL-szerepkörök](https://www.postgresql.org/docs/current/user-manag.html)létrehozásához használható.

[Azure Active Directory (HRE) hitelesítés](concepts-aad-authentication.md)használatával is csatlakozhat a kiszolgálóhoz.


## <a name="threat-protection"></a>Fenyegetések elleni védelem

A komplex [veszélyforrások elleni védelem](concepts-data-access-and-security-threat-protection.md) bekapcsolásával olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek a kiszolgálók eléréséhez vagy kiaknázásához.

A [naplózás elérhető](concepts-audit.md) az adatbázisok tevékenységeinek nyomon követéséhez. 

## <a name="migrating-from-oracle"></a>Áttelepítés Oracle-ből

Az Oracle támogatja a transzparens adattitkosítás (TDE) a tábla-és az tablespace-adattitkosítást. A PostgreSQL-hez készült Azure-ban az adatforgalom különböző rétegekben automatikusan titkosítva van. Tekintse meg a jelen oldal "rest" című szakaszát, és tekintse meg a különböző biztonsági témaköröket, beleértve az [ügyfelek által felügyelt kulcsokat](./concepts-data-encryption-postgresql.md) és az [infrastruktúra kettős titkosítását](./concepts-infrastructure-double-encryption.md)is. Érdemes megfontolni az [Azure for PostgreSQL-hez](./concepts-extensions.md)támogatott [pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html) -bővítmény használatát is.

## <a name="next-steps"></a>Következő lépések
- Tűzfalszabályok engedélyezése [IP](concepts-firewall-rules.md) -címekhez vagy [virtuális hálózatokhoz](concepts-data-access-and-security-vnet.md)
- Tudnivalók a [Azure Active Directory hitelesítésről](concepts-aad-authentication.md) Azure Database for PostgreSQL
