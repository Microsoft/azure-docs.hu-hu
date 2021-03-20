---
title: Kapcsolatok – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerje meg, hogyan lehet elhárítani a Azure Database for PostgreSQL-nagy kapacitású kapcsolódási problémáit (Citus)
keywords: PostgreSQL-kapcsolat, kapcsolati karakterlánc, csatlakozási problémák, átmeneti hiba, kapcsolódási hiba
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295617"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-nagy kapacitású kapcsolódási problémáinak elhárítása (Citus)

A kapcsolódási problémákat számos dolog okozhatja, például:

* Tűzfalbeállítások
* A kapcsolatok időtúllépése
* Helytelen bejelentkezési adatok
* A kiszolgálói csoport számára elérte a kapcsolatok korlátját
* A szolgáltatás infrastruktúrájának problémái
* Szolgáltatás karbantartása
* A koordinátor csomópontja feladatátvételt hajt végre az új hardvereszközökön

A nagy kapacitású (Citus) kapcsolódási problémái általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy időszakos)
* Állandó vagy nem átmeneti hibák (a rendszeresen ismétlődő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Az átmeneti hibák számos okból történnek. A leggyakoribb például a Rendszerkarbantartás, a hardveres vagy szoftveres hiba, valamint a koordinátori csomópontok virtuális mag frissítése.

A magas rendelkezésre állású nagy kapacitású (Citus) kiszolgálói csoport csomópontjainak engedélyezése automatikusan csökkentheti az ilyen típusú problémákat. Az alkalmazásnak azonban még fel kell készülnie, hogy röviden elveszítse a kapcsolatát. Más események is hosszabb időt vehetnek igénybe, például ha egy nagy tranzakció hosszan futó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Győződjön meg arról, hogy a [Microsoft Azure szolgáltatás irányítópultján](https://azure.microsoft.com/status) az alkalmazás által a hibák jelentésének ideje során bekövetkezett ismert kimaradások szerepelnek.
2. A felhőalapú szolgáltatásokhoz, például a nagy kapacitású (Citus) csatlakozó alkalmazásoknak átmeneti hibákat kell elvárniuk, és szabályosan kell reagálnia. Az alkalmazásoknak például meg kell valósítaniuk az újrapróbálkozási logikát a hibák kezeléséhez, ahelyett, hogy az alkalmazási hibákat felhasználják a felhasználóknak.
3. Mivel a kiszolgálócsoport megközelíti az erőforrás-korlátozásokat, a hibák az átmeneti kapcsolódási problémákhoz hasonlóak lehetnek. A csomópont RAM-számának növelése vagy a feldolgozó csomópontok hozzáadása és az adatkiegyensúlyozás is segíthet.
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy 60 másodpercnél tovább tartanak, vagy naponta többször történnek, akkor [Az Azure támogatási webhelyén válassza](https://azure.microsoft.com/support/options) a **támogatás beszerzése** lehetőséget.

## <a name="troubleshoot-persistent-errors"></a>Állandó jellegű hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni a nagy kapacitású (Citus), a leggyakoribb okok a tűzfal helytelen konfigurációja vagy felhasználói hiba.

* Koordinátori csomópont tűzfala: Ellenőrizze, hogy a nagy kapacitású (Citus) kiszolgálói tűzfal úgy van-e konfigurálva, hogy engedélyezze az ügyfélről érkező kapcsolatokat, beleértve a proxykiszolgálót és az átjárókat is.
* Ügyféloldali tűzfal konfigurációja: az ügyfélen lévő tűzfalnak engedélyeznie kell az adatbázis-kiszolgálóhoz való kapcsolódást. Egyes tűzfalak esetében nem csak a név, hanem a kiszolgáló IP-címeinek és portjainak engedélyezése szükséges.
* Felhasználói hiba: dupla – ellenőrizze a kapcsolatok karakterláncát. Lehet, hogy a kiszolgáló neve nem megfelelő típusú paraméterekkel rendelkezik. A Azure Portal különböző nyelvi keretrendszerek és psql esetében a kapcsolatok karakterláncai találhatók. Nyissa meg a nagy kapacitású (Citus) kiszolgálócsoport **kapcsolódási karakterláncok** lapját. Azt is vegye figyelembe, hogy a nagy kapacitású-(Citus-) fürtöknek csak egy adatbázisa van, az előre definiált neve pedig **Citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó csatlakozási problémák megoldásának lépései

1. [Tűzfalszabályok](howto-hyperscale-manage-firewall-using-portal.md) beállítása az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy tűzfalszabály használatát a 0.0.0.0 értékkel a kezdő IP-címként, és használja a 255.255.255.255 a záró IP-címként. Ez a szabály minden IP-cím számára megnyitja a kiszolgálót. Ha a szabály feloldja a kapcsolati problémát, távolítsa el, és hozzon létre egy tűzfalszabály megfelelő, korlátozott IP-címhez vagy címtartományból.
2. Az ügyfél és az Internet közötti összes tűzfalon ellenőrizze, hogy a 5432-es port nyitva van-e a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolatok karakterláncát és az egyéb kapcsolatbeállításokat.
4. Keresse meg a szolgáltatás állapotát az irányítópulton.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg a tűzfalszabályok fogalmait [a Azure Database for PostgreSQL-nagy kapacitású (Citus)](concepts-hyperscale-firewall-rules.md) alkalmazásban
* Lásd: [Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok kezelése](howto-hyperscale-manage-firewall-using-portal.md)
