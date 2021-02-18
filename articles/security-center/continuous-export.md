---
title: A folyamatos exportálás elküldheti Azure Security Center riasztásait és javaslatait Log Analytics munkaterületekre vagy az Azure-ra Event Hubs
description: Ismerje meg, hogyan konfigurálhatja a biztonsági riasztások és javaslatok folyamatos exportálását Log Analytics munkaterületekre vagy az Azure-ra Event Hubs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: 9b8dc635781c96dcbd7aa423c77f60ff0556bd71
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634063"
---
# <a name="continuously-export-security-center-data"></a>Security Center-adatfeldolgozás folyamatos exportálása

Azure Security Center részletes biztonsági riasztásokat és javaslatokat hoz létre. Ezeket megtekintheti a portálon vagy a programozott eszközökön keresztül is. Előfordulhat, hogy ezen információk némelyikét vagy mindegyikét exportálnia kell a környezetben található egyéb figyelési eszközökkel. 

A **folyamatos exportálással** teljes mértékben testreszabhatja, hogy *mi* történjen az exportálásban, és *hová* fog menni. Beállíthatja például, hogy:

- Az összes magas súlyosságú riasztást egy Azure Event hub-ba küldi a rendszer
- Az SQL Server-kiszolgálók biztonsági rések felmérésével kapcsolatos összes közepes vagy magasabb súlyossági megállapítást egy adott Log Analytics-munkaterületre kell elküldeni
- Konkrét javaslatok érkeznek egy Event hub-vagy Log Analytics-munkaterületre, amikor létrehozzák őket 
- Az előfizetés biztonságos pontszámát egy Log Analytics munkaterületre küldi a rendszer, ha a vezérlőelem pontszáma 0,01 vagy újabb értékkel változik 

Annak ellenére, hogy a szolgáltatás neve *folyamatos*, akkor is lehetőség van arra, hogy a biztonságos pontszám vagy a szabályozási megfelelőségi adatmennyiségről is exportáljon heti pillanatképeket.

Ez a cikk bemutatja, hogyan konfigurálhatja a folyamatos exportálást Log Analytics munkaterületekre vagy az Azure Event Hubsra.

> [!NOTE]
> Ha a Security Centert egy SIEM-rel szeretné integrálni, tekintse meg a következőt: a [stream-riasztások egy Siem-, szárnyaló-vagy informatikai szolgáltatási megoldáshoz](export-to-siem.md).

> [!TIP]
> A Security Center a CSV-re történő manuális exportálást is lehetőséget nyújt. További információ a [riasztások és javaslatok manuális egyszeri exportálásával](#manual-one-time-export-of-alerts-and-recommendations)kapcsolatban.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|<ul><li>**Biztonsági rendszergazda** vagy az erőforráscsoport **tulajdonosa**</li><li>Írási engedélyek a célként megadott erőforráshoz</li><li>Ha az alábbiakban ismertetett Azure Policy "DeployIfNotExist" szabályzatot használja, a szabályzatok hozzárendelésére vonatkozó engedélyekre is szüksége lesz</li></ul>|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov, egyéb gov<br>![Yes](./media/icons/yes-icon.png) Kínai gov (az Event hub-hoz)|
|||


## <a name="what-data-types-can-be-exported"></a>Milyen adattípusokat lehet exportálni?

A folyamatos exportálás a következő adattípusokat exportálhatja, valahányszor változnak:

- Biztonsági riasztások
- Biztonsági javaslatok 
- Biztonsági megállapítások, amelyek a sebezhetőségi felmérési képolvasók vagy adott rendszerfrissítések eredményeire vonatkozó "alárendelt" javaslatokként is megtekinthetők. Kiválaszthatja, hogy a "fölérendelt" javaslatokkal együtt tartalmazza a "rendszerfrissítéseket a számítógépekre".
- Biztonságos pontszám (előfizetés vagy vezérlés alapján)
- Szabályozási megfelelőségi adatszolgáltatások

> [!NOTE]
> A biztonságos pontszám és a szabályozás megfelelőségi adatexportálása előzetes funkció, és nem érhető el a kormányzati felhőkben. 

## <a name="set-up-a-continuous-export"></a>Folyamatos exportálás beállítása 

A folyamatos exportálást a Azure Portal Security Center lapjain, a Security Center REST API vagy a megadott Azure Policy sablonok használatával konfigurálhatja. A részletekért válassza az alábbi megfelelő fület.

### <a name="use-the-azure-portal"></a>[**Az Azure Portal használata**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Folyamatos exportálás konfigurálása Azure Portal Security Center lapjain

Az alábbi lépések szükségesek, függetlenül attól, hogy folyamatos exportálást állít be Log Analytics munkaterületre vagy az Azure Event Hubsra.

1. A Security Center oldalsávján válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki azt az előfizetést, amelyhez be szeretné állítani az adatexportálást.
1. Az előfizetés beállítások oldalának oldalsávján válassza a **folyamatos exportálás** lehetőséget.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Exportálási beállítások a Azure Security Center":::

    Itt láthatja az exportálási beállításokat. Minden elérhető exportálási célponthoz van egy lap. 

1. Válassza ki az exportálni kívánt adattípust, és válasszon az egyes típusok szűrőinek közül (például csak a nagy súlyosságú riasztások exportálása).
1. Válassza ki a megfelelő exportálási gyakoriságot:
    - **Streaming** – az értékelések valós időben lesznek elküldve, amikor egy erőforrás állapota frissül (ha nem történik frissítés, a rendszer nem küldi el az adatküldést).
    - **Pillanatképek** – az összes megfelelőségi értékelés aktuális állapotának pillanatképét hetente küldi el a rendszer (ez egy előzetes verziójú funkció a biztonságos pontszámok és a szabályozási megfelelőségi adatok heti pillanatképének elkészítéséhez).

1. Ha a kijelölés magában foglalja a javaslatok egyikét is, a sebezhetőségi felmérés eredményei együttesen is felvehetők:
    - A sebezhetőségi felmérés eredményeit az SQL-adatbázisokban szervizelni kell
    - A biztonsági rések felmérésének eredményeit a gépeken lévő SQL-kiszolgálókon szervizelni kell (előzetes verzió)
    - A Azure Container Registry lemezképekben található biztonsági réseket szervizelni kell (Qualys-alapú)
    - A virtuális gépek biztonsági réseit szervizelni kell
    - A rendszerfrissítéseket telepíteni kell a gépeken

    Ahhoz, hogy a megállapítások szerepeljenek a javaslatokban, engedélyezze a **biztonsági megállapítások belefoglalása** lehetőséget.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Biztonsági megállapítások bekapcsolása a folyamatos exportálási konfigurációban" :::

1. Az "exportálási cél" területen válassza ki, hogy hová szeretné menteni az adatok mentését. Az adattárolók egy másik előfizetésben lévő célhelyre menthetők (például egy központi Event hub-példányon vagy egy központi Log Analytics munkaterületen).
1. Kattintson a **Mentés** gombra.

### <a name="use-the-rest-api"></a>[**A REST API használata**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Folyamatos exportálás konfigurálása a REST API használatával

A folyamatos exportálás konfigurálható és kezelhető a Azure Security Center [automations API](/rest/api/securitycenter/automations)-n keresztül. Ezzel az API-val szabályokat hozhat létre vagy frissíthet a következő lehetséges célhelyekre való exportáláshoz:

- Azure Event Hub
- Log Analytics-munkaterület
- Azure Logic Apps 

Az API olyan további funkciókat biztosít, amelyek nem érhetők el a Azure Portal, például:

* **Nagyobb mennyiség** – az API lehetővé teszi, hogy több exportálási konfigurációt hozzon létre egyetlen előfizetésen. Security Center portál felhasználói felületének **folyamatos exportálás** lapja csak egy exportálási konfigurációt támogat az előfizetések esetében.

* **További funkciók** – az API olyan további paramétereket kínál, amelyek nem jelennek meg a felhasználói felületen. Hozzáadhat például címkéket az Automation-erőforráshoz, valamint megadhatja az exportálást a riasztások és javaslatok szélesebb készlete alapján, mint amelyek az Security Center portál felhasználói felületének **folyamatos exportálás** lapján elérhetők.

* Koncentráltabb **hatókör** – az API részletesebb szintet biztosít az exportálási konfigurációk hatóköréhez. Az API-val való exportálás meghatározásakor ezt az erőforráscsoport szintjén teheti meg. Ha a **folyamatos exportálás** lapot használja Security Center portál felhasználói felületén, azt az előfizetés szintjén kell megadnia.

    > [!TIP]
    > Ha az API használatával több exportálási konfigurációt állított be, vagy ha csak API-paramétereket használt, akkor ezek a további funkciók nem jelennek meg a Security Center felhasználói felületen. Ehelyett megjelenik egy szalagcím, amely tájékoztatja, hogy más konfigurációk léteznek.

További információ az automations API-ról a [REST API dokumentációjában](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Nagy léptékű üzembe helyezés Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>A folyamatos exportálás konfigurálása a megadott szabályzatok használatával

A szervezet figyelési és incidens-reagálási folyamatainak automatizálása nagy mértékben növelheti a biztonsági incidensek kivizsgálásához és enyhítéséhez szükséges időt.

A folyamatos exportálási konfigurációk a szervezeten belüli üzembe helyezéséhez használja az alább ismertetett Azure Policy "DeployIfNotExist" szabályzatokat a folyamatos exportálási eljárások létrehozásához és konfigurálásához.

**A szabályzatok megvalósítása**

1. Az alábbi táblázatból válassza ki az alkalmazni kívánt szabályzatot:

    |Cél  |Szabályzat  |Házirend-azonosító  |
    |---------|---------|---------|
    |Folyamatos Exportálás az Event hub-ba|[Az Azure Security Center-riasztások és -javaslatok Event Hubsba való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Folyamatos exportálás Log Analytics munkaterületre|[Az Azure Security Center-riasztások és -javaslatok Log Analytics-munkaterületre való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Ezeket a Azure Policy keresésével is megtalálhatja:
    > 1. Nyissa meg Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Hozzáférés Azure Policy":::
    > 2. A Azure Policy menüben válassza a **definíciók** lehetőséget, és keressen rájuk név alapján. 

1. A megfelelő Azure Policy lapon válassza a **hozzárendelés** lehetőséget.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="A Azure Policy kiosztása":::

1. Nyissa meg az egyes lapokat, és adja meg a kívánt paramétereket:
    1. Az **alapvető beállítások** lapon állítsa be a házirend hatókörét. A központosított felügyelet használatához rendelje hozzá a szabályzatot a folyamatos exportálási konfigurációt használó előfizetéseket tartalmazó felügyeleti csoporthoz. 
    1. A **Parameters (paraméterek** ) lapon állítsa be az erőforráscsoport és az adattípus részleteit. 
        > [!TIP]
        > Minden paraméterhez tartozik egy elemleírás, amely ismerteti az Ön számára elérhető lehetőségeket.
        >
        > Azure Policy paraméterek lapja (1) hozzáférést biztosít a hasonló konfigurációs beállításokhoz, mint a Security Center folyamatos exportálási lapja (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="A folyamatos exportálás paramétereinek összehasonlítása Azure Policy" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Ha a hozzárendelést meglévő előfizetésekre szeretné alkalmazni, nyissa meg a **szervizelés** lapot, és válassza a Szervizelési feladat létrehozása lehetőséget.
1. Tekintse át az összefoglalás lapot, és válassza a **Létrehozás** lehetőséget.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Információ Log Analytics munkaterületre való exportálásról

Ha egy Log Analytics munkaterületen belül szeretné elemezni Azure Security Center adatait, vagy az Azure-riasztásokat Security Center riasztásokkal együtt szeretné használni, állítsa be a folyamatos exportálást a Log Analytics munkaterületre.

### <a name="log-analytics-tables-and-schemas"></a>Táblák és sémák Log Analytics

A biztonsági riasztások és javaslatok tárolása a *SecurityAlert* és a *SecurityRecommendations* táblákban történik. 

Az alábbi táblákat tartalmazó Log Analytics-megoldás neve attól függ, hogy engedélyezve van-e az Azure Defender: Security ("Security and Audit") vagy a SecurityCenterFree. 

> [!TIP]
> A cél munkaterületen lévő információk megtekintéséhez engedélyeznie kell a következő megoldások valamelyikét **Security and Audit** vagy **SecurityCenterFree**.

![A * SecurityAlert * tábla Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel a [log Analytics Table-sémákat](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Az exportált riasztások és javaslatok megtekintése Azure Monitor

Azt is megteheti, hogy megtekinti az exportált biztonsági riasztásokat és/vagy javaslatokat [Azure monitorban](../azure-monitor/alerts/alerts-overview.md). 

Azure Monitor egységes riasztási élményt nyújt számos Azure-riasztáshoz, beleértve a diagnosztikai naplót, a metrikus riasztásokat és az egyéni riasztásokat Log Analytics munkaterület-lekérdezések alapján.

Ha Azure Monitor Security Center riasztásait és javaslatait szeretné megtekinteni, Log Analytics lekérdezéseken alapuló riasztási szabályt állítson be (naplózási riasztás):

1. A Azure Monitor **riasztások** lapján válassza az **új riasztási szabály** lehetőséget.

    ![Azure Monitor riasztások lapja](./media/continuous-export/azure-monitor-alerts.png)

1. A szabály létrehozása lapon konfigurálja az új szabályt (ugyanúgy, mint a [naplózási riasztási szabály konfigurálása a Azure monitorban](../azure-monitor/alerts/alerts-unified-log.md)):

    * Az **erőforrás** mezőben válassza ki azt a log Analytics munkaterületet, amelyhez biztonsági riasztásokat és javaslatokat exportált.

    * A **feltétel** beállításnál válassza az **egyéni naplók keresése** lehetőséget. A megjelenő oldalon konfigurálja a lekérdezést, az lookback időszakot és a gyakorisági időszakot. A keresési lekérdezésben beírhatja a *SecurityAlert* vagy a *SecurityRecommendation* kifejezést, hogy lekérdezze azokat az adattípusokat, amelyek Security Center folyamatos exportálást végeznek, amikor engedélyezi a folyamatos exportálást log Analytics szolgáltatásba. 
    
    * Megadhatja az aktiválni kívánt [műveleti csoportot](../azure-monitor/alerts/action-groups.md) is. A műveleti csoportok elindíthatják az e-mailek küldését, a ITSM jegyeket, a webhookokat és egyebeket.
    ![Azure Monitor riasztási szabály](./media/continuous-export/azure-monitor-alert-rule.png)

Ekkor megjelenik az új Azure Security Center riasztások vagy javaslatok (a beállított folyamatos exportálási szabályoktól és a Azure Monitor riasztási szabályban megadott feltételtől függően) Azure Monitor riasztásokban, a műveleti csoport automatikus indításával (ha meg van adva).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Riasztások és javaslatok manuális egyszeri exportálása

A riasztások vagy javaslatok CSV-jelentésének letöltéséhez nyissa meg a **biztonsági riasztások** vagy **javaslatok** lapot, és kattintson a **CSV-jelentés letöltése** gombra.

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Riasztási adatfájlok letöltése CSV-fájlként" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Ezek a jelentések riasztásokat és javaslatokat tartalmaznak az aktuálisan kijelölt előfizetésekben lévő erőforrásokra vonatkozóan.


## <a name="faq---continuous-export"></a>Gyakori kérdések – folyamatos exportálás

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Milyen költségekkel jár az adatexportálás?

A folyamatos exportálás engedélyezésének nincs költsége. A Log Analytics munkaterületen lévő adatok betöltésének és megőrzésének költségei az ott konfigurált konfigurációtól függően merülhetnek fel. 

További információ a [log Analytics munkaterület díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).

További információ az [Azure Event hub díjszabásáról](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>Az Exportálás az összes erőforrás aktuális állapotával kapcsolatos információkat tartalmaz?

Nem. A folyamatos Exportálás az **események** folyamatos továbbítására készült:

- Az Exportálás engedélyezése előtt fogadott **riasztások** nem lesznek exportálva.
- A **javaslatok** akkor lesznek elküldve, amikor egy erőforrás megfelelőségi állapota megváltozik. Például, ha egy erőforrás kifogástalan állapotról sérültre vált. Ezért a riasztásokhoz hasonlóan a nem módosult erőforrásokra vonatkozó ajánlásokat is, mivel az exportálás nem lesz exportálva.
- A **biztonságos pontszám (előzetes verzió)** biztonsági vezérléssel vagy előfizetéssel akkor kerül elküldésre, ha a biztonsági vezérlő pontszáma 0,01 vagy újabb értékre változik. 
- A rendszer elküldi a megfelelőségi **állapotot (előzetes verzió)** az erőforrás megfelelőségi változásainak állapota esetén.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>A javaslatok elküldése miért különböző időközönként történik?

A különböző javaslatok különböző megfelelőségi értékelési időközökkel rendelkeznek, ami néhány perctől akár néhány napig változhat. Ennek következtében a javaslatok eltérhetnek az exportálás során megjelenő időtartamtól.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Támogatja a folyamatos Exportálás az üzletmenet folytonosságát vagy a vész-helyreállítási (BCDR) forgatókönyveket?

Ha a környezetet olyan BCDR-forgatókönyvek esetében készíti elő, amelyekben a cél erőforrás leállás vagy más katasztrófa esetén fordul elő, akkor a szervezet felelőssége az adatvesztés megelőzése azáltal, hogy az Azure Event Hubs, Log Analytics munkaterület és a Logic app irányelvek alapján hozza létre a biztonsági mentéseket.

További információ az [Azure Event Hubs-geo-vész-helyreállítással](../event-hubs/event-hubs-geo-dr.md)kapcsolatban.


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>A folyamatos exportálás elérhető Azure Security Center ingyenes?

Igen! Vegye figyelembe, hogy sok Security Center riasztás csak akkor érhető el, ha engedélyezve van az Azure Defender. Az exportált adataiban megjelenő riasztások megtekintésének jó módja, ha a Azure Portal Security Center oldalain látható riasztásokat látja.



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja a javaslatok és riasztások folyamatos exportálását. Azt is megtanulta, hogyan töltheti le a riasztási adatait CSV-fájlként. 

Kapcsolódó anyagok esetében tekintse meg a következő dokumentációt: 

- További információ a [munkafolyamat-automatizálási sablonokról](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Az Azure Event Hubs dokumentációja](../event-hubs/index.yml)
- [Az Azure Sentinel dokumentációja](../sentinel/index.yml)
- [Az Azure Monitor dokumentációja](../azure-monitor/index.yml)
- [Adattípusok sémáinak exportálása](https://aka.ms/ASCAutomationSchemas)