---
title: Egyetlen kiszolgáló Azure Database for PostgreSQL
description: Áttekintést nyújt Azure Database for PostgreSQL egyetlen kiszolgálóról.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379216"
---
# <a name="azure-database-for-postgresql-single-server"></a>Egyetlen kiszolgáló Azure Database for PostgreSQL

A PostgreSQL Community Edition által működtetett [Azure Database for PostgreSQL](./overview.md) három üzembe helyezési módban érhető el:

- Önálló kiszolgáló
- Rugalmas kiszolgáló (előzetes verzió)
- Rugalmas skálázás (Citus)

Ebben a cikkben áttekintjük és bemutatjuk az egykiszolgálós üzembe helyezési modell alapvető fogalmait. A rugalmas kiszolgáló telepítési módjával kapcsolatos további információkért lásd: a [rugalmas kiszolgálók áttekintése](./flexible-server/overview.md) és nagy kapacitású (Citus) áttekintése.

## <a name="overview"></a>Áttekintés

Az egyetlen kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely minimális követelményeket támaszt az adatbázis testreszabásához. Az egykiszolgálós platform úgy lett kialakítva, hogy az adatbázis-kezelési funkciók, például a javítások, a biztonsági mentések, a magas rendelkezésre állás, a biztonság minimális felhasználói konfigurációval és vezérléssel kezelhető legyen. Az architektúra úgy van optimalizálva, hogy 99,99%-os rendelkezésre állást biztosítson egyetlen rendelkezésre állási zónában. A PostgreSQL 9,5, 9,6, 10 és 11 közösségi verzióját támogatja. A szolgáltatás ma már széles körben elérhető az [Azure számos régiójában](https://azure.microsoft.com/global-infrastructure/services/).

Az egyetlen kiszolgáló a legjobb megoldás a Felhőbeli natív alkalmazásokhoz, amelyek az automatikus javítás kezelésére szolgálnak anélkül, hogy a javítási ütemterv és az egyéni PostgreSQL konfigurációs beállítások részletes szabályozására lenne szükség.

## <a name="high-availability"></a>Magas rendelkezésre állás

Az egykiszolgálós üzembe helyezési modell a beépített magas rendelkezésre állást és a rugalmasságot is csökkenti a költséghatékonyan. Az architektúra elkülöníti a számítási és a tárolási kapacitást. Az adatbázismotor egy szabadalmaztatott számítási tárolón fut, míg az adatfájlok az Azure Storage-ban találhatók. A tároló három helyileg redundáns szinkron példányt tart fenn az adatbázisfájlok számára az adattartósság biztosítása érdekében.

Tervezett vagy nem tervezett feladatátvételi események esetén, ha a kiszolgáló leáll, a szolgáltatás a következő automatizált eljárással biztosítja a kiszolgálók magas rendelkezésre állását:

1. Új számítási tároló van kiépítve.
2. Az adatfájlokkal rendelkező tároló az új tárolóra van leképezve.
3. A PostgreSQL-adatbázismotor online állapotba kerül az új számítási tárolón.
4. Az átjáró szolgáltatás biztosítja az átlátszó feladatátvételt, így nem szükséges az alkalmazás oldalának módosítása.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Egyetlen kiszolgáló Azure Database for PostgreSQL":::

A tipikus feladatátvételi idő 60-120 másodperc. Az egykiszolgálós szolgáltatás Felhőbeli natív kialakítása lehetővé teszi, hogy támogassa a rendelkezésre állás 99,99%-át, ami kiküszöböli a passzív készenléti állapot költségeit.

A Microsoft által felügyelt adatközpontok globális hálózata által működtetett Azure piacvezető 99,99%-os rendelkezésre állási szolgáltatói szerződése (SLA) segít megőrizni a 24/7-et futtató alkalmazásokat.

## <a name="automated-patching"></a>Automatikus javítás

A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A PostgreSQL motor esetében az alverziók frissítése automatikusan megtörténik, és a javítási ciklus részét képezi. Nincs szükség felhasználói műveletre vagy konfigurációs beállításra a javításhoz. A javítási gyakoriság a szolgáltatás által felügyelt, az adattartalom kritikus jellemzői alapján. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ. A felhasználók előfizethetnek a [tervezett karbantartási értesítésre]() , hogy értesítést kapjanak a közelgő karbantartási 72 órával az esemény előtt.

## <a name="automatic-backups"></a>Automatikus biztonsági mentések

Az egykiszolgálós szolgáltatás automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által konfigurált, helyileg redundáns (LRS) vagy földrajzilag redundáns tárolásban. A biztonsági mentések segítségével a kiszolgáló bármely időpontra visszaállítható a biztonsági mentés megőrzési ideje alatt. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Az adatmegőrzés opcionálisan 35 napig is konfigurálható. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva. Részletekért lásd: [biztonsági másolatok](./concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

Az egykiszolgálós szolgáltatás három SKU-szinten érhető el: alapszintű, általános célú és memória optimalizálva. Az alapszintű csomag a legjobb megoldás az alacsony költséghatékonyságú fejlesztéshez és az alacsony párhuzamosságú munkaterhelésekhez. Az optimalizált általános célú és memória jobban alkalmazkodik a magas párhuzamosságot, méretezést és kiszámítható teljesítményt igénylő éles munkaterhelésekhez. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A tárolási skálázás online állapotban van, és támogatja a tároló automatikus növekedését. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a felhasznált erőforrásokért kell fizetnie. A részleteket a [tarifacsomagok]() leírása tartalmazza.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Nagyvállalati szintű biztonság, megfelelőség és irányítás

Az egykiszolgálós szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adatok tárolási titkosításához. Az adatokat, beleértve a biztonsági másolatokat, valamint a lekérdezések futtatása közben létrehozott ideiglenes fájlokat is titkosítja a rendszer. A szolgáltatás az Azure Storage-titkosítás részét képező AES 256-bites titkosítást használja, és a kulcsokat rendszerfelügyelt (alapértelmezett) vagy az [ügyfél által felügyelt](). A szolgáltatás alapértelmezés szerint a Transport Layer Security (SSL/TLS) által kényszerített mozgásban lévő adatforgalom titkosítását végzi. A szolgáltatás a 1,2-es, 1,1-as és 1,0-es TLS-verziókat támogatja a [TLS minimális verziójának]()betartatására.

A szolgáltatás lehetővé teszi, hogy privát kapcsolaton keresztül hozzáférjenek a kiszolgálókhoz, és fejlett veszélyforrások elleni védelmet biztosítanak. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A natív hitelesítésen kívül az egyetlen kiszolgáló szolgáltatás támogatja a Azure Active Directory hitelesítést. Az Azure AD-hitelesítés olyan mechanizmus, amely a PostgreSQL-kiszolgálókhoz való csatlakozást az Azure AD-ben meghatározott és felügyelt identitások használatával végzi. Az Azure AD-hitelesítéssel egy központi helyen kezelheti az adatbázis-felhasználói identitásokat és más Azure-szolgáltatásokat, ami leegyszerűsíti és központosítja a hozzáférés-vezérlést.

Naplózási [naplózás]() (előzetes verzió) az összes adatbázis szintű tevékenység nyomon követésére szolgál.

Az egykiszolgálós szolgáltatás megfelel az összes piacvezető tanúsítványnak, mint például a FedRAMP, a HIPAA PCI DSS. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban]() talál információkat.

A Azure Database for PostgreSQL biztonsági funkcióival kapcsolatos további információkért tekintse meg a [Biztonság áttekintése]()című témakört.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Az egykiszolgálós szolgáltatás beépített teljesítmény-figyelési és riasztási funkciókkal rendelkezik. Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. A szolgáltatás lehetővé teszi a lassú lekérdezési naplók konfigurálását, és egy eltérő [lekérdezési tárolási](./concepts-query-store.md) funkcióval rendelkezik. A Query Store leegyszerűsíti a teljesítménnyel kapcsolatos hibaelhárítást, így gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. Ezen eszközök használatával gyorsan optimalizálhatja a számítási feladatokat, és konfigurálhatja a kiszolgálót a legjobb teljesítmény érdekében. További részletek: [figyelés](./concepts-monitoring.md) .

## <a name="migration"></a>Migrálás

A szolgáltatás a PostgreSQL közösségi verzióját futtatja. Ez lehetővé teszi az alkalmazások teljes kompatibilitását, és minimális újrabontási költségeket igényel a PostgreSQL-motoron az egykiszolgálós szolgáltatáshoz fejlesztett meglévő alkalmazások áttelepítéséhez. Az egyetlen kiszolgálóra történő áttelepítés a következő lehetőségek egyikével végezhető el:

- **Memóriakép és visszaállítás** – kapcsolat nélküli áttelepítések esetén, ahol a felhasználók bizonyos állásidőt, kiírást és visszaállítást biztosíthatnak olyan közösségi eszközök használatával, mint a Pg_dump és az Pg_restore a leggyorsabb áttelepítési módot biztosítanak. A részletekért lásd: az [áttelepítés a dump és a Restore használatával](./howto-migrate-using-dump-and-restore.md) .
- **Azure Database Migration Service** – az egykiszolgálós, minimális állásidővel rendelkező, zökkenőmentes és egyszerűsített áttelepítéshez Azure Database Migration Service is kihasználható. Lásd a DMS-t a [portálon](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) és a DMS-n [keresztül a parancssori](../dms/tutorial-postgresql-azure-postgresql-online.md)felületen

## <a name="contacts"></a>Kapcsolattartók

Ha bármilyen kérdése vagy javaslata van a Azure Database for PostgreSQL használatáról, küldjön e-mailt a Azure Database for PostgreSQL csapatnak ([ @Ask Az Azure-adatbázis PostgreSQL-hez](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ez az e-mail-cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Következő lépések

Most, hogy elolvasta Azure Database for PostgreSQL egykiszolgálós üzembe helyezési mód bevezetését, készen áll a következőre:
- Hozza létre az első kiszolgálót.
