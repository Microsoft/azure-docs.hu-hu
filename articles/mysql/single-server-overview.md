---
title: Áttekintés – egyetlen kiszolgáló Azure Database for MySQL
description: Ismerkedjen meg a MySQL Community Edition alapján a Microsoft Cloud-ban található, Azure Database for MySQL egyetlen kiszolgálóval.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: b0f8c5806ba22708db6dc537d391c1f1b1a183ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92543814"
---
# <a name="azure-database-for-mysql-single-server"></a>Önálló Azure Database for MySQL-kiszolgáló

A MySQL Community Edition [Azure Database for MySQL](overview.md) a két üzembe helyezési módban érhető el:
- Önálló kiszolgáló 
- Rugalmas kiszolgáló (előzetes verzió)

Ebben a cikkben áttekintjük és bemutatjuk az egykiszolgálós üzembe helyezési modell alapvető fogalmait. A rugalmas kiszolgáló telepítési módjával kapcsolatos további információkért tekintse meg a [rugalmas kiszolgálók áttekintése című témakört](flexible-server/index.yml). A számítási feladatokhoz megfelelő központi telepítési lehetőség eldöntéséről további információt a [megfelelő MySQL-kiszolgáló kiválasztása az Azure-ban](select-right-deployment-type.md)című témakörben talál.

## <a name="overview"></a>Áttekintés

Az egyetlen kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely minimális követelményeket támaszt az adatbázis testreszabásához. Az egykiszolgálós platform úgy lett kialakítva, hogy az adatbázis-kezelési funkciók, például a javítások, a biztonsági mentések, a magas rendelkezésre állás, a biztonság minimális felhasználói konfigurációval és vezérléssel kezelhető legyen. Az architektúra úgy van optimalizálva, hogy 99,99%-os rendelkezésre állást biztosítson egyetlen rendelkezésre állási zónában. A MySQL 5,6, 5,7 és 8,0 közösségi verzióját támogatja. A szolgáltatás ma már széles körben elérhető az [Azure számos régiójában](https://azure.microsoft.com/global-infrastructure/services/). 

Az egyetlen kiszolgáló a legjobb megoldás a Felhőbeli natív alkalmazásokhoz, amelyek az automatikus javítás kezelésére szolgálnak anélkül, hogy a javítási ütemterv és az egyéni MySQL konfigurációs beállítások részletes szabályozására lenne szükség. 

## <a name="high-availability"></a>Magas rendelkezésre állás

Az egykiszolgálós üzembe helyezési modell a beépített magas rendelkezésre állást és a rugalmasságot is csökkenti a költséghatékonyan. Az architektúra elkülöníti a számítási és a tárolási kapacitást. Az adatbázismotor egy szabadalmaztatott számítási tárolón fut, míg az adatfájlok az Azure Storage-ban találhatók. A tároló három helyileg redundáns szinkron példányt tart fenn az adatbázisfájlok számára az adattartósság biztosítása érdekében. 

Tervezett vagy nem tervezett feladatátvételi események esetén, ha a kiszolgáló leáll, a szolgáltatás a következő automatizált eljárással biztosítja a kiszolgálók magas rendelkezésre állását:

1. Új számítási tároló van kiépítve
2. Az adatfájlokkal rendelkező tároló az új tárolóra van leképezve 
3. A MySQL-adatbázismotor online állapotba kerül az új számítási tárolón
4. Az átjáró szolgáltatás biztosítja az átlátszó feladatátvételt, így nem szükséges az alkalmazás oldalának módosítása. 
  
A tipikus feladatátvételi idő 60-120 másodperc. Az egykiszolgálós szolgáltatás Felhőbeli natív kialakítása lehetővé teszi, hogy támogassa a rendelkezésre állás 99,99%-át, ami kiküszöböli a passzív készenléti állapot költségeit.

A Microsoft által felügyelt adatközpontok globális hálózata által működtetett Azure piacvezető 99,99%-os rendelkezésre állási szolgáltatói szerződése (SLA) segít megőrizni a 24/7-et futtató alkalmazásokat.

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Azure Database for MySQL egykiszolgálós architektúra fogalmi diagramja"::: 

## <a name="automated-patching"></a>Automatikus javítás 

A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A MySQL-motor esetében az alverziók frissítése automatikusan megtörténik, és a javítási ciklus részeként szerepel. Nincs szükség felhasználói műveletre vagy konfigurációs beállításra a javításhoz. A javítási gyakoriság a szolgáltatás által felügyelt, az adattartalom kritikus jellemzői alapján. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ. A felhasználók előfizethetnek a [tervezett karbantartási értesítésre](concepts-monitoring.md) , hogy értesítést kapjanak a közelgő karbantartási 72 órával az esemény előtt.

## <a name="automatic-backups"></a>Automatikus biztonsági mentések

Az egykiszolgálós szolgáltatás automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által konfigurált helyileg redundáns vagy földrajzilag redundáns tárolóban. A biztonsági mentések segítségével a kiszolgáló bármely időpontra visszaállítható a biztonsági mentés megőrzési ideje alatt. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Az adatmegőrzés opcionálisan 35 napig is konfigurálható. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva. További részletekért tekintse meg a [biztonsági mentéseket](concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

Az egykiszolgálós szolgáltatás három SKU-szinten érhető el: alapszintű, általános célú és memória optimalizálva. Az alapszintű csomag a legjobb megoldás az alacsony költséghatékonyságú fejlesztéshez és az alacsony párhuzamosságú munkaterhelésekhez. Az optimalizált általános célú és memória jobban alkalmazkodik a magas párhuzamosságot, méretezést és kiszámítható teljesítményt igénylő éles munkaterhelésekhez. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A tárolási skálázás online állapotban van, és támogatja a tárterület automatikus növekedését. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a felhasznált erőforrásokért kell fizetnie. A részleteket a [tarifacsomagok](./concepts-pricing-tiers.md) leírása tartalmazza.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Nagyvállalati szintű biztonság, megfelelőség és irányítás

Az egykiszolgálós szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adatok tárolási titkosításához. Az adatokat, beleértve a biztonsági másolatokat, valamint a lekérdezések futtatása közben létrehozott ideiglenes fájlokat is titkosítja a rendszer. A szolgáltatás az Azure Storage-titkosítás részét képező AES 256-bites titkosítást használja, és a kulcsokat rendszerfelügyelt (alapértelmezett) vagy az [ügyfél által felügyelt](concepts-data-encryption-mysql.md). A szolgáltatás alapértelmezés szerint a Transport Layer Security (SSL/TLS) által kényszerített mozgásban lévő adatforgalom titkosítását végzi. A szolgáltatás a 1,2-es, 1,1-as és 1,0-es TLS-verziókat támogatja a [TLS minimális verziójának](concepts-ssl-connection-security.md)betartatására. 

A szolgáltatás lehetővé teszi, hogy privát [kapcsolaton](concepts-data-access-security-private-link.md) keresztül hozzáférjenek a kiszolgálókhoz, és [fejlett veszélyforrások elleni védelmet](concepts-data-access-and-security-threat-protection.md) biztosítanak. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A natív hitelesítésen kívül az egyetlen kiszolgáló szolgáltatás támogatja a  [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) hitelesítést. Az Azure AD-hitelesítés olyan mechanizmus, amellyel a MySQL-kiszolgálókhoz kapcsolódhat az Azure AD-ben meghatározott és felügyelt identitások használatával. Az Azure AD-hitelesítéssel egy központi helyen kezelheti az adatbázis-felhasználói identitásokat és más Azure-szolgáltatásokat, ami leegyszerűsíti és központosítja a hozzáférés-vezérlést.

[A naplózás az összes](concepts-audit-logs.md) adatbázis-szintű tevékenység nyomon követésére használható. 

Az egykiszolgálós szolgáltatás az összes piacvezető tanúsítvány, például a FedRAMP, a HIPAA, a PCI DSS. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. 

A Azure Database for MySQL biztonsági funkcióival kapcsolatos további információkért tekintse meg a [Biztonság áttekintése](concepts-security.md)című témakört.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Az egykiszolgálós szolgáltatás beépített teljesítmény-figyelési és riasztási funkciókkal rendelkezik. Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. A szolgáltatás lehetővé teszi a lassú lekérdezési naplók konfigurálását, és egy eltérő [lekérdezési tárolási](concepts-query-store.md) funkcióval rendelkezik. A Query Store leegyszerűsíti a teljesítménnyel kapcsolatos hibaelhárítást, így gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. Ezen eszközök használatával gyorsan optimalizálhatja a számítási feladatokat, és konfigurálhatja a kiszolgálót a legjobb teljesítmény érdekében. További részletek: [figyelés](concepts-monitoring.md) .

## <a name="migration"></a>Áttelepítés

A szolgáltatás a MySQL közösségi verzióját futtatja. Ez lehetővé teszi az alkalmazások teljes kompatibilitását, és minimális újrabontási költségeket igényel a MySQL-motoron az egykiszolgálós szolgáltatáshoz fejlesztett meglévő alkalmazások áttelepítéséhez. Az egyetlen kiszolgálóra történő áttelepítés a következő lehetőségek egyikével végezhető el:

- **Memóriakép és visszaállítás** – kapcsolat nélküli áttelepítések esetén, ahol a felhasználók bizonyos állásidőt, kiírást és visszaállítást biztosíthatnak olyan közösségi eszközökkel, mint a mysqldump/mydumper, a lehető leggyorsabb módon telepíthetik az áttelepítést. A részletekért lásd: az [áttelepítés a dump és a Restore használatával](concepts-migrate-dump-restore.md) . 
- **Azure Database Migration Service** – az egykiszolgálós, minimális állásidővel rendelkező, zökkenőmentes és egyszerűsített áttelepítéshez [Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-online.md) is kihasználható. 
- **Adatreplikáció** – a minimális állásidő-áttelepítéshez, a BinLog-alapú replikációra támaszkodó, adatreplikálást is igénybe vehet. Az adatreplikálási szolgáltatás olyan gyakorlati szakértők által ajánlott minimális állásidő-áttelepítéseket részesíti előnyben, amelyek a Migrálás nagyobb mértékű felügyeletét keresik. Részletekért lásd: az [adatok replikálása](concepts-data-in-replication.md) .

## <a name="contacts"></a>Kapcsolattartók
Ha bármilyen kérdése vagy javaslata van a Azure Database for MySQL használatáról, küldjön e-mailt a Azure Database for MySQL csapatnak ([ @Ask a MySQL-hez készült Azure-adatbázis](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ez az e-mail-cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Következő lépések
Most, hogy elolvasta Azure Database for MySQL egykiszolgálós üzembe helyezési mód bevezetését, készen áll a következőre:

- Hozza létre az első kiszolgálót. 
  - [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Azure CLI-példák az Azure Database for MySQL-hez](sample-scripts-azure-cli.md)

- Hozza létre első alkalmazását a kívánt nyelv használatával:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Ugrás](./connect-go.md)
