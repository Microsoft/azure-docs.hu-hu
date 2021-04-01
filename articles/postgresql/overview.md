---
title: Mi az Azure Database for PostgreSQL?
description: Áttekintést nyújt Azure Database for PostgreSQL a kapcsolódó adatbázis-szolgáltatásról a rugalmas kiszolgáló kontextusában.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491325"
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az az Azure Database for PostgreSQL?

A Azure Database for PostgreSQL egy, a Microsoft felhőben található, a [PostgreSQL közösségi kiadásán](https://www.postgresql.org/) alapuló, a GPLv2-licencben elérhető adatbázis-kezelője. Az Azure Database for PostgreSQL a következőket nyújtja:

- Beépített magas rendelkezésre állás.
- Adatvédelem automatikus biztonsági mentéssel és időponthoz való visszaállítással akár 35 napig.
- Automatizált karbantartás az alapul szolgáló hardver, operációs rendszer és adatbázismotor számára a szolgáltatás biztonságos és naprakészen tartásához.
- Kiszámítható teljesítmény, használatalapú fizetéses befoglaló díjszabással.
- Rugalmas skálázás másodpercek alatt.
- Nagyvállalati szintű biztonság és piacvezető megfelelőség a bizalmas adatok védelme érdekében a nyugalmi és a mozgásban.
- Figyelés és automatizálás a nagyméretű központi telepítések felügyeletének és figyelésének egyszerűsítése érdekében.
- Piacvezető támogatási élmény.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

Ezek a képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Lehetővé teszik, hogy az alkalmazások gyors fejlesztésére koncentráljon, és a virtuális gépek és az infrastruktúra kezeléséhez szükséges értékes idő és erőforrások kiosztása helyett a piacra kerülési időt kell fordítania. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

## <a name="deployment-models"></a>Üzembe helyezési modellek

A PostgreSQL Community Edition által működtetett Azure Database for PostgreSQL három üzembe helyezési módban érhető el:

- Önálló kiszolgáló
- Rugalmas kiszolgáló (előzetes verzió)
- Rugalmas skálázás (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Önálló kiszolgáló

Azure Database for PostgreSQL egyetlen kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely minimális követelményeket támaszt az adatbázis testreszabásához. Az egykiszolgálós platform úgy lett kialakítva, hogy az adatbázis-kezelési funkciók, például a javítások, a biztonsági mentések, a magas rendelkezésre állás, a biztonság minimális felhasználói konfigurációval és vezérléssel kezelhető legyen. Az architektúra beépített, magas rendelkezésre állású, 99,99%-os rendelkezésre állást biztosít egyetlen rendelkezésre állási zónában. A PostgreSQL 9,5, 9, 6, 10 és 11 közösségi verzióját támogatja. A szolgáltatás ma már széles körben elérhető az [Azure számos régiójában](https://azure.microsoft.com/global-infrastructure/services/).

Az egykiszolgálós üzembehelyezési lehetőség három tarifacsomagot kínál: Alapszintű, Általános célú és Memóriaoptimalizált. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](./concepts-pricing-tiers.md) leírása tartalmazza.

Az egyetlen kiszolgáló a legjobb megoldás a Felhőbeli natív alkalmazásokhoz, amelyek az automatikus javítás kezelésére szolgálnak anélkül, hogy a javítási ütemterv és az egyéni PostgreSQL konfigurációs beállítások részletes szabályozására lenne szükség.

Az egykiszolgálós telepítési mód részletes áttekintéséhez tekintse meg az [egykiszolgálós áttekintés című témakört](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Rugalmas Azure Database for PostgreSQL-kiszolgáló (előzetes verzió)

Azure Database for PostgreSQL rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások felett. Általánosságban a szolgáltatás a felhasználói követelmények alapján nagyobb rugalmasságot és testreszabást biztosít. A rugalmas kiszolgáló architektúrája lehetővé teszi a felhasználók számára, hogy az egyetlen rendelkezésre állási zónában és több rendelkezésre állási zónában is magas rendelkezésre állást engedélyezzenek A rugalmas kiszolgáló jobb költség-optimalizálási vezérlőket biztosít a kiszolgáló és a feltört számítási szint leállítására/indítására, ideális olyan munkaterhelésekhez, amelyek folyamatosan teljes számítási kapacitást igényelnek. A szolgáltatás jelenleg a PostgreSQL 11 és 12 közösségi verzióját támogatja, és hamarosan újabb verziókat ad hozzá. A szolgáltatás jelenleg nyilvános előzetes verzióban érhető el, ma már számos Azure-régióban elérhető.

A rugalmas kiszolgálók a legmegfelelőbbek a következőhöz:

- Hatékonyabb vezérlést és testreszabást igénylő alkalmazások fejlesztése.
- A kiszolgáló leállítására/indítására képes optimalizálási vezérlők.
- Zóna redundáns magas rendelkezésre állása
- Felügyelt karbantartási időszakok
  
A rugalmas kiszolgáló üzembe helyezési módjának részletes áttekintését lásd: [rugalmas kiszolgáló – áttekintés](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus)

A rugalmas skálázás (Citus) beállítás horizontálisan skálázza a lekérdezéseket több gépen a sharding (horizontális skálázás) segítségével. A lekérdezési motor párhuzamosan hajtja végre a bejövő SQL-lekérdezéseket ezeken kiszolgálókon, hogy a nagy méretű adathalmazok esetében is gyors választ biztosítson. Nagyobb méretet és teljesítményt igénylő alkalmazásokat szolgál ki. Ezek általában olyan számítási feladatok, amelyekben az adatok mérete megközelíti (vagy meghaladta) a 100 GB-ot.

A rugalmas skálázási (Citus) üzembehelyezési lehetőség a következőket nyújtja:

- Horizontális skálázás több gépen a sharding (horizontális skálázás) segítségével
- A lekérdezések párhuzamosítása a kiszolgálókon a nagy méretű adathalmazokról adott gyors válaszokhoz
- Kiváló támogatás a több-bérlős alkalmazások, a valós idejű működési elemzések és a nagy teljesítményű tranzakciós számítási feladatok számára
  
A PostgreSQL-re épülő alkalmazások [a standard szintű](./concepts-connection-libraries.md) nagy kapacitású (Citus) elosztott lekérdezéseket futtathatnak, és minimális módosításokat hajtanak végre.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Database for PostgreSQL három üzembe helyezési módjáról, valamint az igényeinek megfelelő beállítások kiválasztásáról.

- [Önálló kiszolgáló](./overview-single-server.md)
- [Rugalmas kiszolgáló](./flexible-server/overview.md)
- Rugalmas skálázás (Citus)