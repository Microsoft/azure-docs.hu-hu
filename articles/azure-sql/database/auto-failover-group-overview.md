---
title: Automatikus feladatátvételi csoportok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Az automatikus feladatátvételi csoportok lehetővé teszik a kiszolgálók vagy a felügyelt példányok összes adatbázisának replikálását, valamint az automatikus/koordinált feladatátvétel kezelését.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/26/2020
ms.openlocfilehash: e0b9eea7be97b9b67e75c314c4a1d9e69322e5b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594257"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az automatikus feladatátvételi csoportok funkció lehetővé teszi, hogy kezelje az adatbázisok egy csoportjának replikálását és feladatátvételét egy kiszolgálón vagy egy felügyelt példány összes adatbázisán egy másik régióba. A meglévő [aktív geo-replikálási](active-geo-replication-overview.md) szolgáltatás egyik deklaratív absztrakciója, amely a földrajzilag replikált adatbázisok nagy léptékű üzembe helyezésének és kezelésének egyszerűsítésére szolgál. A feladatátvételt manuálisan is kezdeményezheti, vagy delegálhatja az Azure-szolgáltatásnak egy felhasználó által definiált házirend alapján. Az utóbbi lehetőség lehetővé teszi, hogy egy súlyos hiba vagy más nem tervezett esemény után automatikusan helyreállítson több kapcsolódó adatbázist egy másodlagos régióban, ami az elsődleges régióban a SQL Database vagy az SQL felügyelt példányának teljes vagy részleges elvesztését eredményezi. A feladatátvételi csoportok tartalmazhatnak egy vagy több adatbázist, jellemzően ugyanazt az alkalmazást használják. Emellett az olvasható másodlagos adatbázisokat is használhatja az írásvédett lekérdezési feladatok kiszervezéséhez. Mivel az automatikus feladatátvételi csoportok több adatbázist is tartalmaznak, ezeket az adatbázisokat az elsődleges kiszolgálón kell konfigurálni. Az automatikus feladatátvételi csoportok támogatják a csoport összes adatbázisának replikálását egyetlen másodlagos kiszolgálóra vagy példányra egy másik régióban.

> [!NOTE]
> Ha több Azure SQL Database formátumú másodlagos zónák szeretne ugyanabban vagy különböző régiókban, használja az [aktív földrajzi replikálást](active-geo-replication-overview.md).

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, a csoport egy vagy több adatbázisára hatással lévő kimaradások automatikusan feladatátvételt eredményeznek. Ezek általában olyan incidensek, amelyeket a beépített automatikus magas rendelkezésre állású műveletek nem képesek önállóan elhárítani. A feladatátvételi eseményindítók példái közé tartozik egy SQL Database bérlői kör vagy vezérlési kör okozta incidens, mert egy operációsrendszer-kernel memóriavesztés miatt több számítási csomóponton van, vagy egy vagy több bérlői csengetés okozta incidens, mert a hardver leszerelése során helytelen hálózati kábel lett kivágva.  További információ: [SQL Database magas rendelkezésre állás](high-availability-sla.md).

Az automatikus feladatátvételi csoportok emellett olyan írási és olvasási figyelőket is biztosítanak, amelyek változatlanok maradnak a feladatátvételek során. Akár kézi, akár automatikus feladatátvételi aktiválást használ, a feladatátvétel a csoportban lévő összes másodlagos adatbázist elsődlegesre váltja. Az adatbázis-feladatátvétel befejeződése után a rendszer automatikusan frissíti a DNS-rekordot, hogy átirányítsa a végpontokat az új régióba. Az adott RPO-és RTO-információk esetében lásd: [az üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, a kiszolgálók vagy a felügyelt példányok adatbázisait érintő kimaradások automatikus feladatátvételt eredményeznek. Az automatikus feladatátvételi csoportot az alábbiak szerint kezelheti:

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: feladatátvételi csoport](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: feladatátvételi csoport](scripts/add-database-to-failover-group-powershell.md)
- [REST API: feladatátvételi csoport](/rest/api/sql/failovergroups)

A feladatátvételt követően győződjön meg arról, hogy az adatbázis és a kiszolgáló, illetve a példány hitelesítési követelményei az új elsődleges kiszolgálón vannak konfigurálva. Részletekért lásd: [SQL Database biztonság a katasztrófa utáni helyreállítás után](active-geo-replication-security-configure.md).

A valós Üzletmenet-folytonosság eléréséhez az adatközpontok közötti adatbázis-redundancia csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása végzetes hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ezen összetevők közé tartoznak például az ügyfélszoftverek (például egy egyéni JavaScripttel rendelkező böngésző), a webes kezelőfelületek, a tárterület és a DNS. Kritikus fontosságú, hogy minden összetevő azonos hibákra legyen rugalmas, és az alkalmazás helyreállítási idő célkitűzésén (RTO) belül elérhetővé váljon. Ezért az összes függő szolgáltatást azonosítania kell, és ismernie kell az általuk nyújtott garanciákat és képességeket. Ezt követően meg kell tennie a megfelelő lépéseket annak biztosításához, hogy a szolgáltatás a szolgáltatások feladatátvétele alatt működjön, amelytől függ. A megoldásoknak a vész-helyreállításhoz való kialakításával kapcsolatos további információkért lásd: [felhőalapú megoldások tervezése a vész-helyreállításhoz az aktív földrajzi replikálás használatával](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminológia és képességek

- **Feladatátvételi csoport (köd)**

  A feladatátvételi csoport egyetlen kiszolgáló vagy egy felügyelt példány által kezelt adatbázisok névvel ellátott csoportja, amely egy egységként egy másik régióba helyezi át a feladatátvételt, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régió meghibásodása miatt. Az SQL felügyelt példányhoz való létrehozásakor a feladatátvételi csoport a példány összes felhasználói adatbázisát tartalmazza, ezért csak egy feladatátvételi csoport konfigurálható egy példányon.
  
  > [!IMPORTANT]
  > A feladatátvételi csoport nevének globálisan egyedinek kell lennie a `.database.windows.net` tartományon belül.

- **Kiszolgálók**

     A kiszolgálók esetében a kiszolgálón található felhasználói adatbázisok némelyike vagy mindegyike feladatátvételi csoportba helyezhető. Emellett a kiszolgálók több feladatátvételi csoportot is támogatnak egyetlen kiszolgálón.

- **Elsődleges**

  A feladatátvételi csoportban lévő elsődleges adatbázisokat üzemeltető kiszolgáló vagy felügyelt példány.

- **Másodlagos**

  A feladatátvételi csoportban található másodlagos adatbázisokat üzemeltető kiszolgáló vagy felügyelt példány. A másodlagos nem lehet ugyanabban a régióban, mint az elsődleges.

- **Önálló adatbázisok hozzáadása a feladatátvételi csoporthoz**

  Ugyanazon a kiszolgálón több önálló adatbázis is elhelyezhető ugyanarra a feladatátvételi csoportba. Ha egyetlen adatbázist ad hozzá a feladatátvételi csoporthoz, a automatikusan létrehoz egy másodlagos adatbázist ugyanazzal a kiadással és számítási mérettel a másodlagos kiszolgálón.  A kiszolgálót a feladatátvételi csoport létrehozásakor adta meg. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal a másodlagos kiszolgálón, a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, akkor a rendszer egy új másodlagost hoz létre a másodlagos kiszolgálón.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy a másodlagos kiszolgálónak nincs azonos nevű adatbázisa, kivéve, ha egy meglévő másodlagos adatbázis. Az SQL felügyelt példányának feladatátvételi csoportjaiban az összes felhasználói adatbázis replikálódik. A feladatátvételi csoportban nem választhat felhasználói adatbázisok egy részhalmazát.

- **Adatbázisok hozzáadása rugalmas készletből a feladatátvételi csoportba**

  Egy rugalmas készletben lévő összes vagy több adatbázis ugyanabba a feladatátvételi csoportba helyezhető. Ha az elsődleges adatbázis egy rugalmas készletben van, a rendszer automatikusan létrehozza a másodlagost a rugalmas készletben ugyanazzal a névvel (másodlagos készlettel). Győződjön meg arról, hogy a másodlagos kiszolgáló tartalmaz egy rugalmas készletet ugyanazzal a pontos névvel és elegendő szabad kapacitással a feladatátvételi csoport által létrehozandó másodlagos adatbázisok üzemeltetéséhez. Ha olyan adatbázist ad hozzá a készlethez, amely már rendelkezik másodlagos adatbázissal a másodlagos készletben, akkor a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, a rendszer egy új másodlagost hoz létre a másodlagos készletben.
  
- **Kezdeti előkészítés**

  Adatbázisok, rugalmas készletek vagy felügyelt példányok feladatátvételi csoportba való felvételekor az adatreplikáció elindítása előtt kezdeti előkészítési fázis van. A kezdeti előkészítési fázis a leghosszabb és legdrágább művelet. A kezdeti beültetés befejezése után a rendszer szinkronizálja az adatokat, majd csak a további adatváltozásokat replikálja. A kezdeti vetõmag befejezéséhez szükséges idő az adatok méretétől, a replikált adatbázisok számától, valamint a feladatátvételi csoportban lévő entitások közötti kapcsolat sebességétől függ. Normál körülmények között a lehetséges kivetési sebesség akár 500 GB SQL Database, és akár 360 GB is lehet egy óra az SQL felügyelt példányához. A rendszer az összes adatbázis párhuzamos előkészítését végzi.

  SQL felügyelt példány esetén vegye figyelembe az expressz útvonal kapcsolatának sebességét a két példány között a kezdeti előkészítési fázis idejének becslése során. Ha a két példány közötti kapcsolat sebessége lassabb a szükségesnél, a magokra adott idő valószínűleg különösen hatással van. Felhasználhatja a megadott előkészítési sebességet, az adatbázisok számát, az adatmennyiség teljes méretét, valamint a kapcsolat sebességét annak becsléséhez, hogy mennyi ideig tart a kezdeti előkészítési fázis az adatreplikálás megkezdése előtt. Egy 100 GB-os adatbázis esetében például a kezdeti vetőmag fázisa körülbelül 1,2 órát vesz igénybe, ha a hivatkozás 84 GB/óra lenyomására képes, és ha nincs más adatbázis bevetése. Ha a hivatkozás csak 10 GB/óra átvitelt tud végezni, akkor a 100 GB-os adatbázis kivetése körülbelül 10 órát vesz igénybe. Ha több adatbázis replikálására van szükség, a rendszer párhuzamosan hajtja végre a bevezetést, és ha lassú kapcsolati sebességgel kombinálja, a kezdeti előkészítési fázis jóval hosszabb időt is igénybe vehet, különösen akkor, ha az összes adatbázisból származó adatok párhuzamos kiosztása meghaladja a rendelkezésre álló kapcsolat sávszélességét. Ha a két példány közötti hálózati sávszélesség korlátozott, és több felügyelt példányt ad hozzá egy feladatátvételi csoporthoz, érdemes egymás után több felügyelt példányt hozzáadni a feladatátvételi csoporthoz. Ha a két felügyelt példány között megfelelő méretű átjáró SKU-t adott meg, és ha a vállalati hálózati sávszélesség lehetővé teszi, a sebességet 360 GB-os sebességgel lehet elérni.  

- **DNS-zóna**

  Egyedi azonosító, amely automatikusan létrejön egy új, felügyelt SQL-példány létrehozásakor. A példányhoz több tartományra kiterjedő (SAN) tanúsítvány van kiépítve, amely az ügyfélkapcsolatokat az azonos DNS-zónában lévő bármelyik példánnyal hitelesíti. Az ugyanabban a feladatátvételi csoportban lévő két felügyelt példánynak meg kell osztania a DNS-zónát.
  
  > [!NOTE]
  > A SQL Databasehoz létrehozott feladatátvételi csoportokhoz nem szükséges DNS-zóna azonosítója.

- **Feladatátvételi csoport írási-olvasási figyelője**

  DNS-CNAME rekord, amely az aktuális elsődleges URL-címre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy az olvasási és írási feladat transzparens módon újrakapcsolódjon az elsődleges adatbázishoz, amikor az elsődleges módosítások a feladatátvételt követően változnak. Ha a feladatátvételi csoport egy kiszolgálón jön létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.database.windows.net` . Ha a feladatátvételi csoportot egy SQL felügyelt példányon hozza létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.<zone_id>.database.windows.net` .

- **Feladatátvételi csoport írásvédett figyelője**

  Egy olyan DNS-CNAME rekord, amely a másodlagos URL-címére mutató írásvédett figyelőre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy a csak olvasási jogosultsággal rendelkező SQL-munkaterhelés transzparens módon kapcsolódjon a másodlagoshoz a megadott terheléselosztási szabályok használatával. Ha a feladatátvételi csoport egy kiszolgálón jön létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.secondary.database.windows.net` . Ha a feladatátvételi csoportot egy SQL felügyelt példányon hozza létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a következő lesz: `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Automatikus feladatátvételi szabályzat**

  Alapértelmezés szerint a feladatátvételi csoport automatikus feladatátvételi házirenddel van konfigurálva. Az Azure elindítja a feladatátvételt a hiba észlelése után, és a türelmi időszak lejárt. A rendszernek ellenőriznie kell, hogy a kiesést nem lehet enyhíteni a beépített [magas rendelkezésre állású infrastruktúra](high-availability-sla.md) által a hatás skálázása miatt. Ha a feladatátvételi munkafolyamatot az alkalmazásból vagy manuálisan szeretné vezérelni, akkor kikapcsolhatja az automatikus feladatátvételt.
  
  > [!NOTE]
  > Mivel a leállás skálázásának ellenőrzése és az, hogy milyen gyorsan lehet enyhíteni az operatív csapat által végzett emberi műveleteket, a türelmi időszak nem állítható be egy óra alatt. Ez a korlátozás a feladatátvételi csoport összes adatbázisára vonatkozik, az adatszinkronizálási állapotuktól függetlenül.

- **Csak olvasási feladatátvételi szabályzat**

  Alapértelmezés szerint a csak olvasási figyelő feladatátvétele le van tiltva. Gondoskodik arról, hogy az elsődleges teljesítmény ne legyen hatással a másodlagos kapcsolat nélküli állapotra. Ez azonban azt is jelenti, hogy a csak olvasási munkamenetek nem fognak tudni csatlakozni, amíg a másodlagos helyre nem kerül. Ha nem tudja elviselni az állásidőt a csak olvasási munkamenetek esetében, és az elsődlegest is használhatja mind a írásvédett, mind az írási és olvasási forgalomhoz, akkor a tulajdonság konfigurálásával engedélyezheti a feladatátvételt a csak olvasási figyelő számára `AllowReadOnlyFailoverToPrimary` . Ebben az esetben a csak olvasási forgalom automatikusan átirányítva lesz az elsődlegesre, ha a másodlagos nem érhető el.

  > [!NOTE]
  > A `AllowReadOnlyFailoverToPrimary` tulajdonság csak akkor érvényes, ha az automatikus feladatátvételi házirend engedélyezve van, és az Azure egy automatikus feladatátvételt váltott ki. Ebben az esetben, ha a tulajdonság értéke TRUE (igaz), az új elsődleges az írási és olvasási munkameneteket is fogja szolgálni.

- **Tervezett feladatátvétel**

   A tervezett feladatátvétel teljes szinkronizálást végez az elsődleges és a másodlagos adatbázisok között, mielőtt a másodlagos kapcsolók az elsődleges szerepkörre kerülnek. Ez nem garantálja az adatvesztést. A tervezett feladatátvételt a következő esetekben használja a rendszer:

  - Vész-helyreállítási (DR) gyakorlatok készítése az éles környezetben, ha az adatvesztés nem fogadható el
  - Adatbázisok áthelyezése másik régióba
  - Az adatbázisok visszaküldése az elsődleges régióba a leállás csökkentése után (feladat-visszavétel)

- **Nem tervezett feladatátvétel**

   A nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagost az elsődleges szerepkörre anélkül, hogy szinkronizálnia kellene az elsődlegesével. A művelet adatvesztést eredményezhet. A nem tervezett feladatátvétel helyreállítási módszerként használatos a leállás során, ha az elsődleges nem érhető el. Amikor az eredeti elsődleges ismét online állapotba kerül, automatikusan újrakapcsolódik a szinkronizálás nélkül, és új másodlagos lesz.

- **Manuális feladatátvétel**

  A feladatátvételt manuálisan is kezdeményezheti az automatikus feladatátvételi konfigurációtól függetlenül. Ha az automatikus feladatátvételi házirend nincs konfigurálva, a feladatátvételi csoportban lévő adatbázisok másodlagosra történő helyreállításához manuális feladatátvétel szükséges. Kényszerített vagy felhasználóbarát feladatátvételt is kezdeményezhet (teljes adatszinkronizálással). Az utóbbit az elsődlegesnek a másodlagos régióba való áthelyezésére lehet használni. A feladatátvétel befejezésekor a rendszer automatikusan frissíti a DNS-rekordokat, hogy biztosítsa az új elsődleges kapcsolat elérését.

- **Türelmi időszak adatvesztéssel**

  Mivel az elsődleges és másodlagos adatbázisok aszinkron replikálással vannak szinkronizálva, a feladatátvétel adatvesztést okozhat. Testreszabhatja az automatikus feladatátvételi szabályzatot, hogy tükrözze az alkalmazás adatvesztési tűréshatárát. A konfigurálásával `GracePeriodWithDataLossHours` szabályozhatja, hogy a rendszer mennyi ideig várjon, mielőtt elindítja a feladatátvételt, amely valószínűleg adatvesztést eredményezhet.

- **Több feladatátvételi csoport**

  Több feladatátvételi csoportot is beállíthat ugyanahhoz a kiszolgálóhoz a feladatátvételi hatókör szabályozása érdekében. A csoportok egymástól függetlenül hajtják végre a feladatátvételt. Ha a több-bérlős alkalmazás rugalmas készleteket használ, ezt a képességet használhatja az egyes készletekben lévő elsődleges és másodlagos adatbázisok összekeveréséhez. Így csökkentheti a leállás hatását a bérlők felére.

  > [!NOTE]
  > Az SQL felügyelt példánya nem támogat több feladatátvételi csoportot.
  
## <a name="permissions"></a>Engedélyek

A feladatátvételi csoport engedélyei az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)használatával kezelhetők. A [SQL Server közreműködő](../../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör rendelkezik a feladatátvételi csoportok kezeléséhez szükséges engedélyekkel.

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

Feladatátvételi csoport létrehozásához az Azure RBAC írási hozzáféréssel kell rendelkeznie az elsődleges és a másodlagos kiszolgálókhoz, valamint a feladatátvételi csoport összes adatbázisához. SQL felügyelt példány esetében az Azure RBAC írási hozzáférésre van szükség az elsődleges és másodlagos SQL felügyelt példányhoz, de az egyes adatbázisokra vonatkozó engedélyek nem relevánsak, mert az egyes SQL felügyelt példány-adatbázisok nem vehetők fel és nem távolíthatók el egy feladatátvételi csoportból.

### <a name="update-a-failover-group"></a>Feladatátvételi csoport frissítése

A feladatátvételi csoport frissítéséhez az Azure RBAC írási hozzáférésre van szüksége a feladatátvételi csoporthoz, valamint az aktuális elsődleges kiszolgálón vagy felügyelt példányon található összes adatbázishoz.  

### <a name="fail-over-a-failover-group"></a>Feladatátvételi csoport feladatátvétele

Feladatátvevő csoport feladatátvételéhez az Azure RBAC írási hozzáférésre van szüksége az új elsődleges kiszolgálón vagy felügyelt példányon található feladatátvételi csoport számára.

## <a name="best-practices-for-sql-database"></a>Ajánlott eljárások SQL Database

Az automatikus feladatátvételi csoportot az elsődleges kiszolgálón kell konfigurálni, és egy másik Azure-régióban található másodlagos kiszolgálóhoz kell csatlakoznia. A csoportok tartalmazhatják a kiszolgálók összes vagy néhány adatbázisát. A következő ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját mutatja be, amely több adatbázist és automatikus feladatátvételi csoportot használ.

![A diagram egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját mutatja be, amely több adatbázist és automatikus feladatátvételi csoportot használ.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Lásd: [SQL Database hozzáadása egy feladatátvételi csoporthoz](failover-group-add-single-database-tutorial.md) , amely részletesen ismerteti, hogyan adhat hozzá egy adatbázist a SQL Database egy feladatátvételi csoporthoz.

Az üzletmenet folytonosságát szem előtt tartva az alábbi általános irányelvek szerint járjon el:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Több adatbázis feladatátvételének kezelése egy vagy több feladatátvételi csoport használatával

A különböző régiókban (elsődleges és másodlagos kiszolgálókon) lévő két kiszolgáló között egy vagy több feladatátvételi csoport is létrehozható. Az egyes csoportok tartalmazhatnak egy vagy több olyan adatbázist, amelyeket egységként állítanak be abban az esetben, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban bekövetkező leállás miatt. A feladatátvételi csoport a földrajzilag másodlagos adatbázist hozza létre ugyanazzal a szolgáltatási céllal, mint az elsődleges. Ha egy meglévő geo-replikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a Geo-másodlagos kiszolgáló ugyanazzal a szolgáltatási réteggel és számítási mérettel van konfigurálva, mint az elsődleges.
  
> [!IMPORTANT]
> A különböző előfizetésekben található két kiszolgáló közötti feladatátvételi csoportok létrehozása jelenleg nem támogatott Azure SQL Database esetén. Ha a feladatátvételi csoport létrehozása után áthelyezi az elsődleges vagy a másodlagos kiszolgálót egy másik előfizetésre, a feladatátvételi kérelmek és egyéb műveletek meghibásodását okozhatják.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írási-olvasási figyelő használata a OLTP számítási feladatokhoz

OLTP műveletek végrehajtásakor használja `<fog-name>.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. Megjegyzés: a feladatátvétel magában foglalja a DNS-rekord frissítését, hogy az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor legyenek átirányítva az új elsődlegesre.

### <a name="using-read-only-listener-for-read-only-workload"></a>Írásvédett munkaterheléshez csak olvasási figyelő használata

Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Csak olvasási munkamenetek esetén használja `<fog-name>.secondary.database.windows.net` a kiszolgáló URL-címét, a kapcsolat pedig automatikusan a másodlagosra lesz irányítva. Azt is javasoljuk, hogy a használatával adja meg az olvasási szándékot a kapcsolatok karakterláncában `ApplicationIntent=ReadOnly` .

### <a name="preparing-for-performance-degradation"></a>A teljesítmény romlásának előkészítése

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele az Azure SQL-összetevők állapotán alapul. Előfordulhat, hogy a leállás nem érinti az elsődleges régió többi Azure-szolgáltatását, és ezek összetevői továbbra is elérhetők lesznek az adott régióban. Ha az elsődleges adatbázisok a DR régióra váltanak, a függő összetevők közötti késés megnövekszik. Ha el szeretné kerülni az alkalmazás teljesítményének nagyobb késleltetését, győződjön meg arról, hogy az alkalmazás összes összetevőjének redundancia van a DR régióban, és kövesse ezeket a [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Adatvesztés előkészítése

Ha a rendszer kimaradást észlel, az Azure a által megadott időszakra vár `GracePeriodWithDataLossHours` . Az alapértelmezett érték 1 óra. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a `GracePeriodWithDataLossHours` megfelelő számú, például 24 órás értéket állítsa be. A manuális csoport feladatátvételi szolgáltatásával visszatérhet a másodlagosról az elsődlegesre.

> [!IMPORTANT]
> A 800-es vagy kevesebb DTU és több mint 250 adatbázissal rendelkező, Geo-replikációt használó rugalmas készletek olyan problémákba ütközhet, mint a hosszabb tervezett feladatátvétel és a csökkentett teljesítmény.  Ezek a problémák nagyobb valószínűséggel fordulnak elő az írási igényű munkaterhelések esetében, ha a Geo-replikációs végpontok földrajzilag széles körben elkülönülnek, vagy ha az egyes adatbázisokhoz több másodlagos végpont is használatos.  Ezeknek a problémáknak a tünetei akkor jelennek meg, ha a földrajzi replikálási késés idővel növekszik.  Ezt a késést [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)használatával lehet figyelni.  Ha ezek a problémák lépnek fel, a mérséklések közé tartozik a készlet DTU számának növelése vagy a Geo-replikált adatbázisok számának csökkentése ugyanabban a készletben.

### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

A módosítási folyamat szemléltetése érdekében feltételezzük, hogy az a kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új másodlagos a harmadik régióban.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1. Hozzon létre további formátumú másodlagos zónák az A kiszolgálón lévő összes adatbázisról a C kiszolgálóra [aktív geo-replikáció](active-geo-replication-overview.md)használatával. Az A kiszolgáló minden adatbázisa két formátumú másodlagos zónák fog rendelkezni, egyet a B kiszolgálón, egyet pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védelme az áttérés során is megmaradjon.
2. Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
3. Hozza létre újra a feladatátvételi csoportot ugyanazzal a névvel az A és a C kiszolgáló között. Ezen a ponton a bejelentkezések sikertelenek lesznek.
4. Adja hozzá az A kiszolgálón lévő összes elsődleges adatbázist az új feladatátvételi csoporthoz.
5. A B kiszolgáló eldobása. A B kiszolgálón lévő összes adatbázist automatikusan törli a rendszer.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

A módosítási folyamat szemléltetése érdekében feltételezzük, hogy az a kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új elsődleges a harmadik régióban.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1. Végezzen el egy tervezett feladatátvételt az elsődleges kiszolgáló B kiszolgálóra való átváltásához. a kiszolgáló lesz az új másodlagos kiszolgáló. A feladatátvétel több perc állásidőt eredményezhet. A tényleges idő a feladatátvételi csoport méretétől függ.
2. Hozzon létre további formátumú másodlagos zónák az egyes adatbázisokról a B kiszolgálón a C kiszolgálóra az [aktív földrajzi replikálás](active-geo-replication-overview.md)használatával. A B kiszolgálón található minden adatbázis két formátumú másodlagos zónák rendelkezik, egyet az A kiszolgálón, egy pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védelme az áttérés során is megmaradjon.
3. Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
4. Hozza létre újra a feladatátvételi csoportot a B és a C kiszolgáló között azonos névvel. Ezen a ponton a bejelentkezések sikertelenek lesznek.
5. Vegye fel a B kiszolgálón található összes elsődleges adatbázist az új feladatátvételi csoportba.
6. Végezzen el egy tervezett feladatátvételt a feladatátvételi csoportban a B és C kapcsolóhoz. Most a C kiszolgáló lesz az elsődleges és a B-a másodlagos. Az A kiszolgálón lévő összes másodlagos adatbázis automatikusan a C-ben lévő elsődlegesekhez lesz társítva. Ahogy az 1. lépésben, a feladatátvétel több perc állásidőt eredményezhet.
7. Dobja el az A kiszolgálót. Az A-ben lévő összes adatbázist automatikusan törli a rendszer.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más feladatátvételi csoportot vagy kiszolgálói aliast hozzon létre ugyanazzal a névvel, ami megakadályozza, hogy újra használja. A kockázat minimalizálásához ne használja az általános feladatátvételi csoportok nevét.

## <a name="best-practices-for-sql-managed-instance"></a>Ajánlott eljárások az SQL felügyelt példányaihoz

Az automatikus feladatátvételi csoportot az elsődleges példányon kell konfigurálni, és egy másik Azure-régióban található másodlagos példányhoz kell csatlakoznia.  A példányban lévő valamennyi adatbázis a másodlagos példányra lesz replikálva.

A következő ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját szemlélteti, amely felügyelt példányt és automatikus feladatátvételi csoportot használ.

![automatikus feladatátvételi diagram](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Tekintse meg a [felügyelt példány hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md) című témakört, amely részletesen ismerteti az SQL felügyelt példányok feladatátvételi csoport használatára való hozzáadásának lépéseit.

Ha az alkalmazás az SQL felügyelt példányát használja adatcsomagként, kövesse az alábbi általános irányelveket az üzletmenet folytonosságának tervezésekor:

### <a name="creating-the-secondary-instance"></a>A másodlagos példány létrehozása

Annak biztosítása érdekében, hogy az elsődleges és a másodlagos példányok feladatátvétele ne legyen megszakítva az elsődleges SQL felügyelt példányhoz, mind ugyanabban a DNS-zónában kell lennie. A szolgáltatás garantálja, hogy ugyanaz a többtartományos (SAN) tanúsítvány használható az ügyfélkapcsolatok hitelesítésére a feladatátvételi csoport két példányának egyikén. Ha az alkalmazás készen áll az éles környezetben való üzembe helyezésre, hozzon létre egy másodlagos SQL felügyelt példányt egy másik régióban, és ellenőrizze, hogy a DNS-zónát megosztja-e az elsődleges SQL felügyelt példánnyal. Ezt a nem kötelező paraméter megadásával teheti meg a létrehozás során. Ha a PowerShellt vagy a REST API használja, akkor a választható paraméter neve, a `DNS Zone Partner` Azure Portal a megfelelő választható mező neve pedig elsődleges felügyelt példány.

> [!IMPORTANT]
> Az alhálózatban létrehozott első felügyelt példány határozza meg a DNS-zónát az azonos alhálózaton lévő összes további példányhoz. Ez azt jelenti, hogy az azonos alhálózatból származó két példány nem tartozhat különböző DNS-zónákhoz.

További információ a másodlagos SQL felügyelt példány létrehozásáról az elsődleges példánnyal azonos DNS-zónában: [másodlagos felügyelt példány létrehozása](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Földrajzilag párosított régiók használata

A felügyelt példányokat a [párosított régiók](../../best-practices-availability-paired-regions.md) számára is üzembe helyezheti teljesítménybeli okokból. A földrajzilag párosított régiókban található felügyelt példányok sokkal jobb teljesítményt biztosítanak a nem párosított régiókhoz képest. 

### <a name="enabling-replication-traffic-between-two-instances"></a>A replikálási forgalom engedélyezése két példány között

Mivel minden példány el van különítve a saját VNet, engedélyezni kell a két irányú adatforgalmat a virtuális hálózatok között. Lásd: [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Feladatátvételi csoport létrehozása a felügyelt példányok között eltérő előfizetésekben

Létrehozhat egy feladatátvételi csoportot két különböző előfizetésben lévő SQL felügyelt példányok között, feltéve, hogy az előfizetések ugyanahhoz a [Azure Active Directory bérlőhöz](../../active-directory/fundamentals/active-directory-whatis.md#terminology)vannak társítva. A PowerShell API használatakor ezt megteheti a `PartnerSubscriptionId` másodlagos SQL felügyelt példányának paraméterének megadásával. REST API használatakor a paraméterben szereplő minden példány-azonosító `properties.managedInstancePairs` rendelkezhet saját subscriptionID is.
  
> [!IMPORTANT]
> A Azure Portal nem támogatja a feladatátvételi csoportok létrehozását a különböző előfizetések között. Emellett a meglévő feladatátvételi csoportok esetében a különböző előfizetések és/vagy erőforráscsoportok esetében a feladatátvételt nem lehet manuálisan kezdeményezni a portálon keresztül az elsődleges SQL felügyelt példányon. Ehelyett azt a földrajzilag másodlagos példányból kell kezdeményeznie.

### <a name="managing-failover-to-secondary-instance"></a>Másodlagos példányra történő feladatátvétel kezelése

A feladatátvételi csoport a felügyelt SQL-példányban lévő összes adatbázis feladatátvételét kezelni fogja. Egy csoport létrehozásakor a rendszer automatikusan elvégzi a példány minden adatbázisának georeplikálását a másodlagos felügyelt SQL-példányra. A feladatátvételi csoportok nem használhatók az adatbázisok egy részhalmazának részleges feladatátvételéhez.

> [!IMPORTANT]
> Ha egy adatbázis el lett távolítva az elsődleges SQL felügyelt példányból, akkor azt a rendszer a Geo-másodlagos SQL felügyelt példányon is automatikusan elveti.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írási-olvasási figyelő használata a OLTP számítási feladatokhoz

OLTP műveletek végrehajtásakor használja `<fog-name>.zone_id.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. A feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor lesznek átirányítva az új elsődlegesre. Mivel a másodlagos példány megosztja a DNS-zónát az elsődleges értékkel, az ügyfélalkalmazás újra csatlakozhat ugyanahhoz a SAN-tanúsítványhoz.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>A csak olvasási figyelő használata a másodlagos példányhoz való kapcsolódáshoz

Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Ha közvetlenül a földrajzilag replikált másodlagoshoz szeretne csatlakozni, használja `<fog-name>.secondary.<zone_id>.database.windows.net` a kiszolgáló URL-címét, és a kapcsolat közvetlenül a földrajzilag replikált másodlagosra történik.

> [!NOTE]
> A prémium, üzletileg kritikus és nagy kapacitású szolgáltatási rétegekben a SQL Database támogatja a [csak olvasható replikák](read-scale-out.md) használatát a csak olvasási jogosultsággal rendelkező lekérdezési feladatok futtatásához egy vagy több írásvédett replika kapacitásával, a `ApplicationIntent=ReadOnly` kapcsolódási karakterlánc paraméterének használatával. Ha georeplikált másodlagos példányt konfigurált, ezzel a képességgel csatlakozhat egy írásvédett replikához az elsődleges helyen vagy a georeplikált helyen is.
>
> - Az elsődleges helyen található írásvédett replikához való kapcsolódáshoz használja a és a `ApplicationIntent=ReadOnly` lehetőséget `<fog-name>.<zone_id>.database.windows.net` .
> - Ha a másodlagos helyen lévő írásvédett replikához szeretne csatlakozni, használja a és a következőt: `ApplicationIntent=ReadOnly` `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>A teljesítmény romlásának előkészítése

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele az Azure SQL-összetevők állapotán alapul. Előfordulhat, hogy a leállás nem érinti az elsődleges régió többi Azure-szolgáltatását, és ezek összetevői továbbra is elérhetők lesznek az adott régióban. Ha az elsődleges adatbázisok a másodlagos régióra váltanak, a függő összetevők közötti késés megnövekszik. Ha el szeretné kerülni az alkalmazás teljesítményének nagyobb késleltetését, győződjön meg arról, hogy az alkalmazás összes összetevőjének redundancia a másodlagos régióban, és az alkalmazás-összetevők feladatátvétele az adatbázissal együtt történik. A konfigurációs időpontban kövesse a [hálózati biztonsági irányelveket](#failover-groups-and-network-security) a másodlagos régióban található adatbázishoz való kapcsolódás biztosításához.

### <a name="preparing-for-data-loss"></a>Adatvesztés előkészítése

Ha a rendszer áramszünetet észlel, akkor a rendszer egy írható és olvasható feladatátvételt indít el, ha nulla adatvesztés áll fenn a legjobb tudomásuk szerint. Ellenkező esetben a feladatátvétel a megadott időszakra van késleltetve `GracePeriodWithDataLossHours` . Ha meg van adva `GracePeriodWithDataLossHours` , fel kell készülnie az adatvesztésre. Általánosságban elmondható, hogy az Azure továbbra is rendelkezésre áll. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a GracePeriodWithDataLossHours megfelelően nagy számra, például 24 órára állítsa be, vagy tiltsa le az automatikus feladatátvételt.

Az írható-olvasható figyelő DNS-frissítése a feladatátvétel megkezdése után azonnal megtörténik. Ez a művelet nem eredményez adatvesztést. Az adatbázis-szerepkörök váltásának folyamata azonban normál körülmények között akár 5 percet is igénybe vehet. Amíg a művelet be nem fejeződik, az új elsődleges példány egyes adatbázisai továbbra is írásvédettek maradnak. Ha a feladatátvételt a PowerShell használatával kezdeményezik, az elsődleges replika szerepkör átváltására szolgáló művelet szinkron jellegű. Ha a Azure Portal használatával kezdeményezik, akkor a felhasználói felület a befejezési állapotot jelzi. Ha a REST API használatával kezdeményezik, a végrehajtás figyeléséhez használja a standard Azure Resource Manager lekérdezési mechanizmusát.

> [!IMPORTANT]
> A manuális csoport feladatátvételével visszahelyezheti az elsődleges helyre az elsődleges helyet. Ha a feladatátvételt okozó leállás le lett csökkentve, az elsődleges adatbázisokat az eredeti helyre helyezheti át. Ehhez el kell indítania a csoport manuális feladatátvételét.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

Tegyük fel, hogy az a példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig a harmadik régió új másodlagos példánya.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1. Hozzon létre a C példányt ugyanazzal a mérettel, mint a és ugyanabban a DNS-zónában.
2. Törölje a feladatátvételi csoportot az A és A B példány között. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az elsődlegesekről, és írási-olvasási adatbázisok lesznek.
3. Hozzon létre egy azonos nevű feladatátvételi csoportot az A és A C példány között. kövesse a [feladatátvételi csoport utasításait az SQL felügyelt példányának oktatóanyagával](../managed-instance/failover-group-add-instance-tutorial.md). Ez egy adatméreti művelet, amely akkor fejeződik be, amikor az A példány összes adatbázisát kivezetik és szinkronizálják.
4. Törölje a B példányt, ha nem szükséges a szükségtelen díjak elkerüléséhez.

> [!NOTE]
> A 2. lépés és a 3. lépés elvégzése után az "a" példányban lévő adatbázisok nem védettek maradnak az A példány katasztrofális meghibásodása miatt.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

Tegyük fel, hogy az a példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig a harmadik régió új elsődleges példánya.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy C példányt ugyanazzal a mérettel, mint B és ugyanabban a DNS-zónában.
2. Kapcsolódjon a B példányhoz, és manuálisan végezze el a feladatátvételt az elsődleges példány B-re való átváltásához. az A példány automatikusan az új másodlagos példány lesz.
3. Törölje a feladatátvételi csoportot az A és A B példány között. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az elsődlegesekről, és írási-olvasási adatbázisok lesznek.
4. Hozzon létre egy azonos nevű feladatátvételi csoportot az A és A C példány között. kövesse a [feladatátvételi csoport utasításait a felügyelt példányok oktatóanyagával](../managed-instance/failover-group-add-instance-tutorial.md). Ez egy adatméreti művelet, amely akkor fejeződik be, amikor az A példány összes adatbázisát kivezetik és szinkronizálják.
5. Ha nem szeretné elkerülni A szükségtelen költségeket, törölje az A példányt.

> [!CAUTION]
> A 3. lépés és a 4. lépés befejezése után az A példányban lévő adatbázisok nem védettek maradnak az A példány katasztrofális meghibásodása miatt.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más feladatátvételi csoportot vagy kiszolgálói aliast hozzon létre ugyanazzal a névvel, ami megakadályozza, hogy újra használja. A kockázat minimalizálásához ne használja az általános feladatátvételi csoportok nevét.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>A rendszeradatbázisok objektumaitól függő forgatókönyvek engedélyezése
A rendszeradatbázisokat a rendszer nem replikálja a feladatátvételi csoport másodlagos példányára. Ha olyan forgatókönyveket szeretne engedélyezni, amelyek a rendszeradatbázisokból származó objektumtól függenek, a másodlagos példányon győződjön meg arról, hogy ugyanazokat az objektumokat hozza létre a másodlagos kiszolgálón. Ha például ugyanazokat a bejelentkezéseket kívánja használni a másodlagos példányon, ügyeljen arra, hogy az azonos biztonsági azonosítóval hozza létre őket. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazásokhoz a biztonsági szabályok megkövetelik, hogy az adatcsomaghoz való hálózati hozzáférés egy adott összetevőhöz vagy összetevőkhöz, például egy virtuális géphez, egy webszolgáltatáshoz, stb. van korlátozva. Ez a követelmény az üzletmenet folytonosságának kialakítására és a feladatátvételi csoportok használatára vonatkozó kihívásokat mutatja be. Az ilyen korlátozott hozzáférés megvalósításakor vegye figyelembe az alábbi beállításokat.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és virtuális hálózati szabályok használata

Ha [Virtual Network szolgáltatási végpontokat és szabályokat](vnet-service-endpoint-rule-overview.md) használ az adatbázishoz való hozzáférés korlátozására SQL Database vagy SQL felügyelt példányon, vegye figyelembe, hogy minden egyes virtuális hálózati szolgáltatás végpontja csak egy Azure-régióra vonatkozik. A végpont nem teszi lehetővé más régiók számára, hogy fogadják a kommunikációt az alhálózatból. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások csatlakozhatnak az elsődleges adatbázishoz. Mivel a feladatátvétel a SQL Database-ügyfél munkameneteit egy másik (másodlagos) régióban lévő kiszolgálóra irányítja át, ezek a munkamenetek sikertelenek lesznek, ha az adott régión kívüli ügyféltől származik. Emiatt az automatikus feladatátvételi házirend nem engedélyezhető, ha a résztvevő kiszolgálók vagy példányok szerepelnek a Virtual Network szabályokban. A manuális feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. Az alkalmazás előtér-összetevőinek (webszolgáltatás, virtuális gépek stb.) redundáns másolatainak kiépítése a másodlagos régióban
2. A [virtuális hálózati szabályok](vnet-service-endpoint-rule-overview.md) konfigurálása egyenként az elsődleges és a másodlagos kiszolgáló számára
3. Az [előtér-feladatátvétel engedélyezése Traffic Manager-konfiguráció használatával](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. A manuális feladatátvétel elindítása a leállás észlelésekor. Ez a beállítás olyan alkalmazásokra van optimalizálva, amelyek konzisztens késést igényelnek az előtér és az adatréteg között, és támogatja a helyreállítást, ha az előtér, az adatréteg vagy mindkettő hatással van a kimaradásra.

> [!NOTE]
> Ha az írásvédett **figyelőt** a csak olvasási feladatok terheléselosztásához használja, akkor győződjön meg arról, hogy ez a számítási feladat a másodlagos régióban lévő virtuális gépen vagy más erőforráson fut, így a másodlagos adatbázishoz csatlakozhat.

### <a name="use-failover-groups-and-firewall-rules"></a>Feladatátvevő csoportok és tűzfalszabályok használata

Ha az üzletmenet-folytonossági terv automatikus feladatátvételi csoportokkal való feladatátvételt igényel, akkor a hagyományos tűzfalszabályok használatával korlátozhatja a hozzáférést az adatbázishoz SQL Database. Az automatikus feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. [Nyilvános IP-cím létrehozása](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Hozzon létre egy nyilvános Load balancert](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , és rendelje hozzá a nyilvános IP-címet.
3. [Hozzon létre egy virtuális hálózatot és a virtuális gépeket](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) az előtér-összetevőkhöz
4. [Hozzon létre egy hálózati biztonsági csoportot](../../virtual-network/network-security-groups-overview.md) , és konfigurálja a bejövő kapcsolatokat.
5. Győződjön meg arról, hogy a kimenő kapcsolatok Azure SQL Database az "SQL" [szolgáltatás címkével](../../virtual-network/network-security-groups-overview.md#service-tags).
6. Hozzon létre egy [SQL Database tűzfalszabály](firewall-configure.md) , amely engedélyezi az 1. lépésben létrehozott nyilvános IP-címről érkező bejövő forgalmat.

A kimenő hozzáférés konfigurálásával és a tűzfalszabályok által használt IP-címekkel kapcsolatos további információkért lásd: terheléselosztó [kimenő kapcsolatai](../../load-balancer/load-balancer-outbound-connections.md).

A fenti konfiguráció biztosítja, hogy az automatikus feladatátvétel ne blokkolja az előtér-összetevők kapcsolatait, és azt feltételezi, hogy az alkalmazás képes tolerálni az előtér és az adatréteg közötti nagyobb késést.

> [!IMPORTANT]
> A regionális kimaradások üzletmenet-folytonosságának garantálása érdekében biztosítania kell a földrajzi redundanciát mind az előtér-összetevők, mind az adatbázisok számára.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>A georeplikáció engedélyezése felügyelt példányok és virtuális hálózataik között

Ha egy feladatátvételi csoportot állít be az elsődleges és a másodlagos SQL felügyelt példányok között két különböző régióban, az egyes példányok különálló virtuális hálózattal vannak elkülönítve. A virtuális hálózatok közötti replikációs forgalom engedélyezéséhez ellenőrizze, hogy teljesülnek-e az előfeltételek:

- Az SQL által felügyelt példány két példányának különböző Azure-régiókban kell lennie.
- A felügyelt SQL-példányok két példányának azonos szolgáltatási szintnek kell lennie, és ugyanazzal a tárolási mérettel kell rendelkeznie.
- A felügyelt SQL-példány másodlagos példányának üresnek kell lennie (nincs felhasználói adatbázis).
- Az SQL felügyelt példány példányai által használt virtuális hálózatokat [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [expressz útvonalon](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)keresztül kell csatlakoztatni. Ha két virtuális hálózat egy helyszíni hálózaton keresztül csatlakozik, győződjön meg arról, hogy nem blokkolja tűzfalszabály az 5022-es és a 11000–11999-es portokat. A globális VNet-társítást az alábbi megjegyzésben ismertetett korlátozás támogatja.

   > [!IMPORTANT]
   > [9/22/2020-ben bejelentettük a globális virtuális hálózati társítást az újonnan létrehozott virtuális fürtökhöz](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ez azt jelenti, hogy a globális virtuális hálózati társítás támogatott a bejelentési dátum után üres alhálózatokban létrehozott SQL felügyelt példányok esetében, valamint az ezen alhálózatokban létrehozott összes további felügyelt példány esetében is. A többi SQL felügyelt példányok társításának támogatása az azonos régióban található hálózatokra korlátozódik a [globális virtuális hálózati társítás korlátai](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)miatt. További részletekért tekintse meg az [Azure Virtual Networks – gyakori kérdések](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) című cikket is. 

- A két felügyelt SQL-példány virtuális hálózatok nem rendelkezhet átfedésben lévő IP-címekkel.
- A hálózati biztonsági csoportokat (NSG) úgy kell beállítania, hogy az 5022-es portnak és a 11000–12000 közötti tartományba eső portoknak engedélyezni kell a másik felügyelt példány alhálózatával folytatott bejövő és kimenő forgalmat. Ezzel lehetővé teszi a virtuális hálózatok közötti replikációs forgalmat.

   > [!IMPORTANT]
   > Helytelenül konfigurált NSG biztonsági szabályok vezetnek az adatbázis-másolási műveletek elakadása érdekében.

- A másodlagos SQL felügyelt példánya a helyes DNS-zóna azonosítójával van konfigurálva. A DNS-zóna a felügyelt SQL-példányok és a mögöttes virtuális fürtök egyik tulajdonsága, az azonosító pedig az állomásnév címe. A zóna AZONOSÍTÓját véletlenszerű karakterláncként hozza létre a rendszer, ha az első SQL felügyelt példány az egyes VNet jön létre, és ugyanaz az azonosító az ugyanabban az alhálózatban lévő összes többi példányhoz van rendelve. Hozzárendelés után a DNS-zóna nem módosítható. Az ugyanabban a feladatátvételi csoportban található SQL felügyelt példányoknak meg kell osztaniuk a DNS-zónát. Ezt úgy érheti el, ha a másodlagos példány létrehozásakor a DnsZonePartner paraméter értékeként átadja az elsődleges példány zónájának AZONOSÍTÓját.

   > [!NOTE]
   > A feladatátvételi csoportok SQL felügyelt példánnyal való konfigurálásával kapcsolatos részletes oktatóanyagért lásd: [SQL felügyelt példány hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

A másodlagos adatbázisok leválasztása nélkül frissítheti vagy visszaminősítheti az elsődleges adatbázist más számítási méretre (ugyanazon a szolgáltatási szinten belül, általános célú és üzletileg kritikus között). A frissítéskor javasoljuk, hogy először frissítse az összes másodlagos adatbázist, majd frissítse az elsődlegest. A visszalépést követően a sorrend megfordításakor a rendszer visszaminősíti az elsődlegest, majd lecseréli az összes másodlagos adatbázist. Ha egy alacsonyabb vagy magasabb szolgáltatási szintre módosítja az adatbázist, a rendszer kikényszeríti ezt az ajánlást.

Ezt az ütemezést kifejezetten azért javasoljuk, hogy az alacsonyabb szintű termékváltozatban lévő másodlagos példány ne legyen túlterhelve, és a frissítés vagy lefokozási folyamat során ne kelljen új magot kapnia. A problémát úgy is elkerülheti, ha az elsődleges példányt írásvédetté teszi, de ez hatással lesz az összes írási és olvasási számítási feladatra az elsődleges példányon.

> [!NOTE]
> Ha a másodlagos adatbázist a feladatátvételi csoport konfigurációjának részeként hozta létre, a másodlagos adatbázis visszalépéséhez nem ajánlott. Ezzel biztosíthatja, hogy az adatmennyiség elegendő kapacitással legyen feldolgozva a rendszeres számítási feladatok elvégzése után a feladatátvétel aktiválása után.

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus fontosságú adatmennyiség elvesztésének megakadályozása

A nagyméretű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció során az adatvesztés elkerülhető, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések elleni védelem érdekében az alkalmazás fejlesztője a tranzakció véglegesítése után azonnal meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárást. A hívás `sp_wait_for_database_copy_sync` a hívó szálat blokkolja, amíg az utolsó véglegesített tranzakció át nem lett továbbítva a másodlagos adatbázisba. Azonban nem várja meg, amíg a továbbított tranzakciók újra le lesznek játszva és véglegesítve lettek a másodlagoson. `sp_wait_for_database_copy_sync` hatóköre egy adott folytonos másolási hivatkozásra vonatkozik. Minden olyan felhasználó, aki az elsődleges adatbázishoz kapcsolódási jogokkal rendelkezik, meghívhatja ezt az eljárást.

> [!NOTE]
> `sp_wait_for_database_copy_sync` megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja a teljes szinkronizálást az olvasási hozzáféréshez. Az `sp_wait_for_database_copy_sync` eljárási hívás által okozott késleltetés jelentős lehet, és a tranzakciós napló méretétől függ a hívás időpontjában.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz történő visszaállítás

További információ a feladatátvételi csoportokkal való időpontra történő visszaállításról: [időponthoz való helyreállítás (PITR)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>A feladatátvételi csoportok korlátai

Vegye figyelembe a következő korlátozásokat:

- Nem hozhatók létre feladatátvételi csoportok két kiszolgáló vagy példány között ugyanabban az Azure-régióban.
- A feladatátvételi csoportokat nem lehet átnevezni. Törölnie kell a csoportot, majd újra létre kell hoznia egy másik névvel.
- A feladatátvételi csoportban lévő példányok nem támogatják az adatbázis átnevezését. Az adatbázisok átnevezéséhez átmenetileg törölnie kell a feladatátvételi csoportot.
- A rendszeradatbázisokat a rendszer nem replikálja a feladatátvételi csoport másodlagos példányára. Ezért előfordulhat, hogy a rendszeradatbázisok objektumaitól függő forgatókönyvek a másodlagos példányon nem lesznek lehetségesek, kivéve, ha az objektumokat manuálisan hozták létre a másodlagos kiszolgálón.

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok programozott kezelése

Ahogy azt korábban említettük, az automatikus feladatátvételi csoportok és az aktív geo-replikáció programozott módon is felügyelhető Azure PowerShell és a REST API használatával. A következő táblázatok ismertetik az elérhető parancsok készletét. Az aktív geo-replikálás Azure Resource Manager API-kat tartalmaz a felügyelethez, beleértve a [Azure SQL Database REST API](/rest/api/sql/) és [Azure PowerShell parancsmagokat](/powershell/azure/). Ezek az API-k az erőforráscsoportok használatát igénylik, és támogatják az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC). A hozzáférési szerepkörök megvalósításával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>SQL Database feladatátvétel kezelése

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Parancsmag | Leírás |
| --- | --- |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Feladatátvételi csoport konfigurációjának beolvasása |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Feladatátvételi csoport konfigurációjának módosítása |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos kiszolgálóra |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Egy vagy több adatbázis beadása egy feladatátvételi csoportba|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Parancs | Leírás |
| --- | --- |
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [az SQL feladatátvétel-csoport törlése](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [az SQL feladatátvétel-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Feladatátvételi csoport konfigurációjának beolvasása |
| [az SQL feladatátvétel-csoport frissítése](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Módosítja a feladatátvételi csoport konfigurációját, és/vagy egy vagy több adatbázist vesz fel egy feladatátvételi csoportba.|
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos kiszolgálóra |

# <a name="rest-api"></a>[REST API](#tab/rest-api)

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](/rest/api/sql/failovergroups/createorupdate) | Feladatátvételi csoport létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](/rest/api/sql/failovergroups/delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](/rest/api/sql/failovergroups/failover) | A teljes adatszinkronizálással elindítja az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra irányuló feladatátvételt.|
| [Adatvesztés kényszerített feladatátvételének engedélyezése](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra történő feladatátvételt az adatok szinkronizálása nélkül indítja el. A művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beolvasása](/rest/api/sql/failovergroups/get) | Lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listázása kiszolgáló szerint](/rest/api/sql/failovergroups/listbyserver) | A kiszolgálón található feladatátvételi csoportok felsorolása. |
| [Feladatátvételi csoport frissítése](/rest/api/sql/failovergroups/update) | A feladatátvételi csoport konfigurációjának frissítése. |

---

### <a name="manage-sql-managed-instance-failover"></a>Felügyelt SQL-példány feladatátvételének kezelése


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Parancsmag | Leírás |
| --- | --- |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos példányokon is.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport konfigurációjának módosítása|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport konfigurációjának beolvasása|
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport feladatátvételét indítja el a másodlagos példányra|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Feladatátvételi csoport eltávolítása|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Parancs | Leírás |
| --- | --- |
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [az SQL feladatátvétel-csoport törlése](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [az SQL feladatátvétel-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Feladatátvételi csoport konfigurációjának beolvasása |
| [az SQL feladatátvétel-csoport frissítése](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Módosítja a feladatátvételi csoport konfigurációját, és/vagy egy vagy több adatbázist vesz fel egy feladatátvételi csoportba.|
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos kiszolgálóra |

# <a name="rest-api"></a>[REST API](#tab/rest-api)

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](/rest/api/sql/instancefailovergroups/createorupdate) | Feladatátvételi csoport konfigurációjának létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](/rest/api/sql/instancefailovergroups/delete) | Feladatátvételi csoport eltávolítása a példányból |
| [Feladatátvétel (tervezett)](/rest/api/sql/instancefailovergroups/failover) | A teljes adatszinkronizálással elindítja az aktuális elsődleges példány feladatátvételét erre a példányra. |
| [Adatvesztés kényszerített feladatátvételének engedélyezése](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Az aktuális elsődleges példányról a másodlagos példányra történő feladatátvételt az adatok szinkronizálása nélkül indítja el. A művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beolvasása](/rest/api/sql/instancefailovergroups/get) | lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listázása hely szerint](/rest/api/sql/instancefailovergroups/listbylocation) | Egy helyen lévő feladatátvételi csoportok felsorolása. |

---

## <a name="next-steps"></a>Következő lépések

- Részletes oktatóanyagok:
  - [SQL Database hozzáadása feladatátvételi csoporthoz](failover-group-add-single-database-tutorial.md)
  - [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](failover-group-add-elastic-pool-tutorial.md)
  - [SQL felügyelt példány hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md)
- A minta parancsfájlokat lásd:
  - [A PowerShell használata az aktív geo-replikáció konfigurálásához Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [A PowerShell használatával konfigurálhatja az aktív földrajzi replikálást egy készletezett adatbázishoz Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Azure SQL Database hozzáadása feladatátvételi csoporthoz a PowerShell használatával](scripts/add-database-to-failover-group-powershell.md)
- Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Az automatikus biztonsági mentések Azure SQL Databaseáról a [SQL Database automatizált biztonsági mentések](automated-backups-overview.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](recovery-using-backups.md)című témakört.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeinek megismeréséhez tekintse meg a SQL Database biztonság a vész- [helyreállítás után](active-geo-replication-security-configure.md)című témakört.
