---
title: A kapcsolatok problémáinak elhárítása – Azure Database for MariaDB
description: Ismerje meg, hogy miként lehet elhárítani a Azure Database for MariaDB kapcsolódási problémáit, beleértve az újrapróbálkozásokat igénylő átmeneti hibákat, a tűzfal problémáit és az kimaradásokat.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b9a670472529dccd51cbfa71e385151bc63cff9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664732"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Az Azure Database for MariaDB-hez való csatlakozás hibáinak elhárítása

A kapcsolódási problémákat számos különböző dolog okozhatja, többek között:

* Tűzfalbeállítások
* A kapcsolatok időtúllépése
* Helytelen bejelentkezési adatok
* Néhány Azure Database for MariaDB erőforráshoz elérte a maximális korlátot
* A szolgáltatás infrastruktúrájának problémái
* A szolgáltatásban végrehajtott karbantartás
* A kiszolgáló számítási kiosztását a virtuális mag számának méretezésével vagy egy másik szolgáltatási szinten való áthelyezéssel módosítjuk.

A Azure Database for MariaDBhoz való kapcsolódási problémák általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy időszakos)
* Állandó vagy nem átmeneti hibák (a rendszeresen ismétlődő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Átmeneti hibák történnek a karbantartás végrehajtásakor, a rendszer hibát észlel a hardverrel vagy a szoftverrel kapcsolatban, vagy megváltoztatja a kiszolgáló virtuális mag vagy szolgáltatási szintjét. A Azure Database for MariaDB szolgáltatás beépített magas rendelkezésre állással rendelkezik, és úgy van kialakítva, hogy az ilyen típusú problémákat automatikusan enyhítse. Az alkalmazás azonban nem éri el a kiszolgálóval való kapcsolatát egy rövid ideig, amely általában kevesebb, mint 60 másodperc. Egyes események esetenként hosszabb időt vehetnek igénybe, például ha egy nagy tranzakció hosszan futó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Győződjön meg arról, hogy a [Microsoft Azure szolgáltatás irányítópultján](https://azure.microsoft.com/status) az alkalmazás által jelentett hibák ideje alatt bekövetkezett ismert kimaradások szerepelnek.
2. A felhőalapú szolgáltatásokhoz, például Azure Database for MariaDBokhoz csatlakozó alkalmazásoknak átmeneti hibákat kell elvárniuk, és az újrapróbálkozási logikát kell végrehajtaniuk, hogy ezeket a hibákat az alkalmazás hibáiként felhasználja a felhasználók számára. Az átmeneti hibák kezelésével kapcsolatos ajánlott eljárásokért és tervezési útmutatóért tekintse át [Azure Database for MariaDB átmeneti csatlakozási hibák kezelését](concepts-connectivity.md) .
3. Mivel a kiszolgáló megközelíti az erőforrás-korlátait, úgy tűnik, hogy a hibák átmeneti kapcsolódási problémát jelentenek. Tekintse [meg a Azure Database for MariaDB korlátozásait](concepts-limits.md).
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az alkalmazás az időtartamot meghaladja a 60 másodpercnél, vagy ha egy adott nap több előfordulását látja a hibával, az Azure-támogatási kérést az [Azure](https://azure.microsoft.com/support/options) támogatási webhelyén, a **támogatás beszerzése** lehetőség választásával kérheti le.

## <a name="troubleshoot-persistent-errors"></a>Állandó jellegű hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni a Azure Database for MariaDBhoz, általában a következők egyikével kapcsolatos problémát jelez:

* Tűzfal-konfiguráció: a Azure Database for MariaDB-kiszolgáló vagy az ügyféloldali tűzfal blokkolja a kapcsolatokat.
* Hálózati újrakonfigurálás az ügyféloldali oldalon: új IP-cím vagy proxykiszolgáló lett hozzáadva.
* Felhasználói hiba: lehet, hogy hibás típusú kapcsolatok vannak megadva, például a kiszolgáló neve a (z) vagy a felhasználónévben lévő *\@ kiszolgálónév* -utótaggal.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó csatlakozási problémák megoldásának lépései

1. [Tűzfalszabályok](howto-manage-firewall-portal.md) beállítása az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy tűzfalszabály használatát a 0.0.0.0 értékkel a kezdő IP-címként, és használja a 255.255.255.255 a záró IP-címként. Ekkor megnyílik a kiszolgáló minden IP-címre. Ha ezzel feloldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályot a megfelelő korlátozott IP-címhez vagy címtartomány létrehozásához.
2. Az ügyfél és az Internet közötti összes tűzfalon ellenőrizze, hogy a 3306-es port nyitva van-e a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolatok karakterláncát és az egyéb kapcsolatbeállításokat. Tekintse át az [alkalmazások Azure Database for MariaDBhoz való kapcsolódásának módját](howto-connection-string.md).
4. Keresse meg a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy regionális leállás van, tekintse meg az [üzletmenet folytonosságának áttekintése](concepts-business-continuity.md) című témakört, amely az új régióba való helyreállítás lépéseit Azure Database for MariaDB.

## <a name="next-steps"></a>Következő lépések

* [Azure Database for MariaDB átmeneti kapcsolódási hibáinak kezelése](concepts-connectivity.md)
