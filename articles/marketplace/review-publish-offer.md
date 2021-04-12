---
title: Ajánlat áttekintése és közzététele a Microsoft kereskedelmi piactéren
description: A partner Center használatával küldje el ajánlatát az előzetes verzióra, tekintse meg az ajánlatát, majd tegye közzé a Microsoft kereskedelmi piactéren.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: f8ddcbef6d2ce4a9cba625374db9908335954424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630894"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Ajánlat áttekintése és közzététele a kereskedelmi piactéren

Ebből a cikkből megtudhatja, hogyan használhatja a partner centert az ajánlat közzétételre való benyújtására, az ajánlat előzetes megtekintésére, majd a kereskedelmi piactéren való közzétételre. Azt is bemutatjuk, hogyan ellenőrizheti a közzétételi állapotot a közzétételi lépések végrehajtásával. Már létre kell hoznia egy ajánlatot, amelyet közzé szeretne tenni.

## <a name="offer-status"></a>Ajánlat állapota

Az ajánlat állapotát a [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)kereskedelmi piactér irányítópultjának **Áttekintés** lapján tekintheti át. Az egyes ajánlatok **állapot** oszlopában az alábbi állapotjelzők egyike jelenik meg.

| Állapot | Leírás |
| ------------ | ------------- |
| Draft | Az ajánlat létre lett hozva, de nincs közzétéve. |
| Közzététel folyamatban | Az ajánlat a közzétételi folyamaton keresztül működik. |
| Figyelem szükséges | Egy kritikus problémát észlelt a minősítés vagy egy másik közzétételi fázis során. |
| Előnézet | Igazoljuk az ajánlatot, amely most már a közzétevő általi végleges ellenőrzést várja. Válassza az **élő** indítás lehetőséget az ajánlat élő közzétételéhez. |
| Élő | Az ajánlat a piactéren érhető el, és az ügyfelek megtekinthetik és megszerezhetik azokat. |
| Leállításra váró eladás | A kiadó "értékesítés leállítása" lehetőséget adott egy ajánlaton vagy terven, de a művelet még nem fejeződött be. |
| Nem érhető el a piactéren | A piactéren korábban közzétett ajánlat el lett távolítva. |
|||

## <a name="validation-and-publishing-steps"></a>Érvényesítési és közzétételi lépések

Ha készen áll egy ajánlat közzétételre való elküldésére, válassza a **felülvizsgálat és közzététel** lehetőséget a portál jobb felső sarkában. A **felülvizsgálat és közzététel** oldalon az ajánlat egyes lapjainak állapota látható, amely a következők egyike lehet:

   - **Nincs elindítva** – a lap hiányos.
   - **Hiányos** – a lap nem tartalmaz szükséges információkat, vagy hibákat kell rögzítenie. Vissza kell lépnie az oldalra, és frissítenie kell.
   - **Befejezés** – a lap elkészült. Minden szükséges információ meg lett adta, és nincsenek hibák.

Ha bármelyik oldalon a **befejezéstől** eltérő állapot van, javítsa ki a problémát az oldalon, majd térjen vissza a **felülvizsgálat és közzététel** lapra, és ellenőrizze, hogy az állapot most **készként** jelenik-e meg. Egyes ajánlati típusok tesztelést igényelnek. Ha igen, megjelenik a **minősítési mezőhöz tartozó megjegyzések** , ahol tesztelési utasításokat kell megadnia a minősítési csapatnak, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzéseket.

Miután az összes lap elkészült, és megadta a megfelelő tesztelési megjegyzéseket, válassza a **Közzététel** lehetőséget az érvényesítési és közzétételi folyamatok elindításához. A fázisok és a teljes szakasz a közzétett ajánlat típusától függően változhat. Az alábbi táblázat egy lehetséges közzétételi folyamatot mutat be. A következő szakaszok részletesebben ismertetik az egyes fázisokat.

| Fázis | Mi történik |
| ------------ | ------------- | ------------- |
| [Automatikus ellenőrzés](#automated-validation-phase) | Az automatizált érvényesítések egy készletét dolgozzák fel. |
| [Tanúsítvány](#certification-phase) | Manuális érvényesítést végezünk. |
| [Előnézet létrehozása](#preview-creation-phase) | Az ajánlat előzetes verziójának listázási lapja mindenki számára elérhető, aki rendelkezik az előzetes verziójú hivatkozással. Ha az ajánlatát a Microsoft (transactd) alapján értékesíti, akkor csak az ajánlat **előnézeti célközönsége** oldalon megadott célközönség vásárolhat és érheti el az ajánlatot tesztelésre. |
| [Közzétevő kijelentkezése](#publisher-sign-off-phase) | Egy e-mailt küldünk Önnek, amely az ajánlat előzetes megtekintését és jóváhagyását kéri. |
| [Közzététel](#publish-phase) | Egy sor lépést futtatunk annak ellenőrzéséhez, hogy az előzetes ajánlat közzé van-e téve élőben a kereskedelmi piactéren. |
|||

## <a name="automated-validation-phase"></a>Automatizált ellenőrzési fázis

A közzétételi folyamat első lépése az automatikus érvényesítések halmaza. Az egyes ellenőrzési lépések az ajánlat létrehozásakor kiválasztott szolgáltatásnak felelnek meg. Minden ellenőrzésnek teljesnek kell lennie, mielőtt az ajánlat továbblép a közzétételi folyamat következő lépésére.

- **Ajánlat-megvásárlási folyamat beállítása** (<10 perc)

   Gondoskodunk arról, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése** (~ 5 perc)

   Érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. Teszteljük és jóváhagyjuk a test Drive funkciót. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe** helyezése (~ 30 perc)

    Miután ellenőrizte az előző lépésben a tesztvezetés adatait és funkcióit, üzembe helyezheti és replikálhatja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció** (<15 perc)

    Megerősítjük, hogy a vezető felügyeleti rendszer az **ajánlat beállítása** oldalon megadott részletek alapján fogadja az ügyfelektől érkező érdeklődőket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

- **Ajánlat ellenőrzése** (<30 perc)

    Csak SaaS-ajánlatok esetén gyors érvényesítést végzünk a tartalom és a technikai konfigurációk esetében. Az előzetes verzió létrehozása nem lesz blokkolva az ebben a lépésben észlelt érvényesítési problémák esetén. Ha olyan problémát talál, amely sikertelen minősítést tartalmaz, az **ajánlat áttekintés** lapján megjelenik egy **megtekintési ellenőrzési jelentés** hivatkozása. A jelentés tartalmazni fogja a szabályzatok megsértésének leírását.

    > [!IMPORTANT]
    > Az **élő** adás gomb kiválasztása előtt ellenőrizze, hogy az ajánlat ellenőrzése lépésből származó összes figyelmeztetést megtekinti és kezeli. Ellenkező esetben nem fog tudni kockázatot jelentő minősítést biztosítani, amely késlelteti az ajánlatot élőben.

## <a name="certification-phase"></a>Minősítési fázis

A kereskedelmi piactérnek küldött ajánlatokat a közzététel előtt hitelesíteni kell. Az ajánlatok szigorú tesztelésen mennek keresztül, és néhány automatizált és egyéb manuális. További információ: [kereskedelmi piactér – minősítési szabályzatok](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>A minősítés során megkerülő érvényesítési típusok

Az egyes elküldött ajánlatok esetében három ellenőrzési szint szerepel a minősítési folyamatban.

- Kiadói üzleti jogosultság
- Tartalom érvényesítése
- Technikai ellenőrzés

#### <a name="publisher-business-eligibility"></a>Kiadói üzleti jogosultság

Minden ajánlat típusa a szükséges alap jogosultsági feltételek készletét ellenőrzi. Ez a feltétel magában foglalhatja a közzétevő MPN-állapotát, a megőrzött kompetenciákat, a kompetenciás szinteket és így tovább.

#### <a name="content-validation"></a>Tartalom érvényesítése

Az ajánlat létrehozásakor megadott adatokat a minőség és a relevancia alapján ellenőrzi a rendszer. Ezek az ellenőrzések áttekintik a piactér-lista részleteit, a díjszabást, a rendelkezésre állást, a társított csomagokat és így tovább. A Microsoft AppSource és az Azure Marketplace tőzsdei feltételeinek teljesítése érdekében a következőket érvényesítjük:

- Az ajánlatot pontosan leíró cím
- Jól megírt leírások, amelyek átfogó áttekintést és értéket biztosítanak
- Minőségi képernyőképek és videók
- Annak magyarázata, hogy az ajánlat hogyan használja a Microsoft platformokat és eszközöket.

A tartalom-ellenőrzési feltételekkel kapcsolatos további információkért olvassa el az [általános listázási szabályzatot](/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technikai ellenőrzés

A technikai ellenőrzés során az ajánlat (csomag vagy bináris) a következő ellenőrzéseken megy keresztül.

- Kártevő szoftverek vizsgálata
- Figyelt hálózati hívások
- Elemzett csomag
- Az ajánlat funkcióinak alapos vizsgálata

Az ajánlat a különböző platformokon és verziókon tesztelve biztosítja a robusztus működést.

### <a name="certification-failure-report"></a>Sikertelen minősítési jelentés

Ha az ajánlata nem felel meg a tőzsdei, műszaki vagy házirend-ellenőrzéseknek, vagy ha nem jogosult ilyen típusú ajánlat elküldésére, e-mailt küldünk Önnek a minősítési hibajelentésről.

Ez a jelentés a sikertelen házirendek leírásait tartalmazza, valamint a felülvizsgálati megjegyzéseket. Tekintse át az e-mail-jelentést, a problémák megoldását, az ajánlat frissítését, ahol szükséges, majd küldje el újra az ajánlatot a partner Center [kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) . A minősítés átadása előtt többször is elküldheti az ajánlatot.

## <a name="preview-creation-phase"></a>Előnézet-létrehozási fázis

Az előzetes verziójú létrehozási fázisban az ajánlat egy olyan verzióját hozunk létre, amely csak az ajánlat **előnézeti közönség** lapján megadott célközönség számára érhető el, ha van ilyen. Az ajánlat előzetes verziója nem érhető el mindenki számára az előnézeti közönségen kívül, amíg nem teszi közzé az ajánlatot élőben.

> [!NOTE]
> Ne használja az előzetes verzió célközönségét, hogy a szervezeten kívüli személyek is láthassanak egy ajánlatot. Használja helyette a privát ajánlat lehetőséget. Ezen a ponton az ajánlat nem lett teljes körűen tesztelve és érvényesítve, és nem áll készen a külső terjesztésre.

## <a name="cancel-publishing"></a>Közzététel megszakítása

Ajánlat megszakítása a **Közzététel folyamatban** állapotában:

1. Válassza ki az ajánlat nevét az **ajánlat áttekintő** oldalának megnyitásához.
1. Kattintson a **Közzététel megszakítása gombra** a lap jobb felső sarkában.
1. Erősítse meg, hogy le kívánja állítani az ajánlat közzétételét.

Ha egy későbbi időpontban szeretné közzétenni az ajánlatot, el kell indítania a közzétételi folyamatot.

> [!NOTE]
> Az ajánlat csak akkor állítható le, ha az ajánlat még nem haladt előre a közzétevő kijelentkezési lépésén. Miután kiválasztotta a **Go Live** funkciót, nem fogja tudni megszakítani a közzétételt.

## <a name="publisher-sign-off-phase"></a>Közzétevő kijelentkezési fázisa

Ha az ajánlat készen áll arra, hogy áttekintse és kijelentkezzen, küldjön Önnek egy e-mailt, amely kéri, hogy tekintse át és hagyja jóvá az ajánlat előzetes verzióját. Az **ajánlat áttekintés** lapját is frissítheti a böngészőben, és megtudhatja, hogy az ajánlat elérte-e a közzétevő kijelentkezési fázisát. Ha igen, a **Go Live** gomb és az előzetes verziójú hivatkozások is elérhetők lesznek.

Az alábbi képernyőfelvételen a partner Center kereskedelmi Marketplace programjának **ajánlat áttekintése** lapja látható. Az ezen az oldalon látható ellenőrzési lépések az ajánlat típusától és az ajánlat létrehozásakor létrehozott beállításoktól függően változnak. Vegye figyelembe, hogy a minősítési fázis a beküldés során megtörténhet az előzetes verzióra vagy a küldésre az ajánlat típusától függően.

![A partneri központban elérhető ajánlat áttekintés lapját mutatja be. Megjelenik a Go Live gomb és az előzetes verzió hivatkozásai.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Az ajánlat előzetes megtekintése és jóváhagyása

> [!IMPORTANT]
> A végpontok közötti vásárlási és beállítási folyamat ellenőrzéséhez vásárolja meg ajánlatát, amíg az előzetes verzióban is elérhető. Először értesítse a Microsoftot egy [támogatási jegyről](https://aka.ms/marketplacesupport) , hogy ne dolgozzon fel díjat. Ha az ajánlata egy _kapcsolatfelvételi_ listával rendelkezik, tesztelje, hogy az érdeklődő a várt módon jön létre, ha az előzetes verzióban megadja a kapcsolatfelvételi adatokat.

Az **ajánlat áttekintése** lapon a **Go Live** gomb alatt láthatók az előzetes verziójú hivatkozások. A AppSource előzetes verziójához, az Azure Marketplace előzetes verziójához vagy mindkettőhöz az ajánlat létrehozásakor választott beállításoktól függően lesz hivatkozás. Ha úgy dönt, hogy az ajánlatot a Microsofton keresztül értékesíti, akkor bárki, aki hozzá lett adva az előzetes verzió célközönségéhez, tesztelheti az ajánlat beszerzését és üzembe helyezését, hogy az megfeleljen a jelen szakaszban foglalt követelményeknek.

> [!IMPORTANT]
> Az **élő** adás gomb kiválasztása előtt ellenőrizze, hogy az _ajánlat ellenőrzése_ lépésből származó összes figyelmeztetést megtekinti és kezeli. Ellenkező esetben nem fog tudni kockázatot jelentő minősítést biztosítani, amely késlelteti az ajánlatot élőben.

Az előzetes verzió jóváhagyása után válassza az élő adás lehetőséget, hogy az ajánlat élőben **elérhető** legyen a kereskedelmi piactéren. 

Ha módosítani kívánja az ajánlat előzetes megtekintését követően, szerkesztheti és újra elküldheti a közzétételi kérelmet. Ha az ajánlata már él és elérhető a piactéren, az Ön által végzett frissítések csak akkor lépnek életbe, ha a **Go Live* lehetőséget választja. További információ: [meglévő ajánlat frissítése a kereskedelmi piactéren](partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Közzétételi fázis

Most, hogy úgy döntött, hogy az ajánlatával él, ami elérhetővé teszi a kereskedelmi piactéren, egy sor végső ellenőrzési ellenőrzést végzünk, hogy az élő ajánlat ugyanúgy legyen konfigurálva, mint az ajánlat előzetes verziója.

- **Ajánlat-megvásárlási folyamat beállítása** (>10 perc)

    Gondoskodunk arról, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése** (~ 5 perc)

    Érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. Teszteljük és jóváhagyjuk a test Drive funkciót. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe** helyezése (~ 30 perc)

    Üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció** (>15 perc)

    Megerősítjük, hogy a vezető felügyeleti rendszer az **ajánlat beállítása** oldalon megadott részletek alapján fogadja az ügyfél-érdeklődőket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

- **Végső közzététel (>30 perc)**

    Garantáljuk, hogy az ajánlat nyilvánosan elérhetővé válik a piactéren.

Az ellenőrzés befejezése után az ajánlat a piactéren lesz elérhető.

## <a name="publishing-history"></a>Közzétételi előzmények

A partner Center **Előzmények** lapja a kereskedelmi Piactéri ajánlatok közzétételi eseményeit jeleníti meg. A lap minden eseménynél megjeleníti a műveletet kezdeményező felhasználót, az esemény típusát, valamint az esemény dátumát és időpontját. Az [érvényesítési és közzétételi lépések](#validation-and-publishing-steps) a Befejezés dátumával és időpontjával vannak felsorolva.

Az ajánlat előzményeinek megtekintése:

1.    Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2.    A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
3.    Válassza ki az ajánlatot.
4.    Kattintson a bal oldali navigációs menü **Előzmények** fülére. Az oldal a következő eseményekre vonatkozó adatokat tartalmazza, az ajánlatának megfelelően:

|Esemény    |Leírás    |
|---------|---------------|
|Ajánlat létrehozva    |Az ajánlat a partner Centerben lett létrehozva. A felhasználó az ajánlat típusát, az ajánlat azonosítóját és az ajánlati aliast választotta a **kereskedelmi piactér**  >  **áttekintésében**.    |
|Terv létrehozva: *terv neve*    |A felhasználó létrehozta az új csomagot a csomag **áttekintése** lapon a terv azonosítója és a terv nevének megadásával.</br>*Ez az esemény csak a csomagokat támogató csomagokra vonatkozik*.    |
|Csomag törölve    |A felhasználó törölte a terv **áttekintő** oldalának **Törlés** parancsát, és nem tették közzé a piszkozatot.</br>*Ez az esemény csak a csomagokat támogató csomagokra vonatkozik*.    |
|Kezdeményezett terv leállítása: *csomag neve*    |A felhasználó a terv **áttekintése** lapon lévő **értékesítés leállítása** lehetőség kiválasztásával kezdeményezte a csomag leállítását.</br>*Ez az esemény csak a csomagokat támogató csomagokra vonatkozik*.    |
|Terv visszavonása – eladás: *terv neve*    |A felhasználó megszakította a csomag leállítását. Ehhez válassza az **értékesítés leállítása** a **terv áttekintése** lapról lehetőséget.</br>*Ez az esemény csak a csomagokat támogató csomagokra vonatkozik*.    |
|Előnézeti ajánlat elküldve    |A felhasználók a **felülvizsgálat és közzététel** lap Közzététel elemére kattintva elküldték az ajánlatot az előzetes **verzióra** .    |
|Elindított elküldés az előnézet lemondásához    |Egy felhasználó az ajánlat közzétételének megszakítása az előzetes verzióra való beküldést követően az **ajánlat áttekintése** lapon, a **Közzététel megszakítása** lehetőség választásával.</br>*Ez az esemény a törlési kérelem feldolgozásakor jelenik meg*.    |
|Megszakított küldés az előzetes verzióra    |A felhasználó megszakította az ajánlat közzétételét az előzetes **verzióra, ha a közzététel megszakítva** lehetőséget választja az **ajánlat áttekintése** lapon az előnézet megadását követően.</br>*Ez az esemény a törlési kérelem sikeres feldolgozását követően jelenik meg*.    |
|Bejelentkezés a Go Live szolgáltatásba    |A felhasználók a kereskedelmi piactéren közzétették az ajánlatot, ha az **ajánlat áttekintése** oldalon a **Go Live** lehetőségre kattintanak.    |
|Közzététel kezdeményezése a piactéren – visszavonás    |A felhasználó az ajánlat közzétételének megszakítására kérte a **Közzététel megszakítása** az **ajánlat áttekintése** lapon, miután a bejelentkezés megszakítva állapotba lép.</br>*Ez az esemény a törlési kérelem feldolgozásakor jelenik meg*.    |
|A kereskedelmi piactéren való közzététel megszakítva    |A felhasználó megszakította az ajánlat közzétételét. ehhez kattintson a **Közzététel megszakítása** lehetőségre az **ajánlat áttekintése** oldalon a bejelentkezés előtt.</br>*Ez az esemény a törlési kérelem sikeres feldolgozását követően jelenik meg*.    |
|Privát célközönség szinkronizálása    |A felhasználó frissítette és szinkronizálta a privát célközönséget a **saját célközönség szinkronizálása** lehetőség kiválasztásával a **terv áttekintése** lapon vagy a **csomag díjszabása & rendelkezésre állási** lapon.</br>*Ez az esemény csak a privát terveket támogató ajánlati típusokra vonatkozik*.    |
|Eladási ajánlat leállítása    |A felhasználó leállította az ajánlat értékesítését az **ajánlat áttekintése** oldalon található **értékesítés leállítása** lehetőség kiválasztásával.    |

> [!NOTE]
> Az Előzmények lap nem jelenik meg, amikor a rendszer mentette az ajánlat piszkozatát.

### <a name="filter-options"></a>Szűrési lehetőségek

A szűrők használatával szűkítheti az ajánlat teljes előzményeit adott közzétételi eseményekre:

1.    Kattintson a szűrő gombra a lap jobb felső sarkában.
2.    Válasszon ki egy szűrőt, majd válassza az **alkalmaz** lehetőséget, hogy megtekintse, mely események egyeznek meg a kiválasztott feltételekkel.
3.    Válassza a **Szűrők törlése** lehetőséget az ajánlat teljes előzményeihez való visszatéréshez.

Négy szűrő van:
* Események
* Felhasználók
* Date
* Oldalak

Az **oldalak** szűrő kiválasztásakor kiválaszthatja a partneri központ azon lapjait, amelyek alkalmazhatók az ajánlat típusára. Ha alkalmazva van, a **Pages (oldalak** ) szűrő az összes elküldött ajánlatot megjeleníti az eseményeknek a kiválasztott oldal változásaival való **előnézetéhez** .

* Minden ajánlat esetében az **ajánlat beállítása** lap alapértelmezés szerint az egyes beküldési eseményekhez tartozik.
* A támogatási csomagokkal rendelkező ajánlatok esetében a **terv áttekintő** lapja szerepel az egyes beküldési eseményeknél.
* A test Drive-t támogató ajánlatok esetében minden beküldési eseményhez meg kell adni a **tesztvezetés** lapot.

### <a name="users"></a>Felhasználók

Ha egy felhasználó kezdeményezte az eseményt, az Előzmények lapon a következő forgatókönyvek szerint jelenik meg a felhasználó:

#### <a name="the-event-was-initiated-by-the-publisher"></a>Az eseményt a közzétevő kezdeményezte

Az ajánlatok közzétételi engedélyekkel rendelkező felhasználóinak neve megjelenik az általuk kezdeményezett közzétételi eseményeknél.

[![Az Előzmények lap megjeleníti a közzétételi engedélyekkel rendelkező felhasználók nevét.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>Az eseményt a Microsoft kezdeményezte

Engedélyt adhat a Microsoft rendszergazdái számára, hogy az Ön nevében műveleteket kezdeményezzenek, vagy váratlan rendszerhiba után végezze el a korrekciós műveleteket. A Microsoft neve és emblémája a Microsoft által a fiókja nevében kezdeményezett események közzétételéhez jelenik meg.

[![Példa az előzmények lapra a Microsoft által kezdeményezett események megjelenítéséhez.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>Az eseményt egy azonosítatlan felhasználó kezdeményezte

Azok a felhasználók, akik már nincsenek társítva egy fiókkal, el lesznek távolítva a **kezdeményező** oszlopból a közzétételi engedélyeik visszavonása után.

[![Példa az előzmények lapra az azonosítatlan felhasználók által kezdeményezett események megjelenítéséhez.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Érvényesítési és közzétételi lépések

Az [érvényesítési és a közzétételi lépéseknek](#validation-and-publishing-steps)megfelelő rendszerfolyamatok nem jelennek meg a felhasználónál. Ezek az események az esemény befejezési állapota szerint színkódoltak.

[![Példa arra, hogy az előzmények oldal hogyan jeleníti meg az érvényesítési és közzétételi lépéseket.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Következő lépések

[A partner Center kereskedelmi piactérről származó analitikai jelentések elérése](partner-center-portal/analytics.md)
