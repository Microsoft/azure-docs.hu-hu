---
title: Azure Backup-jelentések konfigurálása
description: Jelentések konfigurálása és megtekintése Azure Backup Log Analytics és Azure-munkafüzetek használatával
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0f3638e7649fc02f050c575ee621ce9dc237c24f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517266"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

A biztonsági mentések rendszergazdái számára gyakori követelmény a hosszú ideig tartó adatokon alapuló biztonsági másolatok elemzésének beszerzése. Az ilyen megoldások a következő esetekben használhatók:

- A felhasznált felhőalapú tárterület allokálása és előrejelzése.
- Biztonsági mentések és visszaállítások naplózása.
- A fő trendek azonosítása a részletesség különböző szintjein.

A Azure Backup jelenleg olyan jelentéskészítési megoldást kínál, amely Azure Monitor [és](../azure-monitor/logs/log-analytics-tutorial.md) [Azure-munkafüzeteket használ.](../azure-monitor/visualize/workbooks-overview.md) Ezek az erőforrások segítenek a teljes biztonsági mentési tulajdon biztonsági másolataiba betekintést nyerhet. Ez a cikk a jelentések konfigurálásának és megtekintésének Azure Backup ismerteti.

## <a name="supported-scenarios"></a>Támogatott esetek

- A biztonsági mentési jelentések támogatottak az Azure-beli virtuális gépek, az Azure-beli virtuális gépeken az SQL, SAP HANA Azure-beli virtuális gépeken, Microsoft Azure Recovery Services- (MARS-) ügynök, Microsoft Azure Backup Server (MABS) és System Center Data Protection Manager (DPM) esetén. Az Azure-fájlmegosztások biztonsági mentésekor a 2020. június 1-jén vagy azt követően létrehozott rekordok adatai jelennek meg.
- Az Azure-fájlmegosztások biztonsági mentése esetén a védett példányok adatai a 2021. február 1. után létrehozott rekordoknál jelennek meg (régebbi rekordok esetén az alapértelmezett érték nulla).
- A DPM-munkaterhelések esetében a biztonsági mentési jelentések a DPM 5.1.363.0-s és újabb, valamint az ügynök 2.0.9127.0-s és újabb verziói esetében támogatottak.
- MABS számítási feladatok esetén a Backup-jelentések a MABS 13.0.415.0-s és újabb, valamint a 2.0.9170.0-s és újabb verziók esetében támogatottak.
- A biztonsági mentési jelentések megtekinthetők az összes biztonsági mentési elemben, tárolóban, előfizetésben és régióban, ha az adataik olyan Log Analytics-munkaterületre vannak küldve, amelyhez a felhasználónak hozzáférése van. Egy tárolókészlet jelentésének megtekintéséhez csak olvasói hozzáférésre van szüksége ahhoz a Log Analytics-munkaterülethez, amelyre a tárolók elküldik az adataikat. Az egyes tárolókhoz nem kell hozzáférnie.
- Ha Ön egy [](../lighthouse/index.yml) Azure Lighthouse hozzáféréssel rendelkezik az ügyfelek előfizetéséhez, ezeket a jelentéseket a Azure Lighthouse-val használhatja az összes bérlő jelentésének megtekintéséhez.
- Az adatok jelenleg legfeljebb Backup-jelentések 100 Log Analytics-munkaterületen megtekinthetők (bérlők között).
- A naplók biztonsági mentési feladatának adatai jelenleg nem jelennek meg a jelentésekben.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Bevezetés

A jelentések használatának elkezdenie az alábbi lépéseket.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Log Analytics-munkaterület létrehozása vagy meglévő használata

Állítson be egy vagy több Log Analytics-munkaterületet a biztonsági mentési jelentési adatok tárolására. A Log Analytics-munkaterület létrehozási helye és előfizetése független a tárolók helyétől és előfizetésétől.

Log Analytics-munkaterület beállításával a Log Analytics-munkaterület létrehozása [a Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)

Alapértelmezés szerint a Log Analytics-munkaterületen lévő adatok 30 napig maradnak meg. Ha hosszabb időhorizontra vonatkozó adatokat keres, módosítsa a Log Analytics-munkaterület megőrzési idejét. A megőrzési időtartammal kapcsolatban lásd: Használat és költségek [kezelése az Azure Monitor naplókban.](../azure-monitor/logs/manage-cost-storage.md)

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. A tárolók diagnosztikai beállításainak konfigurálása

Azure Resource Manager, például Recovery Services-tárolók, diagnosztikai adatokként rögzítik az ütemezett és a felhasználó által aktivált műveletek adatait.

A Recovery Services-tároló monitorozási  szakaszában válassza a Diagnosztikai beállítások lehetőséget, és adja meg a helyreállítási tár diagnosztikai adatainak célját. A diagnosztikai események használatával kapcsolatos további információkért lásd: Diagnosztikai beállítások [használata Recovery Services-tárolókhoz.](./backup-azure-diagnostic-events.md)

![Diagnosztikai beállítások panel](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup beépített definíciót is Azure Policy, amely automatizálja az adott hatókör összes tárolójának diagnosztikai beállításainak konfigurálást. A szabályzat használatának elsajátításért lásd: [Tárolódiagnosztikai beállítások konfigurálása nagy méretekben.](./azure-policy-configure-diagnostics.md)

> [!NOTE]
> A diagnosztika konfigurálása után a kezdeti adatküldés akár 24 órát is igénybe vehet. Miután az adatok elkezdnek a Log Analytics-munkaterületre áramlni, előfordulhat, hogy nem jelennek meg azonnal az adatok a jelentésekben, mert az aktuális részleges nap adatai nem jelennek meg a jelentésekben. További információ: Biztonsági mentési [jelentésekben használt konvenciók.](#conventions-used-in-backup-reports) Javasoljuk, hogy a tárolók Log Analyticsbe való elküldését követően két nappal kezdje meg a jelentések megtekintését.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Jelentések megtekintése a Azure Portal

Miután úgy konfigurálta a tárolókat, hogy adatokat küldjenek a Log Analyticsnek, tekintse meg a Backup-jelentéseket úgy, hogy bármelyik tároló paneljére, majd az **Backup-jelentések.**

![Tároló irányítópultja](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kattintson erre a hivatkozásra a Backup jelentés munkafüzetének megnyitásához.

> [!NOTE]
>
> - Jelenleg a jelentés kezdeti betöltése akár 1 percet is igénybe vehet.
> - A Recovery Services-tároló csupán belépési pont a Backup-jelentésekhez. Miután megnyílik a Backup jelentés munkafüzete egy tároló paneljéből, válassza ki a megfelelő Log Analytics-munkaterületeket az összes tárolóban összesített adatokhoz.

A jelentés különböző lapokat tartalmaz:

##### <a name="summary"></a>Összefoglalás

Ezen a lapon magas szintű áttekintést kaphat a biztonsági mentési tulajdonról. Gyors áttekintést kaphat a biztonsági másolati elemek teljes számáról, a felhasznált felhőbeli tárterületről, a védett példányok számáról és a feladat sikerességéhez számítási feladattípusonkénti arányról. Az adott biztonságimásolat-összetevőtípussal kapcsolatos részletesebb információkért a megfelelő lapokon jelennek meg.

   ![Összegzés lap](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Biztonsági másolati elemei

Ezen a lapon a Biztonsági másolat elem szintjén felhasznált felhőalapú tárolással kapcsolatos információkat és trendeket láthatja. Ha például SQL-t használ egy Azure-beli virtuális gép biztonsági mentéséhez, láthatja a felhőbeli tárhelyet, amelyről biztonsági mentést készít. Választhatja azt is, hogy egy adott védelmi állapotú biztonsági mentési elemek adatait is látni fogja. A lap tetején található **Védelem** leállítva csempe például úgy szűri az alatta lévő összes widgetet, hogy csak a Védelem leállítva állapotban lévő biztonsági mentési elemek adatait mutassa.

   ![Biztonsági mentési elemek lap](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Használat

Ezen a lapon megtekintheti a biztonsági másolatok legfontosabb számlázási paramétereit. Az ezen a lapon megjelenő információk a számlázási entitások (védett tárolók) szintjén jelennek meg. Ha például egy DPM-kiszolgálóról biztonságimentés folyamatban van az Azure-ban, megtekintheti a DPM-kiszolgáló által felhasznált védett példányok és felhőalapú tárhely trendjét. Hasonlóképpen, ha az SQL-t az Azure Backup vagy SAP HANA Azure Backup-ban használja, ez a lap a használattal kapcsolatos információkat nyújt azon virtuális gép szintjén, amelyben az adatbázisok találhatóak.

   ![Használat lap](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> A DPM számítási feladatok esetében a felhasználók kis eltérést tapasztalhatnak (DPM-kiszolgálónkénti 20 MB-os sorrendben) a jelentésekben  látható használati értékek között a Recovery Services-tároló Áttekintés lapján látható összesített használati értékhez képest. Ezt a különbséget az jelenti, hogy minden, biztonsági mentésre regisztrált DPM-kiszolgáló rendelkezik egy társított metaadat-adatforrással, amely nem jelentéskészítési összetevőként van kivetve.

##### <a name="jobs"></a>Feladatok

Ezen a lapon megtekintheti a feladatok hosszan futó trendjeit, például a sikertelen feladatok napi számát és a sikertelen feladatok leggyakoribb okait. Ezek az információk összesítési szinten és Biztonságimásolat-elem szinten is megtekinthetők. Jelöljön ki egy adott Biztonságimásolat-elemet egy rácsban a kiválasztott időtartományon belül az adott Biztonságimásolat-elemen aktivált egyes feladat részletes információinak megtekintéséhez.

   ![Feladatok lap](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Házirendek

Ezen a lapon megtekintheti az összes aktív szabályzat adatait, például a társított elemek számát és az egy adott szabályzatban biztonságiolt elemek által felhasznált teljes felhőbeli tárterületet. Válasszon ki egy adott szabályzatot az egyes társított Biztonságimásolat-elemek információinak megtekintéséhez.

   ![Szabályzatok lap](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optimalizálás

Ezen a lapon betekintést nyerhet a biztonsági másolatok lehetséges költségoptimalizálási lehetőségeibe. Az alábbi forgatókönyvekhez az Optimalizálás lap nyújt jelenleg elemzéseket:

###### <a name="inactive-resources"></a>Inaktív erőforrások

Ezzel a nézetben azonosíthatja azokat a biztonsági mentési elemeket, amelyek jelentős ideje nem voltak sikeresek. Ez azt is jelentheti, hogy a mögöttes gép, amelyről biztonsági másolatot készít, már nem létezik (ezért sikertelen biztonsági mentéseket eredményez), vagy valamilyen probléma van a géppel, amely megakadályozza a biztonsági mentések megbízható készítését.

Az inaktív erőforrások megtekintéséhez lépjen az **Optimalizálás lapra,** és válassza az **Inaktív erőforrások csempét.** Ha kiválasztja ezt a csempét, megjelenik egy rács, amely a kiválasztott hatókörben található összes inaktív erőforrás részleteit tartalmazza. Alapértelmezés szerint a rács azokat az elemeket jeleníti meg, amelyek nem tartalmaznak helyreállítási pontot az elmúlt hét napban. Ha egy másik időtartományhoz inaktív erőforrásokat  keres, a lap tetején módosíthatja az Időtartomány szűrőt.

Ha azonosított egy inaktív erőforrást, a probléma további kivizsgálásához navigáljon az adott erőforráshoz a biztonsági mentési elem irányítópultjára vagy az Azure-erőforrás panelére (ahol van). A forgatókönyvtől függően leállíthatja a gép biztonsági mentését (ha az már nem létezik), és törölheti a szükségtelen biztonsági másolatokat, ami költségeket takarít meg, vagy kijavíthatja a gép problémáit a biztonsági mentések megbízható készítésének biztosítása érdekében.

![Optimalizálás lap – Inaktív erőforrások](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Hosszú adatmegőrzési időtartamú biztonsági másolati elemek

Ezzel a nézettel azonosíthatja azokat az elemeket, amelyekről a szervezet által előírtnál hosszabb ideig őrzi meg a biztonsági másolatokat.

Ha  kiválasztja a Szabályzatoptimalizálások csempét, majd a Megtartás optimalizálása csempét, megjelenik egy rács, amely tartalmazza az összes olyan biztonsági mentési elemet, amelynek napi, heti, havi vagy éves megőrzési pontja (RP) nagyobb, mint a megadott érték.  Alapértelmezés szerint a rács a kijelölt hatókör összes biztonsági mentési elemét megjeleníti. A napi, heti, havi és éves RP-megőrzés szűrőivel tovább szűrheti a rácsot, és azonosíthatja azokat az elemeket, amelyeknél a megőrzés csökkenthető a biztonsági másolatok tárolási költségeinek csökkentése érdekében.

Az olyan adatbázis-munkaterhelések esetében, mint az SQL és SAP HANA, a rácsban látható megőrzési időtartamok a teljes biztonsági mentési pontok megőrzési időszakának felelnek meg, nem a különbségi biztonsági mentési pontoknak. Ugyanez vonatkozik a megtartási szűrőkre is.  

![Optimalizálás lap – Adatmegőrzés optimalizálása](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Napi rendszerességű teljes biztonsági mentéshez beállított adatbázisok 

Ezzel a nézettel azonosíthatja az adatbázis azon számítási feladatait, amelyek napi teljes biztonsági mentésre vannak konfigurálva. A napi különbözeti biztonsági mentés és a heti teljes biztonsági mentés használata gyakran költséghatékonyabb.

A **Szabályzatoptimalizálás csempét,** majd a **Biztonsági** mentés ütemezésének optimalizálása csempét választva megjelenik egy rács, amely az összes adatbázist tartalmazza napi rendszerességgel teljes biztonsági mentési szabályzatokkal. Választhat, hogy egy adott biztonsági mentési elemre navigál, és úgy módosítja a szabályzatot, hogy a napi különbözeti biztonsági mentést használja a heti teljes biztonsági mentéssel.

A **lap tetején** található Biztonságimásolat-kezelés típusa szűrőnek ki kell választania az Sql in Azure VM and SAP HANA in Azure VM (Sql az **Azure-beli** virtuális gépen és az **Azure-beli** virtuális gépen) elemeket, hogy a rács a várt módon tudja megjeleníteni az adatbázis számítási feladatait.

![Optimalizálás lap – Biztonsági mentés ütemezésének optimalizálása](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Szabályzatbetartja

Ezen a lapon megállapíthatja, hogy az összes biztonsági mentési példányon volt-e legalább egy sikeres biztonsági mentés minden nap. A heti biztonsági mentési szabályzatot használva ezen a lapon megállapíthatja, hogy az összes biztonsági mentési példányon volt-e legalább egy sikeres biztonsági mentés hetente.

A szabályzatbetartani nézeteknek két típusa érhető el:

* **Szabályzatbetartjaás** adott időszak szerint: Ebben a nézetben megállapíthatja, hogy hány elemről volt legalább egy sikeres biztonsági mentés egy adott napon, és hány nem volt sikeres biztonsági mentés az adott napon. Egy sorra kattintva a kiválasztott napon aktivált összes biztonsági mentési feladat részleteit láthatja. Vegye figyelembe, hogy ha megnöveli az időtartományt egy nagyobb értékre, például az elmúlt 60 napra, a rács heti nézetben jelenik meg, és megjeleníti azon elemek számát, amelyekről legalább egy sikeres biztonsági mentés történt az adott hét minden napján. Hasonlóképpen, a nagyobb időtartományok havi nézete is van.

A heti biztonsági mentésű elemek esetén ez a rács segít azonosítani az összes olyan elemet, amely legalább egy sikeres biztonsági mentéssel járt az adott héten. Nagyobb időtartományok, például az elmúlt 120 nap esetén a rács havi nézetben jelenik meg, és megjeleníti azon elemek számát, amelyekről hetente legalább egy sikeres biztonsági mentés történt az adott hónapban. A [napi, heti Backup-jelentések](#conventions-used-in-backup-reports) havi megtekintésekkel kapcsolatos további részletekért tekintse meg a következőben használt konvenciókat:

![Szabályzatbetartjaás az időszak szerint](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Szabályzatbetartjaás biztonságimásolat-példány** szerint: Ezzel a nézettel a biztonságimásolat-példány szintjén használhatja a szabályzatok betartásának részleteit. Egy zöld színű cella azt jelzi, hogy a biztonsági mentési példánynak legalább egy sikeres biztonsági mentése volt az adott napon. Egy piros színű cella azt jelzi, hogy a biztonsági mentési példánynak még egyetlen sikeres biztonsági mentés sem volt az adott napon. A napi, heti és havi aggregációk ugyanazt a viselkedést követik, mint a Szabályzatok betartása az időszak szerint nézetben. Bármelyik sorra kattintva megtekintheti az adott biztonsági mentési példányon a kiválasztott időtartományban lévő összes biztonsági mentési feladat megtekintését.

![Szabályzat betartatás biztonságimásolat-példány szerint](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>E-Azure Backup jelentések

Az **E-mail-jelentés** funkcióval, amely a Backup-jelentések, automatizált feladatokat hozhat létre, amelyek rendszeres jelentéseket fogadnak e-mailben. Ez a funkció úgy működik, hogy üzembe helyez egy logikai alkalmazást az Azure-környezetben, amely a megadott bemenetek alapján lekérdezi az adatokat a kiválasztott Log Analytics- (LA-) munkaterületről.

A logikai alkalmazás létrehozása után engedélyeznie kell a kapcsolatokat a naplókhoz Azure Monitor Office 365-beli kapcsolatokhoz. Ehhez lépjen a Logic Apps **a** Azure Portal, és keresse meg a létrehozott feladat nevét. Az **API-kapcsolatok menüpont** kiválasztásával megnyílik az engedélyezni kívánt API-kapcsolatok listája. [További információ az e-mailek konfigurálásáról és a problémák elhárításáról.](backup-reports-email.md)

###### <a name="customize-azure-backup-reports"></a>Jelentések Azure Backup testreszabása

Backup-jelentések [rendszerfunkciókat használ a Azure Monitor naplókban.](backup-reports-system-functions.md) Ezek a függvények az LA nyers Azure Backup tábláiban található adatokon működnek, és formázott adatokat adnak vissza, amelyek egyszerű lekérdezések használatával megkönnyítik a biztonsági mentéssel kapcsolatos entitások adatainak lekérését. 

Ha saját jelentéskészítési munkafüzeteket szeretne létrehozni Backup-jelentések használatával alapként, lépjen  a Backup-jelentések elemre, kattintson a jelentés tetején található Szerkesztés gombra, és tekintse meg/szerkessze a jelentésekben használt lekérdezéseket. Az [egyéni jelentések létrehozásáról az Azure-munkafüzetek](../azure-monitor/visualize/workbooks-overview.md) dokumentációjában talál további információt. 

## <a name="export-to-excel"></a>Exportálás Excelbe

Kattintson a lefelé mutató nyíl gombra bármely widget jobb felső sarkában, például egy táblázatban vagy diagramban, hogy a widget tartalmát Excel-munkalapként exportálja a meglévő szűrők alkalmazásával. Egy táblázat több sorának Excelbe való exportálásához növelheti az oldalon megjelenő  sorok számát az egyes rácsok tetején található Sorok oldalanként legördülő nyíllal.

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton

Az egyes widgetek tetején található Rögzítés gombra kattintva rögzítheti a widgetet a Azure Portal irányítópultján. Ezzel a funkcióval testreszabott irányítópultokat hozhat létre, amelyek a legfontosabb információk megjelenítésére vannak szabva.

## <a name="cross-tenant-reports"></a>Több-bérlős jelentések

Ha delegált [Azure Lighthouse](../lighthouse/index.yml) használ több bérlői környezet előfizetéséhez, használhatja az alapértelmezett előfizetési szűrőt. Válassza a szűrő gombot a képernyő jobb felső sarkában Azure Portal válassza ki az összes előfizetést, amelyhez adatokat szeretne látni. Így több-bérlős jelentések megtekintéséhez kiválaszthatja a bérlők Log Analytics-munkaterületeit.

## <a name="conventions-used-in-backup-reports"></a>A Backup-jelentésekben használt konvenciók

- A szűrők minden lapon balról jobbra, illetve fentről lefelé működnek. Ez azt jelenti, hogy minden szűrő csak azokra a widgetre vonatkozik, amelyek az adott szűrőtől jobbra vagy alatta vannak eltűrve.
- Egy színes csempe kiválasztásával a csempe alatti widgetek szűrik a csempe értékére vonatkozó rekordokat. Ha például a  Biztonsági másolati  elemek lapon kiválasztja a Védelem leállítva csempét, az alábbi rácsokat és diagramokat szűri, hogy a Védelem leállítva állapotban lévő biztonsági mentési elemek adatait mutassa.
- A nem színes csempék nem választhatók ki.
- Az aktuális részleges nap adatai nem jelennek meg a jelentésekben. Így ha az Időtartomány kiválasztott értéke Az elmúlt **7** nap, a jelentés az utolsó hét befejezett nap rekordjait jeleníti meg.  Az aktuális napot nem tartalmazza.
- A jelentés a kiválasztott időtartományban aktivált  feladatok részleteit jeleníti meg (a napló feladatokon kívül).
- A Cloud **Storage** és **a Védett** példányok esetén megjelenő értékek a kiválasztott időtartomány végén vannak. 
- A jelentésekben megjelenített Biztonsági másolat elemek azok  az elemek, amelyek a kiválasztott időtartomány végén jelennek meg. A kiválasztott időtartomány közepén törölt biztonsági mentési elemek nem jelennek meg. Ugyanez a konvenció érvényes a biztonsági mentési szabályzatok esetén is.
- Ha a kiválasztott időtartomány 30 napnál rövidebb időszakra esik, a diagramok napi nézetben jelennek meg, ahol minden nap egy adatpont van. Ha az időtartomány 30 napnál hosszabb, és 90 napnál kevesebb (vagy egyenlő) időtartamra esik, a diagramok heti nézetben jelennek meg. Nagyobb időtartományok esetén a diagramok havi nézetben jelennek meg. Az adatok heti vagy havi összesítése segít a lekérdezések jobb teljesítményében és az adatok könnyebb olvashatóságában a diagramokon.
- A Szabályzatbe tartási rácsok a fent leírtakhoz hasonló összesítési logikát is követnek. Van azonban néhány kisebb különbség. Az első különbség az, hogy a heti biztonsági mentési szabályzatokkal nem érhetők el napi nézetek (csak heti és havi nézetek érhetők el). Emellett a heti biztonsági mentési szabályzatot tartalmazó elemek rácsában a "hónap" 4 heti időszaknak (28 nap) minősül, nem pedig 30 napnak a részleges hetek megfontolásból való kiszűrése érdekében.

## <a name="query-load-times"></a>Lekérdezés betöltési idők

A Backup jelentés widgetjeit Kusto-lekérdezések működtetik, amelyek a felhasználó Log Analytics-munkaterületén futnak. Ezek a lekérdezések általában nagy mennyiségű adat feldolgozását foglalják magukban, és több illesztés használatával teszik lehetővé a gazdagabb elemzéseket. Ennek eredményeképpen előfordulhat, hogy a widgetek nem töltődik be azonnal, amikor a felhasználó egy nagy méretű biztonsági mentési tulajdonról készít jelentést. Ez a táblázat megbecsüli a különböző widgetek betöltési idejét a Biztonsági másolati elemek száma és a jelentés megtekintéséhez szükséges időtartomány alapján.

| **# Adatforrások**                         | **Időhorizont** | **Hozzávetőleges betöltési idők**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 hónap          | Csempék: 5–10 másodperc <br> Rácsok: 5–10 mp <br> Diagramok: 5–10 másodperc <br> Jelentésszintű szűrők: 5–10 másodperc|
| ~5 K                       | 3 hónap          | Csempék: 5–10 másodperc <br> Rácsok: 5–10 másodperc <br> Diagramok: 5–10 másodperc <br> Jelentésszintű szűrők: 5–10 másodperc|
| ~10 K                       | 3 hónap          | Csempék: 15–20 másodperc <br> Rácsok: 15–20 másodperc <br> Diagramok: 1–2 perc <br> Jelentésszintű szűrők: 25–30 másodperc|
| ~15 K                       | 1 hónap          | Csempék: 15–20 másodperc <br> Rácsok: 15–20 másodperc <br> Diagramok: 50–60 másodperc <br> Jelentésszintű szűrők: 20–25 másodperc|
| ~15 K                       | 3 hónap          | Csempék: 20–30 másodperc <br> Rácsok: 20–30 másodperc <br> Diagramok: 2–3 perc <br> Jelentésszintű szűrők: 50–60 mp |

## <a name="what-happened-to-the-power-bi-reports"></a> Mi történt a Power BI-jelentésekkel? 

- A Power BI egy jelentéskészítési sablonalkalmazás, amely egy Azure Storage-fiókból származik, elavult útvonalon található. Javasoljuk, hogy a jelentések megtekintéséhez kezdje el küldeni a tároló diagnosztikai adatait a Log Analyticsnek.

- Emellett a diagnosztikai adatok tárfiókba vagy LA-munkaterületre küldésének [V1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) sémája szintén elavult. Ez azt jelenti, hogy ha a V1 séma alapján írt egyéni lekérdezéseket vagy automatizálásokat, javasoljuk, hogy frissítse ezeket a lekérdezéseket a jelenleg támogatott V2 séma használatára.

## <a name="next-steps"></a>Következő lépések

[További információ a monitorozásról és a jelentéskészítésről a Azure Backup](./backup-azure-monitor-alert-faq.yml)