---
title: Automatikus feladatátvételi csoportok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Az automatikus feladatátvételi csoportok lehetővé teszik a kiszolgálón található adatbázisok csoportjának vagy egy felügyelt példány összes adatbázisának replikálását és automatikus/koordinált feladatátvételét.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: f3bc1dfcfeeb6dda110f71ed7a1c53909153cf00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762154"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az automatikus feladatátvételi csoportok funkcióval kezelheti egy kiszolgálón található adatbáziscsoport vagy egy felügyelt példány összes adatbázisának replikációját és feladatátvételét egy másik régióba. Ez egy deklaratív absztrakció [](active-geo-replication-overview.md) a meglévő aktív georeplikációs szolgáltatáson felül, amelynek célja a georeplikált adatbázisok nagy léptékű üzembe helyezésének és felügyeletének egyszerűsítése. A feladatátvételt kezdeményezheti manuálisan, vagy delegálhatja azt az Azure-szolgáltatásba egy felhasználó által meghatározott szabályzat alapján. Az utóbbi lehetőség lehetővé teszi több kapcsolódó adatbázis automatikus helyreállítását egy másodlagos régióban egy katasztrofális hiba vagy más nem tervezett esemény után, amely az elsődleges régióban a SQL Database vagy az SQL Managed Instance rendelkezésre állásának teljes vagy részleges elvesztését okozhatja. Egy feladatátvételi csoport egy vagy több adatbázist is tartalmazhat, amelyet általában ugyanaz az alkalmazás használ. Emellett használhatja az olvasható másodlagos adatbázisokat is a csak olvasható lekérdezési számítási feladatok kiszervezéséhez. Mivel az automatikus feladatátvételi csoportok több adatbázist foglalnak magukban, ezeket az adatbázisokat az elsődleges kiszolgálón kell konfigurálni. Az automatikus feladatátvételi csoportok támogatják a csoportban található összes adatbázis replikálást egy másik régióban található másodlagos kiszolgálóra vagy példányra.

> [!NOTE]
> Ha több Azure SQL Database szeretne ugyanabban vagy különböző régiókban, használjon [aktív georeplikációt.](active-geo-replication-overview.md)

Ha automatikus feladatátvételi csoportokat használ automatikus feladatátvételi szabályzattal, a csoport egy vagy több adatbázisát esetlegesen befolyásoló kimaradások automatikus feladatátvételt fognak elérni. Ezek általában olyan incidensek, amelyek a beépített automatikus magas rendelkezésre állási műveletekkel nem mérsékelhetők. A feladatátvételi eseményindítók közé tartozik például egy olyan incidens, amelyet egy SQL Database-bérlői kör vagy vezérlőkarika azért okozott, mert az operációs rendszer kernelmemória-szivárgása miatt több számítási csomóponton is kiszivárogtak, vagy egy vagy több bérlői kör leállt, mert hibás hálózati kábel lett kivágva a rutinszerű hardverszerelés során.  További információkért lásd: [SQL Database magas rendelkezésre állás.](high-availability-sla.md)

Emellett az automatikus feladatátvételi csoportok írás-olvasási és írási figyelő végpontokat biztosítanak, amelyek a feladatátvétel során változatlanok maradnak. Akár manuális, akár automatikus feladatátvételi aktiválást használ, a feladatátvétel a csoportban található összes másodlagos adatbázist elsődlegesre váltja át. Az adatbázis feladatátvételének befejezése után a DNS-rekord automatikusan frissül, és átirányítja a végpontokat az új régióba. Az adott RPO- és RTO-adatokért [lásd: Az üzletmenet-folytonosság áttekintése.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

Ha automatikus feladatátvételi csoportokat használ automatikus feladatátvételi szabályzattal, a kiszolgálón vagy felügyelt példányon található adatbázisokra hatással van bármilyen kimaradás automatikus feladatátvételt is. Az automatikus feladatátvételi csoport a következővel kezelhető:

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: Feladatátvételi csoport](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Feladatátvételi csoport](scripts/add-database-to-failover-group-powershell.md)
- [REST API: Feladatátvételi csoport](/rest/api/sql/failovergroups)

A feladatátvétel után győződjön meg arról, hogy az adatbázis és a kiszolgáló vagy példány hitelesítési követelményei konfigurálva vannak az új elsődleges kiszolgálón. Részletekért lásd a [SQL Database utáni biztonsági beállításokat.](active-geo-replication-security-configure.md)

A valódi üzletmenet-folytonosság eléréséhez az adatbázisok adatközpontok közötti redundanciának hozzáadása csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes helyreállítása egy katasztrofális hiba után megköveteli a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását. Ilyen összetevők például az ügyfélszoftver (például egy egyéni JavaScriptet használó böngésző), a webes előoldalak, a tárhely és a DNS. Rendkívül fontos, hogy minden összetevő ellenálló legyen ugyanazokkal a hibákval szemben, és elérhetővé váljon az alkalmazás helyreállítási időre vonatkozó célkitűzése (RTO) alapján. Ezért azonosítania kell az összes függő szolgáltatást, és meg kell értenie az által nyújtott garanciákat és képességeket. Ezután megfelelő lépéseket kell tenni, hogy a szolgáltatás megfelelően működik-e azon szolgáltatások feladatátvétele során, amelyektől függ. További információ a vészhelyreállítási megoldások tervezésről: Felhőalapú megoldások tervezése vészhelyreállításhoz [aktív georeplikáció használatával.](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="terminology-and-capabilities"></a>Terminológia és képességek

- **Feladatátvételi csoport (FOG)**

  A feladatátvételi csoport olyan elnevezett adatbáziscsoport, amelyet egyetlen kiszolgáló vagy egy felügyelt példányon belül lehet kezelni, és amely egy egységként feladatátvételt tud egy másik régióba, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban található szolgáltatáskimaradás miatt. A létrehozáskor a SQL Managed Instance egy feladatátvételi csoport tartalmazza a példányban található összes felhasználói adatbázist, ezért egy példányon csak egy feladatátvételi csoport konfigurálható.
  
  > [!IMPORTANT]
  > A feladatátvételi csoport nevének globálisan egyedinek kell lennie a `.database.windows.net` tartományon belül.

- **Kiszolgálók**

     A kiszolgálókon a kiszolgálón található felhasználói adatbázisok egy része vagy egésze egy feladatátvételi csoportba helyezhető. A kiszolgálók emellett több feladatátvételi csoportot is támogatnak egyetlen kiszolgálón.

- **Elsődleges**

  A feladatátvételi csoportban található elsődleges adatbázisokat tartalmazó kiszolgáló vagy felügyelt példány.

- **Másodlagos**

  A feladatátvételi csoportban található másodlagos adatbázisokat tartalmazó kiszolgáló vagy felügyelt példány. A másodlagos nem lehet ugyanabban a régióban, mint az elsődleges.

- **Egyedi adatbázisok hozzáadása feladatátvételi csoporthoz**

  Több, ugyanazon a kiszolgálón található adatbázist is ugyanabban a feladatátvételi csoportban helyezzen el. Ha egyetlen adatbázist ad hozzá a feladatátvételi csoporthoz, az automatikusan létrehoz egy másodlagos adatbázist, amely ugyanazt a kiadást és számítási méretet használja a másodlagos kiszolgálón.  Ezt a kiszolgálót a feladatátvételi csoport létrehozásakor adott meg. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal a másodlagos kiszolgálón, a csoport örökli a georeplikációs hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, a rendszer létrehoz egy új másodlagos adatbázist a másodlagos kiszolgálón.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy a másodlagos kiszolgálónak nincs azonos nevű adatbázisa, kivéve, ha az egy meglévő másodlagos adatbázis. Az adatbázis feladatátvételi csoportjaiban SQL Managed Instance összes felhasználói adatbázis replikálva lesz. A feladatátvételi csoportban nem választhatja ki a felhasználói adatbázisok egy részkészletét a replikációhoz.

- **Adatbázisok hozzáadása rugalmas készlethez feladatátvételi csoporthoz**

  Egy rugalmas készletben az összes vagy több adatbázist is egy feladatátvételi csoportba helyezze. Ha az elsődleges adatbázis egy rugalmas készletben található, a másodlagos adatbázis automatikusan létrejön a rugalmas készletben ugyanazokkal a névvel (másodlagos készlet). Gondoskodnia kell arról, hogy a másodlagos kiszolgáló pontosan azonos nevű és elegendő szabad kapacitású rugalmas készletet tartalmaz a feladatátvételi csoport által létrehozott másodlagos adatbázisokhoz. Ha olyan adatbázist ad hozzá a készlethez, amely már rendelkezik másodlagos adatbázissal a másodlagos készletben, a csoport örökli a georeplikációs hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, a rendszer létrehoz egy új másodlagos adatbázist a másodlagos készletben.
  
- **Kezdeti vetés**

  Amikor adatbázisokat, rugalmas készleteket vagy felügyelt példányokat ad hozzá egy feladatátvételi csoporthoz, az adatreplikáció elindulása előtt kezdeti adatokat kell adatokat bevetni. A kezdeti átképzési fázis a leghosszabb és legdrágább művelet. A kezdeti adatokat a rendszer szinkronizálja, majd csak a későbbi adatváltozásokat replikálja. A kezdeti magok befejezéséhez szükséges idő az adatok méretétől, a replikált adatbázisok számától és a feladatátvételi csoportban található entitások közötti kapcsolat sebességétől függ. Normál körülmények között a lehetséges áttolási sebesség óránként legfeljebb 500 GB SQL Database, és legfeljebb 360 GB óránként SQL Managed Instance. A rendszer minden adatbázis esetében párhuzamosan végzi el az adatokat.

  A SQL Managed Instance vegye figyelembe a két példány közötti Express Route-kapcsolat sebességét a kezdeti kiinduló sebességet megcsatoláskor. Ha a két példány közötti kapcsolat sebessége lassabb, mint ami szükséges, a kezdőidő valószínűleg kihat. Az adatreplikáció kezdete előtt a kezdeti adatokat használhatja a kezdeti adatokat be- és leküldő sebességgel, az adatbázisok számával, az adatok teljes méretével és a kapcsolat sebességével. Egyetlen 100 GB-os adatbázis esetében például a kezdeti kezdőfázis körülbelül 1,2 órát fog igénybe venni, ha a hivatkozás képes óránként 84 GB-ot leküldni, és ha nincs más adatbázis átiratva. Ha a hivatkozás óránként csak 10 GB átvitelére képes, akkor egy 100 GB-os adatbázis átvitele körülbelül 10 órát fog igénybe venni. Ha több adatbázist kell replikálni, a rendszer párhuzamosan végrehajtja az adatokat, és ha lassú kapcsolati sebességgel kombinálja őket, a kezdeti átképzési fázis jelentősen tovább tarthat, különösen akkor, ha az összes adatbázisból származó adatok párhuzamos adatokat történő adatokat használ fel egyszerre, és meghaladja a rendelkezésre álló kapcsolati sávszélességet. Ha a két példány közötti hálózati sávszélesség korlátozott, és több felügyelt példányt ad hozzá egy feladatátvételi csoporthoz, fontolja meg több felügyelt példány egymás utáni hozzáadását a feladatátvételi csoporthoz, egyesével. A két felügyelt példány közötti megfelelő méretű átjáró-termékváltozat, és ha a vállalati hálózati sávszélesség ezt lehetővé teszi, óránként akár 360 GB-os sebességet is elérhet.  

- **DNS-zóna**

  Egy egyedi azonosító, amely automatikusan létrejön egy új SQL Managed Instance létrehozásakor. Ehhez a példányhoz többtartományos (SAN) tanúsítvány van kiépítve az ügyfélkapcsolatok hitelesítéséhez az ugyanabban a DNS-zónában található bármely példányhoz. Az ugyanabban a feladatátvételi csoportban található két felügyelt példánynak meg kell osztania a DNS-zónát.
  
  > [!NOTE]
  > A kiszolgálóhoz létrehozott feladatátvételi csoportokhoz nem szükséges DNS-zónaazonosító SQL Database.

- **Feladatátvételi csoport olvasási-írási figyelője**

  Egy DNS CNAME rekord, amely az aktuális elsődleges url-címére mutat. Automatikusan létrejön a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy az olvasási és írási számítási feladat transzparens módon újracsatlakozjon az elsődleges adatbázishoz, amikor a feladatátvételt követően az elsődleges adatbázis módosul. Amikor a feladatátvételi csoport létrejön egy kiszolgálón, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.database.windows.net` . Amikor a feladatátvételi csoport létrejön egy SQL Managed Instance, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.<zone_id>.database.windows.net` .

- **A feladatátvételi csoport csak olvasható figyelője**

  A létrehozott DNS CNAME-rekord a másodlagos URL-címére mutató csak olvasható figyelőre mutat. Automatikusan létrejön a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy a csak olvasható SQL számítási feladat transzparens módon csatlakozzon a másodlagos adatbázishoz a megadott terheléselosztási szabályok használatával. Amikor a feladatátvételi csoport létrejön egy kiszolgálón, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.secondary.database.windows.net` . Amikor a feladatátvételi csoport létrejön egy SQL Managed Instance, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Automatikus feladatátvételi szabályzat**

  Alapértelmezés szerint a feladatátvételi csoport automatikus feladatátvételi szabályzattal van konfigurálva. Az Azure a hiba észlelése és a türelmi időszak lejárta után aktiválja a feladatátvételt. A rendszernek ellenőriznie kell, hogy a beépített [](high-availability-sla.md) magas rendelkezésre állású infrastruktúra a hatás mértéke miatt nem tudja enyhíteni a kimaradást. Ha az alkalmazásból vagy manuálisan szeretné vezérelni a feladatátvételi munkafolyamatot, kikapcsolhatja az automatikus feladatátvételt.
  
  > [!NOTE]
  > Mivel a kimaradás méretének ellenőrzése és a kezelés gyors csökkentése emberi műveleteket foglal magában az üzemeltetési csapat számára, a türelmi időszak nem lehet egy óránál alacsonyabb. Ez a korlátozás a feladatátvételi csoport összes adatbázisára vonatkozik, függetlenül azok adatszinkronizálási állapotától.

- **Csak olvasási feladatátvételi szabályzat**

  Alapértelmezés szerint a csak olvasható figyelő feladatátvétele le van tiltva. Ez biztosítja, hogy a másodlagos kapcsolat nélküli állapotban ne befolyásolja az elsődleges kiszolgáló teljesítményét. Ez azonban azt is jelenti, hogy a csak olvasási munkamenetek nem tudnak csatlakozni a másodlagos munkamenet helyreállításáig. Ha a csak olvasási munkamenetek állásideje nem elfogadható, és használhatja az elsődlegeset mind csak olvasási, mind írási forgalomhoz, az elsődleges munkamenet lehetséges teljesítménycsökkenése rovására, a tulajdonság konfigurálásával engedélyezheti a feladatátvételt a csak olvasható figyelő `AllowReadOnlyFailoverToPrimary` számára. Ebben az esetben a rendszer automatikusan átirányítja a csak olvasható forgalmat az elsődlegesre, ha a másodlagos nem érhető el.

  > [!NOTE]
  > A tulajdonság csak akkor van érvényben, ha az automatikus feladatátvételi szabályzat engedélyezve van, és az Azure automatikus `AllowReadOnlyFailoverToPrimary` feladatátvételt aktivált. Ebben az esetben, ha a tulajdonság True (Igaz) értékre van állítva, az új elsődleges kiszolgáló olvasási-írási és csak olvasási munkameneteket is kiszolgál.

- **Tervezett feladatátvétel**

   A tervezett feladatátvétel teljes szinkronizálást végez az elsődleges és a másodlagos adatbázisok között, mielőtt a másodlagos átvált az elsődleges szerepkörre. Ez garantálja az adatvesztést. A tervezett feladatátvételt a következő esetekben használjuk:

  - Vészhelyreállítási (DR) próba végrehajtása éles környezetben, ha az adatvesztés nem elfogadható
  - Adatbázisok áthelyezése egy másik régióba
  - Az adatbázisok visszaadása az elsődleges régióba a kimaradás mérséklése után (feladat-visszavétel)

- **Nem tervezett feladatátvétel**

   A nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagos szerepkört az elsődleges szerepkörre az elsődlegesvel való szinkronizálás nélkül. Ez a művelet adatvesztést eredményez. A nem tervezett feladatátvétel helyreállítási módszerként használatos leállások esetén, ha az elsődleges kiszolgáló nem érhető el. Amikor az eredeti elsődleges adatbázis ismét online állapotba kerül, automatikusan újracsatlakozik szinkronizálás nélkül, és új másodlagossá válik.

- **Manuális feladatátvétel**

  A feladatátvételt bármikor kezdeményezheti manuálisan, az automatikus feladatátvételi konfigurációtól függetlenül. Ha az automatikus feladatátvételi szabályzat nincs konfigurálva, akkor manuális feladatátvételre van szükség a feladatátvételi csoportban található adatbázisok másodlagos helyre való helyreállításához. Kezdeményezhet kényszerített vagy felhasználóbarát feladatátvételt (teljes adatszinkronizálással). Az utóbbi használható az elsődleges régió másodlagos régióba való áthelyezésére. A feladatátvétel befejezésekor a DNS-rekordok automatikusan frissülnek az új elsődleges kiszolgálóval való kapcsolat biztosítása érdekében.

- **Türelmi időszak adatvesztéssel**

  Mivel az elsődleges és másodlagos adatbázisok aszinkron replikációval vannak szinkronizálva, a feladatátvétel adatvesztést eredményezhet. Az automatikus feladatátvételi szabályzatot testreszabhatja, hogy az alkalmazás adatvesztéssel szembeni tűrőképességét tükrözze. A konfigurálásával szabályozhatja, hogy mennyi ideig várjon a rendszer a feladatátvétel megkezdése előtt, ami valószínűleg `GracePeriodWithDataLossHours` adatvesztést eredményez.

- **Több feladatátvételi csoport**

  Ugyanazon kiszolgálópárhoz több feladatátvételi csoportot is konfigurálhat a feladatátvétel hatókörének szabályozása érdekében. Minden csoport külön-külön veszi át a rel. Ha a több-bérlős alkalmazás rugalmas készleteket használ, ezzel a képességgel az egyes készletekben található elsődleges és másodlagos adatbázisokat is keverheti. Így a kimaradások hatását a bérlőknek csak a felére csökkentheti.

  > [!NOTE]
  > SQL Managed Instance nem támogatja a több feladatátvételi csoportot.
  
## <a name="permissions"></a>Engedélyek

A feladatátvételi csoport engedélyei az [Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) kezelhetők.](../../role-based-access-control/overview.md) A [SQL Server Közreműködő](../../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör rendelkezik a feladatátvételi csoportok kezeléséhez szükséges összes engedéllyel.

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

Feladatátvételi csoport létrehozásához Azure RBAC írási hozzáférésre van szükség az elsődleges és a másodlagos kiszolgálókhoz, valamint a feladatátvételi csoportban található összes adatbázishoz. A SQL Managed Instance az elsődleges és a másodlagos SQL Managed Instance-hoz is Azure RBAC írási hozzáféréssel kell rendelkeznie, de az egyes adatbázisokra vonatkozó engedélyek nem relevánsak, mert az egyes SQL Managed Instance-adatbázisok nem adhatók hozzá a feladatátvételi csoportokhoz, és nem távolíthatók el abból.

### <a name="update-a-failover-group"></a>Feladatátvételi csoport frissítése

A feladatátvételi csoport frissítéséhez Azure RBAC írási hozzáféréssel kell hozzáférnie a feladatátvételi csoporthoz, valamint az aktuális elsődleges kiszolgálón vagy felügyelt példányon található összes adatbázishoz.  

### <a name="fail-over-a-failover-group"></a>Feladatátvételi csoport feladatátvétele

Feladatátvételi csoport feladatátvételéhez Azure RBAC írási hozzáférésre van szüksége a feladatátvételi csoporthoz az új elsődleges kiszolgálón vagy felügyelt példányon.

## <a name="best-practices-for-sql-database"></a>Ajánlott eljárások a SQL Database

Az automatikus feladatátvételi csoportot konfigurálni kell az elsődleges kiszolgálón, és egy másik Azure-régióban található másodlagos kiszolgálóhoz kell csatlakoztatni. A csoportok az összes vagy néhány adatbázist tartalmazhatják ezekben a kiszolgálókban. Az alábbi ábra egy georedundáns felhőalkalmazás több adatbázist és automatikus feladatátvételi csoportot használó tipikus konfigurációját mutatja be.

![A több adatbázist és automatikus feladatátvételi csoportot használó georedundáns felhőalkalmazás tipikus konfigurációját bemutató ábra.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Az [adatbázisnak a](failover-group-add-single-database-tutorial.md) SQL Database egy feladatátvételi csoporthoz való hozzáadását ismertető cikk részletes részletes oktatóanyagot tartalmaz, amelyből megtudhatja, hogyan adhat hozzá adatbázist SQL Database egy feladatátvételi csoporthoz.

Az üzletmenet folytonosságát szem előtt tartva az alábbi általános irányelveket kövesse:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Egy vagy több feladatátvételi csoport használata több adatbázis feladatátvételének kezeléséhez

Egy vagy több feladatátvételi csoport két kiszolgáló között is létre lehet hozva különböző régiókban (elsődleges és másodlagos kiszolgálók). Minden csoport tartalmazhat egy vagy több adatbázist, amelyek egy egységként lesznek helyreállítva arra az esetre, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban található kimaradás miatt. A feladatátvételi csoport az elsődleges céllal azonos szolgáltatási céllal hozza létre a földrajzilag másodlagos adatbázist. Ha meglévő georeplikációs kapcsolatot ad a feladatátvételi csoporthoz, győződjön meg arról, hogy a földrajzi másodlagos régió ugyanazokkal a szolgáltatási szinttel és számítási mérettel van konfigurálva, mint az elsődleges.
  
> [!IMPORTANT]
> A különböző előfizetések két kiszolgálója közötti feladatátvételi csoportok létrehozása jelenleg nem támogatott a Azure SQL Database. Ha a feladatátvételi csoport létrehozása után áthelyezi az elsődleges vagy másodlagos kiszolgálót egy másik előfizetésbe, az a feladatátvételi kérések és egyéb műveletek sikertelenségéhez vezethet.

### <a name="using-read-write-listener-for-oltp-workload"></a>Olvasási-írási listener használata OLTP számítási feladatokhoz

OLTP-műveletek végrehajtásakor használja a címet a kiszolgáló URL-címeként, és a rendszer automatikusan az elsődleges kiszolgálóhoz irányítja a `<fog-name>.database.windows.net` kapcsolatokat. Ez az URL-cím nem változik a feladatátvétel után. Vegye figyelembe, hogy a feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítése után lesznek átirányítva az új elsődleges kiszolgálóra.

### <a name="using-read-only-listener-for-read-only-workload"></a>Csak olvasható figyelő használata csak olvasható számítási feladatokhoz

Ha logikailag elkülönített, csak olvasható számítási feladat van, amely tűri az adatok bizonyos elavultságát, használhatja a másodlagos adatbázist az alkalmazásban. Csak olvasási munkamenetek esetén használja a címet kiszolgálói URL-címként, és a kapcsolat automatikusan `<fog-name>.secondary.database.windows.net` a másodlagoshoz lesz irányítva. Javasoljuk továbbá, hogy a kapcsolati sztringben a használatával jelezze az olvasási `ApplicationIntent=ReadOnly` szándékot.

> [!NOTE]
> A Prémium, üzletileg kritikus és Hyperscale szolgáltatási szinteken a SQL Database támogatja [](read-scale-out.md) a csak olvasható replikák használatát a csak olvasható lekérdezési számítási feladatok kiszervezéséhez a kapcsolati sztringben a paraméter `ApplicationIntent=ReadOnly` használatával. Ha georeplikált másodlagos példányt konfigurált, ezzel a képességgel csatlakozhat egy írásvédett replikához az elsődleges helyen vagy a georeplikált helyen is.
>
> - Az elsődleges helyen található írási replikához való csatlakozáshoz használja a és `ApplicationIntent=ReadOnly` a `<fog-name>.database.windows.net` kapcsolót.
> - Ha egy csak olvasható replikához szeretne csatlakozni a másodlagos helyen, használja a és `ApplicationIntent=ReadOnly` a `<fog-name>.secondary.database.windows.net` kapcsolót.

### <a name="preparing-for-performance-degradation"></a>Felkészülés a teljesítménycsökkenésre

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele csak az összetevők állapotától Azure SQL aktiválódik. Előfordulhat, hogy az elsődleges régióban található egyéb Azure-szolgáltatásokat nem érinti a szolgáltatáskimaradás, és az összetevőik továbbra is elérhetők lesznek ebben a régióban. Miután az elsődleges adatbázisok a dr. régióra váltottak, a függő összetevők közötti késés növekedhet. Annak érdekében, hogy elkerülhető legyen a nagyobb késés hatása az alkalmazás teljesítményére, gondoskodjon az alkalmazás összetevőinek redundanciéről a DR régióban, és kövesse ezeket a hálózati biztonsági [irányelveket.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Felkészülés az adatvesztésre

Ha a rendszer kimaradást észlel, az Azure megvárja a által megadott `GracePeriodWithDataLossHours` időszakot. Az alapértelmezett érték 1 óra. Ha nem engedheti meg magának az adatvesztést, ügyeljen arra, hogy kellően nagy számra, például `GracePeriodWithDataLossHours` 24 órára legyen beállítva. A másodlagosról az elsődlegesre való feladat-visszavételhez használjon manuális csoport-feladatátvételt.

> [!IMPORTANT]
> A 800 vagy kevesebb DTA-val és több mint 250 georeplikációt használó adatbázissal rendelkező rugalmas készletek olyan problémákba ütközhetnek, mint a hosszabb tervezett feladatátvételek és a csökkentett teljesítmény.  Ezek a problémák nagyobb valószínűséggel fordulnak elő az írásigényes számítási feladatoknál, ha a georeplikációs végpontokat földrajzi hely választja el széles körben, vagy ha több másodlagos végpontot használnak az egyes adatbázisokhoz.  A problémák tünetei akkor jelentkeznek, ha a georeplikáció késése idővel növekszik.  Ez a késés a használatával [sys.dm_geo_replication_link_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)  Ilyen problémák esetén a megoldások közé tartozik a készlet DTA-i számának növelése vagy az ugyanabban a készletben található georeplikált adatbázisok számának csökkentése.

### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

A változásütemezés szemléltetésére azt feltételezzük, hogy az A kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új másodlagos a harmadik régióban.  Az átváltáshoz kövesse az alábbi lépéseket:

1. Hozzon létre további bináris fájlokat az A kiszolgálón lévő adatbázisokról a C kiszolgálóra aktív [georeplikáció használatával.](active-geo-replication-overview.md) Az A kiszolgálón minden adatbázis két második, egy a B kiszolgálón, egy pedig a C kiszolgálón található. Ez garantálja az elsődleges adatbázisok védelmét az átváltás során.
2. Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőinek SQL-aliasa törölve lett, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
3. Hozza létre újra a feladatátvételi csoportot ugyanazokkal a névvel az A és A kiszolgálók között. Ezen a ponton a bejelentkezések leállnak.
4. Adja hozzá az összes elsődleges adatbázist az A kiszolgálón az új feladatátvételi csoporthoz.
5. A B kiszolgáló eldobás. A B adatbázisai automatikusan törlődnek.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

A változásütemezés szemléltetésére feltételezzük, hogy az A kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új elsődleges kiszolgáló a harmadik régióban.  Az átváltáshoz kövesse az alábbi lépéseket:

1. Végezzen el egy tervezett feladatátvételt, hogy az elsődleges kiszolgálót B kiszolgálóra váltsa. Az A kiszolgáló lesz az új másodlagos kiszolgáló. A feladatátvétel több perc leállást eredményezhet. A tényleges idő a feladatátvételi csoport méretétől függ.
2. Hozzon létre további bináris fájlokat a B kiszolgálón lévő adatbázisokról a C kiszolgálóra [aktív georeplikáció használatával.](active-geo-replication-overview.md) A B kiszolgálón minden adatbázis két második, egy az A kiszolgálón, egy pedig a C kiszolgálón található. Ez garantálja, hogy az elsődleges adatbázisok az átváltás során is védettek maradnak.
3. Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőinek SQL-aliasa törölve lett, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
4. Hozza létre újra a feladatátvételi csoportot ugyanazokkal a névvel a B és a C kiszolgálók között. Ezen a ponton a bejelentkezések leállnak.
5. Adja hozzá a B-ben az összes elsődleges adatbázist az új feladatátvételi csoporthoz.
6. Végezzen el egy tervezett feladatátvételt a feladatátvételi csoporton a B és C váltáshoz. Most a C kiszolgáló lesz az elsődleges, a B pedig a másodlagos. Az A kiszolgálón található összes másodlagos adatbázis automatikusan csatolva lesz a C-hez. Ahogy az 1. lépésben is, a feladatátvétel több perc leállást eredményezhet.
7. Az A kiszolgáló elejtve. Az A adatbázisai automatikusan törlődnek.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a listener végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más létrehoz egy azonos nevű feladatátvételi csoportot vagy kiszolgálói aliast, ami megakadályozza, hogy újra használni tudja. A kockázat minimalizálása érdekében ne használjon általános feladatátvételi csoportneveket.

## <a name="best-practices-for-sql-managed-instance"></a>Ajánlott eljárások a SQL Managed Instance

Az automatikus feladatátvételi csoportot az elsődleges példányon kell konfigurálni, és egy másik Azure-régióban található másodlagos példányhoz kell csatlakoznia.  A példányban lévő valamennyi adatbázis a másodlagos példányra lesz replikálva.

Az alábbi ábra egy georedundáns felhőalkalmazás felügyelt példányt és automatikus feladatátvételi csoportot használó tipikus konfigurációját mutatja be.

![automatikus feladatátvétel ábrája](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> A [felügyelt példány feladatátvételi](../managed-instance/failover-group-add-instance-tutorial.md) csoporthoz való hozzáadását ismertető cikk részletes, részletes oktatóanyagot tartalmaz, amelyből megtudhatja, hogyan adhat SQL Managed Instance a feladatátvételi csoporthoz.

Ha az alkalmazás SQL Managed Instance használ adatszintként, kövesse az alábbi általános irányelveket az üzletmenet folytonosságának kialakításához:

### <a name="creating-the-secondary-instance"></a>A másodlagos példány létrehozása

Annak érdekében, hogy a feladatátvétel után ne szakadjon meg a kapcsolat az elsődleges kiszolgálóval SQL Managed Instance az elsődleges és a másodlagos példánynak is ugyanabban a DNS-zónában kell lennie. Ez garantálja, hogy ugyanaz a többtartományos (SAN) tanúsítvány használható a feladatátvételi csoport két példányának ügyfélkapcsolatainak hitelesítésére. Amikor az alkalmazás készen áll az éles üzembe helyezésre, hozzon létre egy másodlagos SQL Managed Instance egy másik régióban, és győződjön meg arról, hogy a DNS-zóna meg van ásva az elsődleges SQL Managed Instance. Ezt a választható paraméter megadásával adhatja meg a létrehozás során. Ha a PowerShellt vagy a REST API, a választható paraméter neve , a parancsban a megfelelő nem kötelező mező neve pedig elsődleges felügyelt `DNS Zone Partner` Azure Portal lesz.

> [!IMPORTANT]
> Az alhálózatban létrehozott első felügyelt példány határozza meg a DNS-zónát az azonos alhálózaton található összes további példányhoz. Ez azt jelenti, hogy egy alhálózat két példánya nem lehet különböző DNS-zónákhoz tartozik.

További információ a másodlagos példány létrehozásáról SQL Managed Instance elsődleges példány dns-zónájában található: Másodlagos felügyelt [példány létrehozása.](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)

### <a name="using-geo-paired-regions"></a>Földrajzi párosított régiók használata

Mindkét felügyelt példányt üzembe [helyezheti a párosított régiókban](../../best-practices-availability-paired-regions.md) teljesítménybeli okokból. A földrajzi párosított régiókban található felügyelt példányok teljesítménye sokkal jobb, mint a ki nem fizetett régiókban. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Replikációs forgalom engedélyezése két példány között

Mivel minden példány a saját virtuális hálózatában van elkülönítve, a virtuális hálózatok közötti kétirányú forgalmat engedélyezve kell lennie. Lásd: [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Feladatátvételi csoport létrehozása a felügyelt példányok között eltérő előfizetésekben

Két különböző előfizetésben hozhat létre feladatátvételi csoportot a felügyelt SQL-példányok között, ha az előfizetések ugyanathoz a bérlőhöz [Azure Active Directory társítva.](../../active-directory/fundamentals/active-directory-whatis.md#terminology) A PowerShell API használata esetén ezt a másodlagos adatbázis paraméterének megadásával `PartnerSubscriptionId` SQL Managed Instance. A REST API paraméterben szereplő példányazonosítók saját subscriptionID azonosítóval `properties.managedInstancePairs` is használhatók.
  
> [!IMPORTANT]
> Azure Portal nem támogatja feladatátvételi csoportok létrehozását különböző előfizetések között. Emellett a különböző előfizetések és/vagy erőforráscsoportok meglévő feladatátvételi csoportjai esetében a feladatátvétel nem indítható el manuálisan a portálon keresztül az elsődleges SQL Managed Instance. Ehelyett azt a földrajzilag másodlagos példányból kell kezdeményeznie.

### <a name="managing-failover-to-secondary-instance"></a>Másodlagos példányra történő feladatátvétel kezelése

A feladatátvételi csoport a felügyelt SQL-példányban lévő összes adatbázis feladatátvételét kezelni fogja. Egy csoport létrehozásakor a rendszer automatikusan elvégzi a példány minden adatbázisának georeplikálását a másodlagos felügyelt SQL-példányra. A feladatátvételi csoportok nem használhatók az adatbázisok egy részhalmazának részleges feladatátvételéhez.

> [!IMPORTANT]
> Ha eltávolít egy adatbázist az elsődleges SQL Managed Instance, akkor az adatbázis is automatikusan el lesz távolítva a földrajzi másodlagos SQL Managed Instance.

### <a name="using-read-write-listener-for-oltp-workload"></a>Olvasási-írási figyelés használata OLTP számítási feladatokhoz

OLTP-műveletek végrehajtásakor használja a címet a kiszolgáló URL-címeként, és a kapcsolatok automatikusan `<fog-name>.zone_id.database.windows.net` az elsődlegeshez vannak irányítva. Ez az URL-cím nem változik a feladatátvétel után. A feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítése után lesznek átirányítva az új elsődleges kiszolgálóra. Mivel a másodlagos példány osztozik a DNS-zónán az elsődlegessel, az ügyfélalkalmazás újra tud majd csatlakozni hozzá ugyanazokkal a SAN-tanúsítvánnyal.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Csak olvasható listener használata a másodlagos példányhoz való csatlakozáshoz

Ha logikailag elkülönített, csak olvasható számítási feladat van, amely tűri az adatok bizonyos elavultságát, használhatja a másodlagos adatbázist az alkalmazásban. A georeplikált másodlagos adatbázishoz való közvetlen csatlakozáshoz használja a címet kiszolgálói URL-címként, és a kapcsolat közvetlenül a georeplikált `<fog-name>.secondary.<zone_id>.database.windows.net` másodlagos adatbázishoz csatlakozik.

> [!NOTE]
> A üzletileg kritikus szinten a SQL Managed Instance támogatja a csak [](read-scale-out.md) olvasható replikák használatát a csak olvasható lekérdezési számítási feladatok kiszervezéséhez a kapcsolati sztring `ApplicationIntent=ReadOnly` paraméterének használatával. Ha georeplikált másodlagos példányt konfigurált, ezzel a képességgel csatlakozhat egy írásvédett replikához az elsődleges helyen vagy a georeplikált helyen is.
>
> - Az elsődleges helyen található írási replikához való csatlakozáshoz használja a és `ApplicationIntent=ReadOnly` a `<fog-name>.<zone_id>.database.windows.net` kapcsolót.
> - Ha egy csak olvasható replikához szeretne csatlakozni a másodlagos helyen, használja a és `ApplicationIntent=ReadOnly` a `<fog-name>.secondary.<zone_id>.database.windows.net` kapcsolót.

### <a name="preparing-for-performance-degradation"></a>Felkészülés a teljesítménycsökkenésre

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele a feladatátvételi összetevők Azure SQL alapján aktiválódik. Előfordulhat, hogy az elsődleges régióban található többi Azure-szolgáltatást nem érinti a szolgáltatáskimaradás, és az összetevőik továbbra is elérhetők lesznek az ebben a régióban. Miután az elsődleges adatbázisok áttűnnek a másodlagos régióra, a függő összetevők közötti késés növekedhet. Annak érdekében, hogy elkerülhető legyen a nagyobb késés hatása az alkalmazás teljesítményére, gondoskodjon az alkalmazás összetevőinek redundanciéről a másodlagos régióban, és az adatbázissal együtt az alkalmazás összetevőinek feladatátvételét. Konfigurációkor kövesse a [hálózati biztonsági irányelveket](#failover-groups-and-network-security) a másodlagos régióban található adatbázishoz való csatlakozás biztosításához.

### <a name="preparing-for-data-loss"></a>Felkészülés az adatvesztésre

Ha a rendszer kimaradást észlel, legjobb tudásunk szerint írási/olvasási feladatátvétel lesz aktiválva, ha nincs adatvesztés. Ellenkező esetben a rendszer a használatával megadott időszakra késlelteti a `GracePeriodWithDataLossHours` feladatátvételt. Ha a értéket adja `GracePeriodWithDataLossHours` meg, készüljön fel az adatvesztésre. A szolgáltatáskimaradások során az Azure általában a rendelkezésre állást támogatja. Ha nem engedheti meg magának az adatvesztést, állítsa a GracePeriodWithDataLossHours tulajdonságot megfelelően nagy számra, például 24 órára, vagy tiltsa le az automatikus feladatátvételt.

Az olvasási-írási figyelő DNS-frissítése a feladatátvétel kezdeményezése után azonnal megtörténik. Ez a művelet nem eredményez adatvesztést. Az adatbázis-szerepkörök váltása normál körülmények között azonban akár 5 percet is igénybe vehet. Amíg be nem fejeződik, az új elsődleges példány egyes adatbázisai továbbra is csak olvashatók lesznek. Ha a feladatátvételt a PowerShell használatával indítja el, az elsődleges replikaszerepkronizálási művelet szinkron módon történik. Ha a folyamat a Azure Portal indul el, a felhasználói felület a befejezés állapotát jelzi. Ha az eljárást a REST API kezdeményezi, használja a standard Azure Resource Manager lekérdezési mechanizmusát a befejezés monitorozására.

> [!IMPORTANT]
> Manuális csoport-feladatátvétel használatával helyezze vissza az elsődleges helyhez a feladat-visszavételt. Ha a feladatátvételt okozó kimaradást enyhítik, az elsődleges adatbázisokat áthelyezheti az eredeti helyre. Ennek hez kezdeményezze a csoport manuális feladatátvételét.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

Tegyük fel, hogy az A példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig az új másodlagos példány a harmadik régióban.  Az átváltáshoz kövesse az alábbi lépéseket:

1. Hozza létre a C példányt az A-val azonos méretű és ugyanabban a DNS-zónában.
2. Törölje az A és b példányok közötti feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőinek SQL-aliasai törölve vannak, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az elsődleges adatbázisokról, és írási/olvasási adatbázisokká válnak.
3. Hozzon létre egy azonos nevű feladatátvételi csoportot az A és a C példány között. Kövesse a következő oktatóanyagban található [utasításokat: SQL Managed Instance.](../managed-instance/failover-group-add-instance-tutorial.md) Ez egy adatméret-művelet, amely akkor fejeződik be, ha az A példány összes adatbázisa át lesz edve és szinkronizálva van.
4. Ha nem szükséges, törölje a B példányt a szükségtelen díjak elkerülése érdekében.

> [!NOTE]
> A 2. lépés után és a 3. lépés befejezéséig az A példány adatbázisai nem védettek maradnak az A példány katasztrofális meghibásodása esetén.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

Tegyük fel, hogy az A példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig az új elsődleges példány a harmadik régióban.  Az átváltáshoz kövesse az alábbi lépéseket:

1. Hozza létre a C példányt a B-ével azonos méretű és ugyanabban a DNS-zónában.
2. Csatlakozzon a B példányhoz, és manuális feladatátvételt az elsődleges példány B példányra való váltáshoz. Az A példány automatikusan az új másodlagos példány lesz.
3. Törölje az A és b példányok közötti feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőinek SQL-aliasai törölve vannak, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az adatbázisokról, és írási/olvasási adatbázisokká válnak.
4. Hozzon létre egy azonos nevű feladatátvételi csoportot az A és C példány között. Kövesse a felügyelt példány oktatóanyagában található feladatátvételi csoport [utasításait.](../managed-instance/failover-group-add-instance-tutorial.md) Ez egy adatméret-művelet, amely akkor fejeződik be, ha az A példány összes adatbázisa fel lesz úsodva és szinkronizálva van.
5. Ha nem szükséges, törölje az A példányt a szükségtelen költségek elkerülése érdekében.

> [!CAUTION]
> A 3. lépés után és a 4. lépés befejezéséig az A példány adatbázisai nem védettek maradnak az A példány katasztrofális meghibásodása esetén.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a listener végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más létrehoz egy azonos nevű feladatátvételi csoportot vagy kiszolgálói aliast, ami megakadályozza, hogy újra felhasználja. A kockázat minimalizálása érdekében ne használjon általános feladatátvételi csoportneveket.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>A rendszeradatbázisok objektumaitól függő forgatókönyvek engedélyezése
A rendszeradatbázisok nem replikálódnak a feladatátvételi csoportban a másodlagos példányra. A rendszeradatbázisok objektumaitól függő forgatókönyvek engedélyezéséhez a másodlagos példányon is hozza létre ugyanezeket az objektumokat a másodlagoson. Ha például ugyanazt a bejelentkezést tervezi használni a másodlagos példányon, mindenképpen azonos SID azonosítóval hozza létre őket. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazások esetében a biztonsági szabályok megkövetelik, hogy az adatréteghez való hálózati hozzáférés egy adott összetevőre vagy összetevőkre, például virtuális gépre, webszolgáltatásra stb. korlátozódik. Ez a követelmény kihívást jelent az üzletmenet-folytonosság tervezése és a feladatátvételi csoportok használata szempontjából. Az ilyen korlátozott hozzáférés megvalósításakor vegye figyelembe a következő lehetőségeket.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és virtuális hálózati szabályok használata

Ha [szolgáltatásvégpontokat és](vnet-service-endpoint-rule-overview.md) szabályokat használ Virtual Network SQL Database-ban vagy SQL Managed Instance-ban az adatbázishoz való hozzáférés korlátozásához, vegye figyelembe, hogy minden virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi, hogy más régiók kommunikációt fogadjanak az alhálózatról. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások csatlakozhatnak az elsődleges adatbázishoz. Mivel a feladatátvétel az SQL Database-ügyfél-munkameneteket egy másik (másodlagos) régióban található kiszolgálóra átirányítja, ezek a munkamenetek sikertelenek lesznek, ha a munkamenetek a régión kívüli ügyfélről származnak. Emiatt az automatikus feladatátvételi szabályzat nem engedélyezhető, ha a részt vevő kiszolgálók vagy példányok szerepelnek a Virtual Network szabályokban. A manuális feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. Az alkalmazás előoldali összetevőinek redundáns másolatai (webszolgáltatás, virtuális gépek stb.) kiépítése a másodlagos régióban
2. A virtuális [hálózati szabályok konfigurálása egyenként](vnet-service-endpoint-rule-overview.md) az elsődleges és a másodlagos kiszolgálóhoz
3. Az [előoldali feladatátvétel engedélyezése Traffic Manager-konfigurációval](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Kezdeményezzen manuális feladatátvételt, ha a rendszer kimaradást észlel. Ez a beállítás olyan alkalmazásokhoz van optimalizálva, amelyek az előtere és az adatréteg közötti konzisztens késést igényelnek, és támogatják a helyreállítást, ha a szolgáltatáskimaradás hatással van az előtere, az adatrétegre vagy mindkettőre.

> [!NOTE]
> Ha a csak  olvasható figyelőt használja egy csak olvasható számítási feladat terhelésének elosztására, győződjön meg arról, hogy a számítási feladat végrehajtása a másodlagos régióban található virtuális gépen vagy más erőforrásban történik, hogy csatlakozni tudjon a másodlagos adatbázishoz.

### <a name="use-failover-groups-and-firewall-rules"></a>Feladatátvételi csoportok és tűzfalszabályok használata

Ha az üzletmenet-folytonossági terv automatikus feladatátvételt használó csoportok használatával igényel feladatátvételt, a hagyományos tűzfalszabályok használatával korlátozhatja a SQL Database adatbázishoz való hozzáférést. Az automatikus feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. [Nyilvános IP-cím létrehozása](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Hozzon létre egy nyilvános terheléselosztást,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) és rendelje hozzá a nyilvános IP-címet.
3. [Virtuális hálózat és virtuális gépek létrehozása](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) az előoldali összetevők számára
4. [Hozzon létre hálózati biztonsági csoportot,](../../virtual-network/network-security-groups-overview.md) és konfigurálja a bejövő kapcsolatokat.
5. Győződjön meg arról, hogy a kimenő kapcsolatok nyitva Azure SQL Database az "Sql" [szolgáltatáscímkével.](../../virtual-network/network-security-groups-overview.md#service-tags)
6. Hozzon [létre SQL Database tűzfalszabályt,](firewall-configure.md) amely engedélyezi az 1. lépésben létrehozott nyilvános IP-címről bejövő forgalmat.

További információ a kimenő hozzáférés konfigurálásról és a tűzfalszabályokban használt IP-címről: [Kimenő kapcsolatok terheléselosztása.](../../load-balancer/load-balancer-outbound-connections.md)

A fenti konfiguráció biztosítja, hogy az automatikus feladatátvétel ne blokkolja az előoldali összetevőktől származó kapcsolatokat, és feltételezi, hogy az alkalmazás képes tűrni az előlap és az adatréteg közötti hosszabb késést.

> [!IMPORTANT]
> A regionális kimaradások üzletmenet-folytonosságának garantálása érdekében biztosítania kell a földrajzi redundanciát az előoldali összetevők és az adatbázisok számára is.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>A georeplikáció engedélyezése felügyelt példányok és virtuális hálózataik között

Amikor két különböző régióban is beállít egy feladatátvételi csoportot az elsődleges és másodlagos felügyelt SQL-példányok között, minden példány független virtuális hálózattal lesz elkülönítve. A virtuális hálózatok közötti replikációs forgalom biztosításához győződjön meg a következő előfeltételekről:

- A két példánynak SQL Managed Instance különböző Azure-régiókban kell lennie.
- A két példánynak SQL Managed Instance szolgáltatási szintnek kell lennie, és azonos tárolási mérettel kell rendelkezik.
- Az adatbázis másodlagos SQL Managed Instance kell lennie (nincs felhasználói adatbázis).
- A példányok által használt virtuális hálózatokat SQL Managed Instance expressz útvonalon [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) [csatlakoztatni.](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Ha két virtuális hálózat egy helyszíni hálózaton keresztül csatlakozik, győződjön meg arról, hogy nem blokkolja tűzfalszabály az 5022-es és a 11000–11999-es portokat. A globális virtuális hálózatok közötti társviszony-létesítés támogatott, azonban az alábbi megjegyzésben található korlátozások vonatkoznak rá.

   > [!IMPORTANT]
   > [2020. 09. 22-én bejelentettük a globális virtuális hálózatok közötti társviszony-létesítést az újonnan létrehozott virtuális fürtökhöz](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ez azt jelenti, hogy a bejelentés dátumától a globális virtuális hálózatok közötti társviszony-létesítés támogatott az üres alhálózatokon létrehozott felügyelt SQL-példányok, valamint az ezeken az alhálózatokon létrehozott további felügyelt példányok esetén. A felügyelt SQL-példányok egyéb társviszony-létesítés támogatása az azonos régióban lévő hálózatokra korlátozódik a globális virtuális hálózatok közötti társviszony-létesítés [korlátai miatt.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) További részletekért tekintse meg az [Azure-beli](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) virtuális hálózatokra vonatkozó gyakori kérdések című cikk megfelelő szakaszát is. 

- A két SQL Managed Instance virtuális hálózat IP-címe nem lehet átfedésben.
- A hálózati biztonsági csoportokat (NSG) úgy kell beállítania, hogy az 5022-es portnak és a 11000–12000 közötti tartományba eső portoknak engedélyezni kell a másik felügyelt példány alhálózatával folytatott bejövő és kimenő forgalmat. Ezzel lehetővé teszi a virtuális hálózatok közötti replikációs forgalmat.

   > [!IMPORTANT]
   > A helytelenül konfigurált NSG-biztonsági szabályok elakadt adatbázis-másolási műveletekhez vezetnek.

- A másodlagos SQL Managed Instance a megfelelő DNS-zónaazonosítóval van konfigurálva. A DNS-zóna egy virtuális SQL Managed Instance mögöttes virtuális fürt egyik tulajdonsága, és az azonosítója szerepel az állomásnév címében. A zónaazonosító véletlenszerű sztringként jön létre, amikor az első SQL Managed Instance minden virtuális hálózatban létrejön, és ugyanazt az azonosítót rendeli hozzá a rendszer az azonos alhálózaton található összes többi példányhoz. Hozzárendelés után a DNS-zóna nem módosítható. Az ugyanabban a feladatátvételi csoportban található felügyelt SQL-példányok dns-zónáját meg kell osztani. Ezt úgy valósítja meg, hogy az elsődleges példány zónaazonosítóját a DnsZonePartner paraméter értékeként továbbítja a másodlagos példány létrehozásakor.

   > [!NOTE]
   > Részletes oktatóanyag a feladatátvételi csoportok konfigurálásához a SQL Managed Instance: Új SQL Managed Instance hozzáadása [egy feladatátvételi csoporthoz.](../managed-instance/failover-group-add-instance-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>Elsődleges adatbázis frissítése vagy visszalépése

Az elsődleges adatbázist a másodlagos adatbázisok leválasztása nélkül frissítheti vagy szűkítheti más számítási méretre (ugyanazon a szolgáltatási szinten, nem pedig általános célú és üzletileg kritikus között). Frissítéskor javasoljuk, hogy először frissítse az összes másodlagos adatbázist, majd frissítse az elsődleges adatbázist. Visszalépéskor fordítsa meg a sorrendet: először az elsődleges adatbázist, majd az összes másodlagos adatbázist vissza. Ha egy alacsonyabb vagy magasabb szolgáltatási szintre módosítja az adatbázist, a rendszer kikényszeríti ezt az ajánlást.

Ezt az ütemezést kifejezetten azért javasoljuk, hogy az alacsonyabb szintű termékváltozatban lévő másodlagos példány ne legyen túlterhelve, és a frissítés vagy lefokozási folyamat során ne kelljen új magot kapnia. A problémát úgy is elkerülheti, ha az elsődleges példányt írásvédetté teszi, de ez hatással lesz az összes írási és olvasási számítási feladatra az elsődleges példányon.

> [!NOTE]
> Ha a feladatátvételi csoport konfigurációjának részeként létrehozott egy másodlagos adatbázist, nem ajánlott a másodlagos adatbázis visszalépése. Ezzel biztosíthatja, hogy az adatszint elegendő kapacitással rendelkezik a normál számítási feladatok feldolgozásához a feladatátvétel aktiválása után.

## <a name="preventing-the-loss-of-critical-data"></a>Kritikus fontosságú adatok elvesztésének megakadályozása

A nagy területű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció elkerülhetetlenné teszi bizonyos adatvesztést hiba esetén. Egyes alkalmazások azonban nem igényelnek adatvesztést. A kritikus frissítések védelme érdekében az alkalmazásfejlesztők a tranzakció [véglegesítését](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) követően sp_wait_for_database_copy_sync a rendszer eljárását. A hívása blokkolja a hívó szálat, amíg a rendszer nem továbbítja az utolsó lekötött `sp_wait_for_database_copy_sync` tranzakciót a másodlagos adatbázisba. Nem vár azonban arra, hogy az átvitt tranzakciók visszajátssának és a másodlagos adatbázison való kötelezettséget vállalják. `sp_wait_for_database_copy_sync` A hatóköre egy adott folyamatos másolási hivatkozásra terjed ki. Ezt az eljárást bármely olyan felhasználó hívhatja meg, aki az elsődleges adatbázishoz rendelkezik kapcsolati jogosultságokkal.

> [!NOTE]
> `sp_wait_for_database_copy_sync` megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja az olvasási hozzáférés teljes szinkronizálását. Az eljáráshívás által okozott késés jelentős lehet, és a híváskor a tranzakciónapló méretétől `sp_wait_for_database_copy_sync` függ.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz való visszaállítás

További információ az időponthoz időben való visszaállítás feladatátvételi csoportokkal való használatával kapcsolatban: Időponthoz időbeni helyreállítás [(PITR).](recovery-using-backups.md#point-in-time-restore)

## <a name="limitations-of-failover-groups"></a>A feladatátvételi csoportok korlátozásai

Vegye figyelembe az alábbi korlátozásokat:

- Nem lehet feladatátvételi csoportokat létrehozni két kiszolgáló vagy példány között ugyanabban az Azure-régióban.
- A feladatátvételi csoportok nem nevezhetők át. Törölnie kell a csoportot, és újra létre kell hoznia egy másik névvel.
- A feladatátvételi csoportban a példányok nem támogatják az adatbázis-átnevezéseket. Az adatbázis átnevezéséhez ideiglenesen törölnie kell a feladatátvételi csoportot.
- A rendszeradatbázisok nem replikálódnak a feladatátvételi csoportban a másodlagos példányra. Ezért a rendszeradatbázisok objektumaitól függő forgatókönyvek lehetetlenek lesznek a másodlagos példányon, kivéve, ha az objektumok manuálisan vannak létrehozva a másodlagos példányon.

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok programozott kezelése

Ahogy korábban említettük, az automatikus feladatátvételi csoportok és az aktív georeplikáció programozott módon is kezelhetők a Azure PowerShell és a REST API. Az alábbi táblázatok ismertetik az elérhető parancsokat. Az aktív georeplikáció számos felügyeleti API Azure Resource Manager tartalmaz, [](/rest/api/sql/) beleértve a Azure SQL Database REST API és Azure PowerShell [parancsmagokat.](/powershell/azure/) Ezek az API-k erőforráscsoportok használatát igénylik, és támogatják az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC). További információ a hozzáférési szerepkörök megvalósításához: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/overview.md)

### <a name="manage-sql-database-failover"></a>A SQL Database kezelése

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Parancsmag | Leírás |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos kiszolgálón is|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Eltávolít egy feladatátvételi csoportot a kiszolgálóról |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekéri a feladatátvételi csoport konfigurációját |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Módosítja a feladatátvételi csoport konfigurációját |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Feladatátvételi csoport feladatátvételét váltja ki a másodlagos kiszolgálóra |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Hozzáad egy vagy több adatbázist egy feladatátvételi csoporthoz|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Parancs | Leírás |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos kiszolgálón is|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Eltávolít egy feladatátvételi csoportot a kiszolgálóról |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Lekéri a feladatátvételi csoport konfigurációját |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Módosítja a feladatátvételi csoport konfigurációját, és/vagy hozzáad egy vagy több adatbázist egy feladatátvételi csoporthoz|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Feladatátvételi csoport feladatátvételét váltja ki a másodlagos kiszolgálóra |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](/rest/api/sql/failovergroups/createorupdate) | Feladatátvételi csoportot hoz létre vagy frissíti |
| [Feladatátvételi csoport törlése](/rest/api/sql/failovergroups/delete) | Eltávolít egy feladatátvételi csoportot a kiszolgálóról |
| [Feladatátvétel (tervezett)](/rest/api/sql/failovergroups/failover) | Feladatátvételt indít el az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra teljes adatszinkronizálással.|
| [Adatvesztést engedélyező kényszerített feladatátvétel](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Feladatátvételt indít el az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra az adatok szinkronizálása nélkül. Ez a művelet adatvesztést eredményezhet. |
| [Feladatátvételi csoport levétele](/rest/api/sql/failovergroups/get) | Lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listába sorolás kiszolgáló szerint](/rest/api/sql/failovergroups/listbyserver) | Felsorolja a kiszolgálón található feladatátvételi csoportokat. |
| [Feladatátvételi csoport frissítése](/rest/api/sql/failovergroups/update) | Frissíti a feladatátvételi csoport konfigurációját. |

---

### <a name="manage-sql-managed-instance-failover"></a>A SQL Managed Instance kezelése


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Parancsmag | Leírás |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos példányon is|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Módosítja a feladatátvételi csoport konfigurációját|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Lekéri a feladatátvételi csoport konfigurációját|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport feladatátvételét váltja ki a másodlagos példányra|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Eltávolít egy feladatátvételi csoportot|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Parancs | Leírás |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos kiszolgálón is|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Eltávolít egy feladatátvételi csoportot a kiszolgálóról |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Lekéri a feladatátvételi csoport konfigurációját |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Módosítja a feladatátvételi csoport konfigurációját, és/vagy hozzáad egy vagy több adatbázist egy feladatátvételi csoporthoz|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Feladatátvételi csoport feladatátvételét váltja ki a másodlagos kiszolgálóra |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](/rest/api/sql/instancefailovergroups/createorupdate) | Létrehozza vagy frissíti a feladatátvételi csoport konfigurációját |
| [Feladatátvételi csoport törlése](/rest/api/sql/instancefailovergroups/delete) | Eltávolít egy feladatátvételi csoportot a példányból |
| [Feladatátvétel (tervezett)](/rest/api/sql/instancefailovergroups/failover) | Teljes adatszinkronizálással elindítja az aktuális elsődleges példányról erre a példányra való feladatátvételt. |
| [Adatvesztést engedélyező kényszerített feladatátvétel](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Az aktuális elsődleges példányról a másodlagos példányra való feladatátvételt váltja ki az adatok szinkronizálása nélkül. Ez a művelet adatvesztést eredményezhet. |
| [Feladatátvételi csoport levétele](/rest/api/sql/instancefailovergroups/get) | lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listája – List by Location](/rest/api/sql/instancefailovergroups/listbylocation) | Felsorolja a feladatátvételi csoportokat egy adott helyen. |

---

## <a name="next-steps"></a>Következő lépések

- Részletes oktatóanyagok:
  - [Új SQL Database hozzáadása feladatátvételi csoporthoz](failover-group-add-single-database-tutorial.md)
  - [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](failover-group-add-elastic-pool-tutorial.md)
  - [Új SQL Managed Instance hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md)
- Példaszk szkriptekért lásd:
  - [A PowerShell használatával konfigurálja az aktív georeplikációt a Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [A PowerShell használatával konfigurálhatja az aktív georeplikációt egy készletbe Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Feladatátvételi csoporthoz Azure SQL Database PowerShell használatával](scripts/add-database-to-failover-group-powershell.md)
- Az üzletmenet folytonosságának áttekintéséhez és a forgatókönyvekhez lásd: [Az üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- További információ az Azure SQL Database biztonsági mentések készítéséről: SQL Database [automatikus biztonsági mentések készítése.](automated-backups-overview.md)
- Az automatikus biztonsági mentések helyreállításhoz való használatával kapcsolatos további információkért lásd: Adatbázis visszaállítása a szolgáltatás által kezdeményezett [biztonsági másolatból.](recovery-using-backups.md)
- Az új elsődleges kiszolgálók és adatbázisok hitelesítési követelményeivel kapcsolatos további információkért lásd a SQL Database utáni [biztonsággal kapcsolatos cikkeket.](active-geo-replication-security-configure.md)
