---
title: Ajánlott eljárások az Azure SQL Data Synchez
description: Ismerje meg az Azure-SQL-adatszinkronizálás konfigurálásával és futtatásával kapcsolatos ajánlott eljárásokat.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ee15bfaa1d69e2e5047e7d24986f8e4e7d5b8b31
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180241"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Ajánlott eljárások az Azure SQL Data Synchez 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk az Azure SQL-adatszinkronizálás ajánlott eljárásait ismerteti.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-data-sync-data-sql-server-sql-database.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg **nem** támogatja az Azure SQL felügyelt példányát.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Biztonság és megbízhatóság

### <a name="client-agent"></a>Ügyfél ügynöke

-   Telepítse az ügynököt a hálózati szolgáltatás elérésével rendelkező, legkevésbé privilegizált felhasználói fiók használatával.  
-   Telepítse az ügyféloldali ügynököt olyan számítógépre, amely nem a SQL Server számítógép.  
-   Ne regisztráljon egy helyszíni adatbázist egynél több ügynökkel.    
    -   Ezt akkor is el kell kerülni, ha különböző táblákat szinkronizál a különböző szinkronizálási csoportokhoz.  
    -   Egy helyszíni adatbázis több ügyfél-ügynökkel való regisztrálása kihívást jelent, amikor törli az egyik szinkronizálási csoportot.

### <a name="database-accounts-with-least-required-privileges"></a>A minimálisan szükséges jogosultságokkal rendelkező adatbázis-fiókok

-   **Szinkronizálás beállításakor**. Tábla létrehozása/módosítása; Adatbázis módosítása; Eljárás létrehozása; Séma kiválasztása/módosítása; Hozzon létre User-Defined típust.

-   **Folyamatos szinkronizáláshoz**. A szinkronizáláshoz kiválasztott táblák kijelölése/beillesztése/frissítése/törlése, valamint a szinkronizálási metaadatok és nyomon követési táblázatok; Végrehajtási engedély a szolgáltatás által létrehozott tárolt eljárásokhoz; Végrehajtási engedély a felhasználó által definiált táblák típusaihoz.

-   **A** megszüntetéshez. A szinkronizáláshoz a táblázatok részét változtatja; Válassza ki/törölje a szinkronizálási metaadatokat tartalmazó táblákat; A szinkronizálás nyomon követésére szolgáló táblák, tárolt eljárások és felhasználó által definiált típusok vezérlése.

Azure SQL Database csak a hitelesítő adatok egyetlen készletét támogatja. A feladatok ezen a korlátozáson belüli végrehajtásához vegye figyelembe a következő lehetőségeket:

-   Módosítsa a különböző fázisokhoz tartozó hitelesítő adatokat (például *credentials1* a telepítéshez és a *credentials2* a folyamatban van).  
-   Módosítsa a hitelesítő adatok engedélyét (azaz módosítsa az engedélyt a szinkronizálás beállítása után).

### <a name="auditing"></a>Naplózás

Javasoljuk, hogy engedélyezze a naplózást a szinkronizálási csoportok adatbázisainak szintjén. 

## <a name="setup"></a>Telepítés

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Az adatbázissal kapcsolatos megfontolások és megkötések

#### <a name="database-size"></a>Adatbázisméret

Amikor új adatbázist hoz létre, állítsa be a maximális méretet úgy, hogy az mindig nagyobb legyen, mint a telepített adatbázis. Ha nem állítja be a maximális méretet az üzembe helyezett adatbázisnál nagyobb értékre, a szinkronizálás sikertelen lesz. Bár a SQL-adatszinkronizálás nem biztosít automatikus növekedést, a parancs futtatásával `ALTER DATABASE` növelheti az adatbázis méretét a létrehozása után. Győződjön meg arról, hogy az adatbázis mérete korlátok között marad.

> [!IMPORTANT]
> A SQL-adatszinkronizálás további metaadatokat tárol az egyes adatbázisokkal. Győződjön meg arról, hogy a szükséges lemezterület kiszámításához a metaadatokat kell figyelembe veszi. A hozzáadott terhelés mennyisége a táblák szélessége (például a keskeny táblázatok nagyobb terhelést igényelnek) és a forgalom mennyiségével kapcsolatos.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> A táblázat szempontjai és megkötései

#### <a name="selecting-tables"></a>Táblák kiválasztása

A szinkronizálási csoportban lévő adatbázisban lévő összes táblát nem kell tartalmaznia. A szinkronizálási csoportba belefoglalt táblák a hatékonyságot és a költségeket érintik. A táblákat és az azoktól függő táblákat csak akkor kell szinkronizálni a szinkronizálási csoportban, ha az üzleti igényekhez szükség van.

#### <a name="primary-keys"></a>Elsődleges kulcsok

A szinkronizálási csoportban lévő összes táblának rendelkeznie kell egy elsődleges kulccsal. SQL-adatszinkronizálás nem tud szinkronizálni egy olyan táblát, amely nem rendelkezik elsődleges kulccsal.

A SQL-adatszinkronizálás éles környezetben való használata előtt tesztelje a kezdeti és folyamatos szinkronizálási teljesítményt.

#### <a name="empty-tables-provide-the-best-performance"></a>Az üres táblázatok biztosítják a legjobb teljesítményt

Az üres táblák a legjobb teljesítményt nyújtják az inicializálási időpontnál. Ha a céltábla üres, az adatszinkronizálás a tömeges Beszúrás használatával tölti be az adatmennyiséget. Ellenkező esetben az adatszinkronizálás az ütközések ellenőrzéséhez sorok közötti összehasonlítást és beszúrást végez. Ha azonban a teljesítmény nem jelent problémát, beállíthatja az olyan táblák közötti szinkronizálást, amelyek már tartalmaznak adattartalmat.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Cél-adatbázisok kiépítés

A SQL-adatszinkronizálás alapszintű adatbázis-létesítést biztosít.

Ez a szakasz a kiépítés korlátozásait ismerteti SQL-adatszinkronizálásban.

#### <a name="autoprovisioning-limitations"></a>Az kiépítés korlátai

A SQL-adatszinkronizálás az alábbi korlátozásokkal rendelkezik az kiépítés során:

-   Csak a cél táblában létrehozott oszlopokat válassza ki. A szinkronizálási csoport részét nem képező oszlopok nincsenek kiépítve a céltábla tábláiban.
-   Az indexek csak a kijelölt oszlopokhoz jönnek létre. Ha a forrástábla indexe olyan oszlopokkal rendelkezik, amelyek nem részei a szinkronizálási csoportnak, akkor ezek az indexek nincsenek kiépítve a céltábla tábláiban.  
-   Az XML típusú oszlopok indexei nincsenek kiépítve.  
-   Az ellenőrzési korlátozások nincsenek kiépítve.  
-   A forrástábla meglévő eseményindítói nincsenek kiépítve.  
-   A céladatbázis nem hoz létre nézeteket és tárolt eljárásokat.
-   A frissítés KASZKÁDOLT és a TÖRLÉSi műveletek nem hozhatók létre a külső kulcsokra vonatkozó megkötések esetében a célhely tábláiban.
-   Ha olyan decimális vagy numerikus oszlopokkal rendelkezik, amelyek pontossága nagyobb, mint 28, akkor a szinkronizálás során előfordulhat, hogy SQL-adatszinkronizálás a konverzió túlcsordulása problémát tapasztal. Javasoljuk, hogy a decimális vagy numerikus oszlopok pontosságát 28 vagy kevesebb értékre korlátozza.

#### <a name="recommendations"></a>Javaslatok

-   Kizárólag a szolgáltatás kipróbálásakor használja a SQL-adatszinkronizálás-kiépítési képességet.  
-   Éles környezetben kiépítheti az adatbázis-sémát.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Hol található a hub-adatbázis

#### <a name="enterprise-to-cloud-scenario"></a>Vállalat – felhő forgatókönyv

A késés csökkentése érdekében a hub-adatbázist közel kell tartani a szinkronizálási csoport adatbázis-forgalmának legnagyobb koncentrációjára.

#### <a name="cloud-to-cloud-scenario"></a>Felhőből felhőbe irányuló forgatókönyv

-   Ha a szinkronizálási csoportban lévő összes adatbázis egy adatközpontban található, akkor a hub-nak ugyanabban az adatközpontban kell lennie. Ez a konfiguráció csökkenti a késést és az adatközpontok közötti adatátvitel költségeit.
-   Ha a szinkronizálási csoportban lévő adatbázisok több adatközpontban találhatóak, akkor a hub-nek ugyanabban az adatközpontban kell lennie, mint az adatbázisok és az adatbázis-forgalom többségének.

#### <a name="mixed-scenarios"></a>Vegyes forgatókönyvek

Alkalmazza az előző irányelveket az összetett szinkronizálási csoport konfigurációkra, például a nagyvállalati Felhőbeli és a felhőből a felhőbe irányuló forgatókönyvek együttes használatára.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> A lassú és költséges kezdeti szinkronizálás elkerülése

Ebben a szakaszban egy szinkronizálási csoport kezdeti szinkronizálását tárgyaljuk. Ebből a témakörből megtudhatja, hogyan akadályozhatja meg, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, és költségesebb legyen a szükségesnél.

#### <a name="how-initial-sync-works"></a>A kezdeti szinkronizálás működése

Egy szinkronizálási csoport létrehozásakor csak egy adatbázisban kell kezdenie az adatgyűjtést. Ha több adatbázisban is vannak adatforrások, SQL-adatszinkronizálás az egyes sorokat a feloldani kívánt ütközésnek megfelelően kezeli. Ez az ütközési megoldás azt eredményezi, hogy a kezdeti szinkronizálás lassan meghalad. Ha több adatbázisban is vannak adatai, az adatbázis méretétől függően a kezdeti szinkronizálás több nap és néhány hónap között is eltarthat.

Ha az adatbázisok különböző adatközpontokban találhatók, minden sornak a különböző adatközpontok között kell utaznia. Ez növeli a kezdeti szinkronizálás költségeit.

#### <a name="recommendation"></a>Ajánlás

Ha lehetséges, először csak az egyik szinkronizálási csoport adatbázisaiban található adatmennyiséget kell megkezdenie.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> A szinkronizálási hurkok elkerülésének tervezése

Szinkronizálási hurok akkor következik be, amikor körkörös hivatkozások vannak egy szinkronizálási csoporton belül. Ebben az esetben az egyik adatbázis minden változása végtelen és körkörösen replikálódik a szinkronizálási csoport adatbázisain keresztül.   

Ügyeljen arra, hogy elkerülje a szinkronizálási hurkokat, mert ezek a teljesítmény romlását okozzák, és jelentős mértékben növelhetik a költségeket.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Nem propagált módosítások

#### <a name="reasons-that-changes-fail-to-propagate"></a>A változások sikertelen propagálásának okai

Előfordulhat, hogy a módosítások a következő okok egyike miatt sikertelenek lesznek:

-   Séma/adattípus inkompatibilitása.
-   Null érték beszúrása nem null értékű oszlopokban.
-   A külső kulcsokra vonatkozó megkötések megsértése.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Mi történik, ha a módosításokat nem sikerül propagálni?

-   A szinkronizálási csoport azt mutatja, hogy **Figyelmeztetési** állapotban van.
-   A részleteket a portál felhasználói felületének naplófájljaiban találja.
-   Ha a probléma 45 napig nem oldható fel, az adatbázis elavult lesz.

> [!NOTE]
> Ezek a változások soha nem szaporodnak. Ebben az esetben a helyreállítás egyetlen módja a szinkronizálási csoport újbóli létrehozása.

#### <a name="recommendation"></a>Ajánlás

A szinkronizálási csoport és az adatbázis állapotának rendszeres figyelése a Portálon és a log felületen keresztül.


## <a name="maintenance"></a>Karbantartás

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Elavult adatbázisok és szinkronizálási csoportok elkerülése

A szinkronizálási csoportban lévő szinkronizálási csoport vagy adatbázis elavult lehet. Ha a szinkronizálási csoport állapota elavult **, a** működése leáll. Ha **az adatbázis állapota elavult, az** adatok elveszhetnek. Érdemes elkerülni ezt a forgatókönyvet ahelyett, hogy helyre kellene állítani.

#### <a name="avoid-out-of-date-databases"></a>Elavult adatbázisok elkerülése

Az adatbázisok állapota **elavult** , ha 45 vagy több napig offline állapotban van. Az adatbázis elavult állapotának elkerülése érdekében győződjön meg arról, hogy az adatbázisok egyike sem érhető **el** legalább 45 napig.

#### <a name="avoid-out-of-date-sync-groups"></a>Elavult szinkronizálási csoportok elkerülése

A szinkronizálási csoport **állapota elavult, ha a** szinkronizálási csoport bármelyik változása nem propagálja a szinkronizálási csoport többi részét 45 napig vagy többet. A szinkronizálási csoport **elavult** állapotának elkerüléséhez rendszeresen tekintse meg a szinkronizálási csoport előzményeinek naplóját. Győződjön meg arról, hogy az összes ütközés fel van oldva, és a módosítások a szinkronizálási csoport adatbázisaiban sikeresen propagálva lettek.

Előfordulhat, hogy egy szinkronizálási csoport nem tud alkalmazni egy módosítást az alábbi okok valamelyike miatt:

-   Séma inkompatibilitása a táblák között.
-   A táblák közötti adatkompatibilitás.
-   Null értéket tartalmazó sort szúr be egy olyan oszlopba, amely nem engedélyezi null értékek használatát.
-   Egy olyan értéket tartalmazó sor frissítése, amely megsért egy idegenkulcs-korlátozást.

Elavult szinkronizálási csoportok megelőzése:

-   Frissítse a sémát, hogy engedélyezze a hibás sorokban található értékeket.
-   Frissítse az idegenkulcs-értékeket a hibás sorokban található értékek belefoglalásához.
-   Frissítse a meghibásodott sorban lévő adatértékeket, hogy azok kompatibilisek legyenek a céladatbázis sémával vagy külső kulcsaival.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> A problémák megszüntetésének elkerülése

Bizonyos esetekben előfordulhat, hogy az ügyfél ügynökkel való regisztrációjának törlése miatt a szinkronizálás sikertelen lesz.

#### <a name="scenario"></a>Eset

1. Az A szinkronizálási csoport egy SQL Database-példány és egy SQL Server-adatbázis használatával lett létrehozva, amely az 1. helyi ügynökhöz van társítva.
2. Ugyanaz a helyszíni adatbázis regisztrálva van a (z) 2. helyi ügynökkel (ez az ügynök nincs hozzárendelve egyetlen szinkronizálási csoporthoz sem).
3. A helyszíni adatbázis helyi ügynökből való regisztrációjának törlése eltávolítja a helyszíni adatbázishoz tartozó A szinkronizálási csoport nyomon követését és meta tábláit.
4. A szinkronizálási csoport egy művelete meghiúsul, ezzel a hibával: "az aktuális művelet nem fejeződött be, mert az adatbázis nincs kiépítve szinkronizálásra, vagy Önnek nincs engedélye a szinkronizálási konfigurációs táblákhoz."

#### <a name="solution"></a>Megoldás

Ha el szeretné kerülni ezt a forgatókönyvet, ne regisztráljon egynél több ügynököt tartalmazó adatbázist.

Helyreállítás ebből a forgatókönyvből:

1. Távolítsa el az adatbázist az összes olyan szinkronizálási csoportból, amelyhez tartozik.  
2. Adja vissza az adatbázist minden olyan szinkronizálási csoportba, amelyet eltávolított a alkalmazásból.  
3. Telepítsen minden érintett szinkronizálási csoportot (ez a művelet kiépíti az adatbázist).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Szinkronizálási csoport módosítása

Ne kísérelje meg eltávolítani az adatbázist egy szinkronizálási csoportból, majd szerkessze a szinkronizálási csoportot anélkül, hogy először telepítené az egyik módosítást.

Ehelyett először távolítson el egy adatbázist egy szinkronizálási csoportból. Ezután végezze el a módosítást, és várjon, amíg a telepítés befejeződik. A megszüntetés befejezése után szerkesztheti a szinkronizálási csoportot, és telepítheti a módosításokat.

Ha megpróbálja eltávolítani az adatbázist, majd egy szinkronizálási csoportot anélkül szerkeszt, hogy először telepítené az egyik módosítást, akkor az egyik vagy a másik művelet meghiúsul. Előfordulhat, hogy a portál felülete inkonzisztens lesz. Ha ez történik, frissítse a lapot a megfelelő állapot visszaállításához.

### <a name="avoid-schema-refresh-timeout"></a>A séma-frissítés időtúllépésének elkerülése

Ha a szinkronizáláshoz összetett séma áll rendelkezésre, akkor a séma frissítése során a "művelet időtúllépése" kifejezés jelenhet meg, ha a szinkronizálási metaadat-adatbázis alacsonyabb SKU-val rendelkezik (például: alapszintű). 

#### <a name="solution"></a>Megoldás

A probléma megoldásához csökkentse a szinkronizálási metaadat-adatbázist úgy, hogy magasabb SKU-t (például S3) használjon. 

## <a name="next-steps"></a>Következő lépések
További információ a SQL-adatszinkronizálásról:

-   Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](sql-data-sync-data-sql-server-sql-database.md)
-   Az SQL Data Sync beállítása
    - A portálon – [oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a SQL Server közötti adatszinkronizáláshoz](sql-data-sync-sql-server-configure.md)
    - A PowerShell-lel
        -  [Több adatbázis közötti szinkronizálás a Azure SQL Database-ben a PowerShell használatával](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [SQL Database és egy SQL Server-példányban található adatbázis közötti szinkronizálás a PowerShell használatával](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-data-sync-agent-overview.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](./monitor-tune-overview.md)
-   Hibaelhárítás – [Az Azure SQL-adatszinkronizálás hibáinak elhárítása](sql-data-sync-troubleshoot.md)
-   A szinkronizálási séma frissítése
    -   A Transact-SQL- [ben – automatizálja a séma változásainak az Azure-ban való replikálását SQL-adatszinkronizálás](sql-data-sync-update-sync-schema.md)
    -   A PowerShell [használatával – egy meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítéséhez használja a PowerShellt](scripts/update-sync-schema-in-sync-group.md) .

További információ a SQL Databaseról:

-   [SQL Database áttekintése](sql-database-paas-overview.md)
-   [Adatbázis-életciklus kezelése](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
