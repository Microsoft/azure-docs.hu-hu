---
title: Kapcsolatok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan lehet elhárítani a Azure Database for PostgreSQL-kiszolgáló kapcsolódási problémáit.
keywords: PostgreSQL-kapcsolat, kapcsolati karakterlánc, csatlakozási problémák, átmeneti hiba, kapcsolódási hiba
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bff930153dc8941fbfe561edf963d5b1c1e7811f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014618"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló kapcsolódási problémáinak elhárítása

A kapcsolódási problémákat számos különböző dolog okozhatja, többek között:

* Tűzfalbeállítások
* A kapcsolatok időtúllépése
* Helytelen bejelentkezési adatok
* Néhány Azure Database for PostgreSQL erőforráshoz elérte a maximális korlátot
* A szolgáltatás infrastruktúrájának problémái
* A szolgáltatásban végrehajtott karbantartás
* A kiszolgáló számítási kiosztását a virtuális mag számának méretezésével vagy egy másik szolgáltatási szinten való áthelyezéssel módosítjuk.

A Azure Database for PostgreSQLhoz való kapcsolódási problémák általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy időszakos)
* Állandó vagy nem átmeneti hibák (a rendszeresen ismétlődő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Átmeneti hibák történnek a karbantartás végrehajtásakor, a rendszer hibát észlel a hardverrel vagy a szoftverrel kapcsolatban, vagy megváltoztatja a kiszolgáló virtuális mag vagy szolgáltatási szintjét. A Azure Database for PostgreSQL szolgáltatás beépített magas rendelkezésre állással rendelkezik, és úgy van kialakítva, hogy az ilyen típusú problémákat automatikusan enyhítse. Az alkalmazás azonban nem éri el a kiszolgálóval való kapcsolatát egy rövid ideig, amely általában kevesebb, mint 60 másodperc. Egyes események esetenként hosszabb időt vehetnek igénybe, például ha egy nagy tranzakció hosszan futó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Győződjön meg arról, hogy a [Microsoft Azure szolgáltatás irányítópultján](https://azure.microsoft.com/status) az alkalmazás által jelentett hibák ideje alatt bekövetkezett ismert kimaradások szerepelnek.
2. A felhőalapú szolgáltatásokhoz, például Azure Database for PostgreSQLokhoz csatlakozó alkalmazásoknak átmeneti hibákat kell elvárniuk, és az újrapróbálkozási logikát kell végrehajtaniuk, hogy ezeket a hibákat az alkalmazás hibáiként felhasználja a felhasználók számára. Az átmeneti hibák kezelésével kapcsolatos ajánlott eljárásokért és tervezési útmutatóért tekintse át [Azure Database for PostgreSQL átmeneti csatlakozási hibák kezelését](concepts-connectivity.md) .
3. Mivel a kiszolgáló megközelíti az erőforrás-korlátait, úgy tűnik, hogy a hibák átmeneti kapcsolódási problémát jelentenek. Tekintse [meg a Azure Database for PostgreSQL korlátozásait](concepts-limits.md).
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az alkalmazás az időtartamot meghaladja a 60 másodpercnél, vagy ha egy adott nap több előfordulását látja a hibával, az Azure-támogatási kérést az [Azure](https://azure.microsoft.com/support/options) támogatási webhelyén, a **támogatás beszerzése** lehetőség választásával kérheti le.

## <a name="troubleshoot-persistent-errors"></a>Állandó jellegű hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni a Azure Database for PostgreSQLhoz, általában a következők egyikével kapcsolatos problémát jelez:

* Kiszolgáló tűzfal-konfigurációja: Ellenőrizze, hogy a Azure Database for PostgreSQL kiszolgáló tűzfala úgy van-e konfigurálva, hogy engedélyezze a kapcsolódást az ügyfélről, beleértve a proxykiszolgálót és az átjárókat is.
* Ügyféloldali tűzfal konfigurációja: az ügyfélen lévő tűzfalnak engedélyeznie kell az adatbázis-kiszolgálóhoz való kapcsolódást. A kiszolgáló azon IP-címeit és portjait, amelyeket nem lehet engedélyezni, valamint az alkalmazások nevét, például a PostgreSQL-t egyes tűzfalakon.
* Felhasználói hiba: lehet, hogy hibás típusú kapcsolatok vannak megadva, például a kiszolgáló neve a (z) vagy a felhasználónévben lévő *\@ kiszolgálónév* -utótagnál.
* Ha úgy látja, hogy a hiba _-kiszolgáló nincs konfigurálva az IPv6-kapcsolatok engedélyezésére_, vegye figyelembe, hogy az alapszintű csomag nem támogatja az VNet-szolgáltatási végpontokat. El kell távolítania a Microsoft. SQL-végpontot abból az alhálózatból, amely az alapszintű kiszolgálóhoz próbál csatlakozni.
* Ha a _"* * *" kapcsolati hiba sslmode értéke érvénytelen, ha az SSL-támogatás nincs lefordítva a_ hibánál, az azt jelenti, hogy a PostgreSQL-ügyfél nem támogatja az SSL-t. Az ügyféloldali libpq valószínűleg nem a "--with-OpenSSL" jelzővel lett lefordítva. Próbáljon meg csatlakozni egy olyan PostgreSQL-ügyféllel, amely SSL-támogatással rendelkezik. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó csatlakozási problémák megoldásának lépései

1. [Tűzfalszabályok](howto-manage-firewall-using-portal.md) beállítása az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy tűzfalszabály használatát a 0.0.0.0 értékkel a kezdő IP-címként, és használja a 255.255.255.255 a záró IP-címként. Ekkor megnyílik a kiszolgáló minden IP-címre. Ha ezzel feloldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályot a megfelelő korlátozott IP-címhez vagy címtartomány létrehozásához.
2. Az ügyfél és az Internet közötti összes tűzfalon ellenőrizze, hogy a 5432-es port nyitva van-e a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolatok karakterláncát és az egyéb kapcsolatbeállításokat.
4. Keresse meg a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy regionális leállás van, tekintse meg az [üzletmenet folytonosságának áttekintése](concepts-business-continuity.md) című témakört, amely az új régióba való helyreállítás lépéseit Azure Database for PostgreSQL.

## <a name="next-steps"></a>Következő lépések

* [Azure Database for PostgreSQL átmeneti kapcsolódási hibáinak kezelése](concepts-connectivity.md)
