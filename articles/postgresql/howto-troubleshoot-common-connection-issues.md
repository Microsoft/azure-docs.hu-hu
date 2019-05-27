---
title: PostgreSQL - hez-kiszolgáló egyetlen Azure-adatbázis-kapcsolatok problémáinak hibaelhárítása
description: Ismerje meg, hogyan háríthatók el a kapcsolódási problémák az Azure Database for PostgreSQL – egyetlen kiszolgáló.
keywords: postgresql-kapcsolat, kapcsolati karakterlánc, kapcsolódási problémák, átmeneti hiba, kapcsolódási hiba
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8a0fe87703c9fb471174c761a6e8296e6e7a37ec
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952111"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>PostgreSQL - hez-kiszolgáló egyetlen Azure-adatbázis-kapcsolatok problémáinak hibaelhárítása

Csatlakozási problémák oka lehet a különböző dolgok, többek között:

* Tűzfalbeállítások
* A kapcsolat időkorlátja
* Helytelen bejelentkezési információk
* Az egyes, Azure Database for PostgreSQL erőforrás elérte a maximális korlátját
* A szolgáltatás az infrastruktúrával kapcsolatos problémák
* A szolgáltatásban végzett karbantartási
* A kiszolgáló számítási lefoglalt módosítja a virtuális magok vagy áthelyezése egy másik szolgáltatásszinthez számának méretezése

Általában a kapcsolódási problémák, Azure database for PostgreSQL segítségével osztályozzák:

* Átmeneti hibák (rövid ideig tartó vagy időszakos)
* Állandó és nem átmeneti hibák (, rendszeresen ismétlődő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Átmeneti hibák fordulhatnak elő, amikor a karbantartásra azért, a rendszer a hardveres vagy szoftveres hiba fordul elő, vagy a kiszolgáló virtuális magok vagy szolgáltatási szintjének módosítása. Az Azure Database for PostgreSQL szolgáltatás beépített magas szinten rendelkezésre áll, és célja, hogy az ilyen típusú problémák megoldásához automatikusan. Azonban az alkalmazás megszakad a kapcsolat a kiszolgálóval általában kevesebb, mint 60 másodpercre, rövid ideig legfeljebb. Egyes események időnként több időt vesz igénybe, például ha olyan nagy tranzakciót miatt a hosszú futású helyreállítási csökkentése érdekében.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [a Microsoft Azure-szolgáltatások irányítópultját](https://azure.microsoft.com/status) , amelyben a jelenített meg az alkalmazás az idő során fellépő ismert szolgáltatáskimaradások.
2. Az alkalmazásokat, amelyek csatlakozni a cloud service, Azure Database for PostgreSQL kell átmeneti hibák várhatóan és megvalósításához újrapróbálkozási logika kezelje ezeket a hibákat, felszínre hozza a ezeket az adatokat az alkalmazáshibák felhasználók helyett. Felülvizsgálat [átmeneti kapcsolati hibákat kezelése az Azure Database for postgresql-hez](concepts-connectivity.md) ajánlott eljárások és tervezési útmutatást az átmeneti hibák kezelése.
3. Egy kiszolgáló megközelíti az erőforrás-korlátozások, mint hibák úgy is tűnik, hogy átmeneti kapcsolati probléma. Lásd: [korlátozások az Azure Database for postgresql-hez](concepts-limits.md).
4. Ha csatlakozási problémák továbbra is, vagy ha, amelynek az alkalmazás a hiba lép fel, mint 60 másodperc, vagy ha a hiba több példányban jelenik meg egy adott napon egy Azure-támogatáskérést fájl kiválasztásával **első támogatja**a a [Azure-támogatási](https://azure.microsoft.com/support/options) hely.

## <a name="troubleshoot-persistent-errors"></a>Az állandó hibák elhárítása

Ha az alkalmazás nem állandó csatlakozás az Azure Database for postgresql-hez, azt általában azt jelzi, hogy probléma az alábbi lehetőségek közül:

* Kiszolgálói tűzfal-konfiguráció: Győződjön meg arról, hogy az Azure Database for PostgreSQL-kiszolgáló tűzfal az ügyfélről, beleértve a proxy-kiszolgálók és az átjárók kapcsolatok engedélyezésére van konfigurálva.
* Ügyfél tűzfal-konfiguráció: Az ügyfélen a tűzfalnak engedélyeznie kell az adatbázis-kiszolgálóhoz csatlakozhat. IP-címek és portok nem lehet a kiszolgáló és az egyes tűzfalak például PostgreSQL alkalmazásnevek engedélyezni kell.
* Felhasználói hiba: Előfordulhat, hogy a kapcsolati paramétereket, például a kiszolgáló neve a kapcsolati karakterlánc vagy egy hiányzó elgépelte  *\@servername* utótagot a felhasználó nevében.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Állandó kapcsolattal összefüggő problémák megoldásának lépései

1. Állítsa be a [tűzfalszabályok](howto-manage-firewall-using-portal.md) lehetővé teszi az ügyfél IP-címet. Ideiglenes kizárólag tesztelési célra egy tűzfalszabályt 0.0.0.0 használja, mint a kezdő IP-cím és a 255.255.255.255 használja, mint a záró IP-cím beállított. Ekkor megnyílik a kiszolgáló összes IP-címet. A kapcsolódási probléma így megoldódik, amennyiben ez a szabály eltávolításához, és hozzon létre egy tűzfalszabályt megfelelően korlátozott IP-címet vagy címtartományt.
2. Az ügyfél és az internet közötti összes tűzfalnak győződjön meg róla, hogy 5432-es port nyitva a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolati karakterláncot, és egyéb kapcsolati beállításokat.
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy van egy regionális kimaradás, [üzletmenet-folytonossági funkcióinak áttekintése az Azure Database for PostgreSQL](concepts-business-continuity.md) a lépéseket egy új régióban helyreállításához.

## <a name="next-steps"></a>További lépések

* [Átmeneti kapcsolati hibákat kezelése az Azure Database for postgresql-hez](concepts-connectivity.md)
