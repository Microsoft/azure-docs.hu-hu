---
title: Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez az Azure Sentinel használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni elemzési szabályokat a biztonsági fenyegetések észleléséhez az Azure Sentinel használatával. Kihasználhatja az események csoportosításának, a riasztások csoportosításának és a riasztások dúsításának előnyeit, és megismerheti az automatikus letiltást.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453935"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Oktatóanyag: egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez

Most, hogy [az adatforrásokat az Azure Sentinel szolgáltatáshoz csatlakoztatta](quickstart-onboard.md) , létrehozhat egyéni elemzési szabályokat, amelyek segítenek felderíteni a környezetben található fenyegetéseket és rendellenes viselkedéseket. Ezek a szabályok adott eseményekre vagy események csoportjaira keresik a környezetében, riasztást küldenek bizonyos események küszöbértékének vagy feltételeinek elérésekor, előidézik a SOC osztályozását és kivizsgálását, és reagálnak a fenyegetésekre automatikus követési és szervizelési folyamatokkal. 

Ez az oktatóanyag segít egyéni szabályok létrehozásában az Azure Sentinel használatával észlelt fenyegetések észleléséhez.

Az oktatóanyag elvégzése után a következőket teheti:
> [!div class="checklist"]
> * Elemzési szabályok létrehozása
> * Az események és a riasztások feldolgozásának meghatározása
> * A riasztások és incidensek generálási módjának meghatározása
> * Válassza ki a szabályok automatizált veszélyforrásokra vonatkozó válaszait

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Egyéni elemzési szabály létrehozása ütemezett lekérdezéssel

1. Az Azure Sentinel navigációs menüjében válassza az **elemzés** lehetőséget.

1. A felső eszköztáron válassza a **+ Létrehozás** lehetőséget, és válassza az **ütemezett lekérdezési szabályt**. Ekkor megnyílik az **elemzési szabály varázsló**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Ütemezett lekérdezés létrehozása" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Elemzési szabály varázsló – általános lap

- Adjon meg egy egyedi **nevet** és egy **leírást**. 

- A **taktikák** mezőben választhat a támadási kategóriák közül, amelyek alapján osztályozni tudja a szabályt. Ezek a [Mitre ATT&CK](https://attack.mitre.org/) Framework taktikáján alapulnak.

- Szükség szerint állítsa be a riasztás **súlyosságát** . 

- A szabály létrehozásakor az **állapota** alapértelmezés szerint **engedélyezve** van, ami azt jelenti, hogy a létrehozás befejezését követően azonnal elindul. Ha nem szeretné, hogy azonnal fusson, válassza a **Letiltva** lehetőséget, és a szabály hozzá lesz adva az **aktív szabályok** laphoz, és szükség esetén is engedélyezheti.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Egyéni elemzési szabály létrehozásának megkezdése":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>A szabály lekérdezési logikájának definiálása és a beállítások konfigurálása

A **szabály logikájának beállítása** lapon közvetlenül is írhat egy lekérdezést a **szabály lekérdezése** mezőbe, vagy létrehozhatja a lekérdezést log Analytics, majd másolhatja és beillesztheti itt.

- A lekérdezések a Kusto lekérdezési nyelven (KQL) vannak megírva. További információ a KQL- [fogalmakról](/azure/data-explorer/kusto/concepts/) és- [lekérdezésekről](/azure/data-explorer/kusto/query/), valamint a hasznos [gyors útmutató](/azure/data-explorer/kql-quick-reference).

- Az ebben a képernyőfelvételben bemutatott példa lekérdezi a *SecurityEvent* táblázatot a [sikertelen Windows-bejelentkezési események](/windows/security/threat-protection/auditing/event-4625)típusának megjelenítéséhez.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="A lekérdezési szabály logikájának és beállításainak konfigurálása" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Íme egy másik példa a lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az [Azure-tevékenységben](../azure-monitor/platform/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Szabályok lekérdezése – ajánlott eljárások
    > - A lekérdezés hosszának 1 és 10 000 karakter közöttinek kell lennie, és nem tartalmazhatja a következőt: "" `search *` vagy "" `union *` .
    >
    > - Az ADX függvények használata az Azure Adatkezelő-lekérdezések létrehozásához a Log Analytics lekérdezési ablakban **nem támogatott**.
    >
    > - **`bag_unpack`** Ha a függvényt a lekérdezésben használja, ha az oszlopokat mezőként "" értékkel látja el, `project field1` és az oszlop nem létezik, a lekérdezés sikertelen lesz. A művelet elvégzése érdekében az oszlopot az alábbiak szerint kell megtervezni:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Riasztások dúsítása

> [!IMPORTANT]
> A riasztások dúsítási funkciói jelenleg **előzetes** verzióban érhetők el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.
    
- Az **entitás-hozzárendelési** konfiguráció szakasz segítségével leképezheti a lekérdezési eredményekből származó paramétereket az Azure Sentinel által felismert entitásokra. Az entitások a (z) kimenet (riasztások és incidensek) szabályait a következő alapvető információkkal gazdagítják, amelyek az alábbi vizsgálati folyamatok és javító műveletek építőelemeként szolgálnak. Emellett azok a feltételek, amelyekkel a riasztásokat az **incidensek beállításai** lap incidensek csoportjába rendezheti.

    További információ az [Azure Sentinel-beli entitásokról](entities-in-azure-sentinel.md).

    Lásd: [adatmezők leképezése az Azure sentinelben lévő entitásokra](map-data-fields-to-entities.md) az entitás-hozzárendelési utasítások végrehajtásához, valamint a [visszamenőleges kompatibilitással](map-data-fields-to-entities.md#notes-on-the-new-version)kapcsolatos fontos információk.

- Az **Egyéni adatok** konfigurációs szakasza segítségével kinyerheti az események adatelemeit a lekérdezésből, és felhasználhatja őket a szabály által létrehozott riasztásokban, így a riasztások és incidensek azonnali esemény-tartalma látható.

    További információ a felszínre vonatkozó egyéni részletekről a riasztásokban, valamint a [teljes útmutató](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Lekérdezés ütemezése és riasztási küszöbértéke

- A **lekérdezés ütemezése** szakaszban adja meg a következő paramétereket:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Lekérdezési ütemterv és események csoportosításának beállítása" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Az **összes lekérdezés futtatása** beállítással beállíthatja, hogy a lekérdezés milyen gyakran fusson – akár 5 percenként, akár ritkán, akár 14 naponta egyszer.

    - A **legutóbbi keresési adatok** beállítása a lekérdezés által jelzett adatok időtartamának meghatározásához – például lekérdezheti az elmúlt 10 perc adatait, vagy az elmúlt 6 óra adatait. A maximális érték 14 nap.

        > [!NOTE]
        > **Lekérdezési időközök és lookback időszak**
        >
        >  Ez a két beállítás egymástól független, egy pontra. A lekérdezéseket rövid idő alatt futtathatja, amely hosszabb időt is igénybe vehet az intervallumnál (egymást átfedő lekérdezésekkel), de nem futtathat lekérdezést olyan intervallumban, amely meghaladja a lefedettségi időt, ellenkező esetben a teljes lekérdezési lefedettség hiányában marad.
        >
        > **Betöltési késleltetés**
        >
        > Annak érdekében, hogy az események a forráshoz és az Azure Sentinelbe való betöltéséhez esetlegesen előforduló **késést** is figyelembe lehessen venni, és az adatok ismétlődése nélkül az Azure Sentinel ütemezett elemzési szabályokat futtat az ütemezett időponttól számított **öt perces késleltetéssel** .
        >
        > Ha részletes technikai leírást szeretne arról, hogy miért van szükség erre a késésre, és hogyan oldja meg ezt a problémát, tekintse meg Ron Marsiano kiváló blogbejegyzését, "a betöltés[késleltetésének kezelése az Azure Sentinel ütemezett riasztási szabályaiban](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)" című témakört.

- A **riasztási küszöbérték** szakasz használatával határozhatja meg a szabály érzékenységi szintjét. Például állítsa be a **riasztást, ha a lekérdezés eredményeinek száma** **nagyobb, mint** , és adja meg a 1000 számot, ha azt szeretné, hogy a szabály csak akkor hozzon létre riasztást, ha a lekérdezés több, mint 1000 eredményt ad vissza, amikor az fut. Ez egy kötelező mező, tehát ha nem szeretne küszöbértéket beállítani – azaz ha azt szeretné, hogy a riasztás minden eseményt regisztráljon – a 0 értéket adja meg a szám mezőben.
    
### <a name="results-simulation"></a>Eredmények szimulálása

Az **eredmények szimulálása** területen a varázsló jobb oldalán válassza a **tesztelés az aktuális adattal** lehetőséget, majd az Azure Sentinel megjeleníti az eredmények (log Events) diagramját, amelyet a lekérdezés az utolsó 50 alkalommal generált volna, az aktuálisan meghatározott ütemtervnek megfelelően. Ha módosítja a lekérdezést, a diagram frissítéséhez válassza ismét az **aktuális adattal való tesztelés** lehetőséget. A diagram a megadott időszakon belüli eredmények számát jeleníti meg, amelyet a **lekérdezés ütemezése** szakaszban megadott beállítások határoznak meg.
  
Az eredmények szimulálása a fenti képernyőképen a lekérdezéshez hasonlóan fog kinézni. A bal oldalon az alapértelmezett nézet, a jobb oldalon pedig az látható, ha a diagramon egy adott időponthoz viszi a kurzort.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Eredmények szimulációs képernyőképek":::

Ha úgy látja, hogy a lekérdezés túl sok vagy túl gyakori riasztást vált ki, a **lekérdezés ütemezése** és a **riasztási küszöbérték** [szakaszban](#query-scheduling-and-alert-threshold) található beállításokkal kísérletezhet, és ismét kiválaszthatja a **tesztelés az aktuális adattal** lehetőséget.

### <a name="event-grouping-and-rule-suppression"></a>Az események csoportosítása és a szabály letiltása

> [!IMPORTANT]
> Az események csoportosítása jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.
    
- Az **események** **csoportosítása** lehetőségnél válassza ki a két módszer egyikét a **riasztások** csoportosításának kezelésére: 

    - Az **összes esemény egyetlen riasztásba csoportosítható** (az alapértelmezett beállítás). A szabály minden futtatáskor egyetlen riasztást hoz létre, ha a lekérdezés több eredményt ad vissza, mint a fent megadott **riasztási küszöbérték** . A riasztás az eredményekben visszaadott összes esemény összegzését tartalmazza. 

    - **Riasztás aktiválása minden eseményhez**. A szabály egyedi riasztást állít elő a lekérdezés által visszaadott egyes eseményekhez. Ez akkor hasznos, ha azt szeretné, hogy az események egyenként jelenjenek meg, vagy ha azokat bizonyos paraméterek szerint szeretné csoportosítani – felhasználó, állomásnév vagy valami más. Ezeket a paramétereket megadhatja a lekérdezésben.
    
        A szabályok jelenleg legfeljebb 20 riasztást tudnak létrehozni. Ha egy adott szabályban az **esemény-csoportosítás** úgy van beállítva, hogy **riasztást indítson az egyes eseményekhez**, és a szabály lekérdezése több mint 20 eseményt ad vissza, akkor az első 19 esemény mindegyike egyedi riasztást hoz létre, a 20 riasztás pedig összegzi a visszaadott események teljes készletét. Más szóval, a huszadik riasztás a **csoport összes eseményének egyetlen riasztási** lehetőségbe való létrehozásakor lett létrehozva.

    > [!NOTE]
    > Mi a különbség az **események** és a **riasztások** között?
    >
    > - Az **esemény** egy művelet egyetlen előfordulásának leírása. Például egy naplófájl egyetlen bejegyzése eseménynek számít. Ebben a kontextusban egy esemény egyetlen, a lekérdezés által egy elemzési szabályban visszaadott eredményre hivatkozik.
    >
    > - A **riasztások** olyan események gyűjteményei, amelyek együttesen jelentősek a biztonsági szempontból. A riasztások egyetlen eseményt tartalmazhatnak, ha az esemény jelentős biztonsági következményekkel járt – például az Office-munkaidőn kívüli külföldi országtól érkező rendszergazdai bejelentkezés.
    >
    > - Mellesleg mi az **incidens**? Az Azure Sentinel belső logikája **incidenseket** vagy  riasztási csoportokat hoz létre. Az incidensek várólistája a SOC-elemzők munkahelyi osztályozásának, kivizsgálásának és szervizelésének középpontja.
    > 
    > Az Azure Sentinel a különböző adatforrásokból származó nyers eseményeket és mások által már feldolgozott riasztásokat is tartalmaz. Fontos megjegyezni, hogy az Ön által felmerülő, bármikor felhasználható.

- A **letiltási** szakaszban bekapcsolhatja a **futó lekérdezés leállítása a riasztás létrehozása után** beállítást,  ha a riasztást követően felfüggeszti a szabály működését egy olyan időszakra, amely meghaladja a lekérdezési időközt. Ha bekapcsolja ezt a beállítást, be kell állítania a **lekérdezés leállítása leállítását** azon időtartamra, ameddig a lekérdezésnek futnia kell, akár 24 óráig.

## <a name="configure-the-incident-creation-settings"></a>Az incidens-létrehozási beállítások konfigurálása

Az **incidens beállításai** lapon megadhatja, hogy az Azure Sentinel hogyan kapcsolja be a riasztásokat a gyakorlatban előforduló incidensekre. Ha ez a lap egyedül marad, az Azure Sentinel egyetlen, külön incidenst hoz létre minden riasztásból. Dönthet úgy, hogy nem hozott létre incidenseket, vagy egyetlen incidensbe csoportosítja a több riasztást. ehhez módosítsa az ezen a lapon található beállításokat.

> [!IMPORTANT]
> Az incidens beállításai lap jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Az incidensek létrehozásának és a riasztások csoportosítási beállításainak megadása":::

### <a name="incident-settings"></a>Incidensbeállítások

Az **incidens beállításai** szakaszban az **ezen elemzési szabály által aktivált riasztásokból származó incidensek létrehozása** alapértelmezés szerint **engedélyezve** értékre van állítva, ami azt jelenti, hogy az Azure Sentinel egyetlen, külön incidenst hoz létre minden egyes, a szabály által aktivált riasztás alapján.
    
- Ha nem szeretné, hogy ez a szabály bármilyen incidens létrehozását eredményezje (például ha ez a szabály csak a további elemzéshez szükséges adatok gyűjtésére szolgál), állítsa ezt a lehetőséget **Letiltva** értékre.

- Ha azt szeretné, hogy a rendszer egyetlen incidenst hozzon létre a riasztások csoportjából, az egyes riasztások egyike helyett a következő szakaszban talál további információt.

### <a name="alert-grouping"></a>Riasztások csoportosítása

Ha azt szeretné, hogy a **riasztások csoportosítása** szakaszban egyetlen incidenst hozzon létre egy akár 150 hasonló vagy ismétlődő riasztásból álló csoportból (lásd: Megjegyzés), állítsa be a **csoporttal kapcsolatos riasztásokat, az elemzési szabály által aktivált incidensekre** **, és** állítsa be a következő paramétereket.

- A **csoport korlátozása a kiválasztott időkereten belül létrehozott riasztásokra**: határozza meg azt az időkeretet, amelyen belül a hasonló vagy ismétlődő riasztások együtt lesznek csoportosítva. Az adott időkereten belül az összes vonatkozó riasztás együttesen létrehoz egy incidenst vagy incidensek készletét (az alábbi csoportosítási beállításoktól függően). Az ebben az időkereten kívüli riasztások külön incidenst vagy incidenseket hoznak létre.

- Az **elemzési szabály által aktivált riasztások csoportosítása egyetlen incidensre a következő módon**: válassza ki, hogy melyik alapján csoportosítsa a rendszer a riasztásokat:

    - **Riasztások csoportosítása egyetlen incidensbe, ha az összes entitás egyezik**: a riasztások együtt vannak csoportosítva, ha az egyes leképezett entitások azonos értékekkel rendelkeznek (a szabály logikájának beállítása lapon definiálva). Ez az ajánlott beállítás.

    - A **szabály által aktivált összes riasztás egyetlen incidensbe csoportosítva**: a szabály által létrehozott összes riasztás együtt van csoportosítva, még akkor is, ha azok nem azonos értékeket használnak.

    - **Riasztások csoportosítása egyetlen incidensbe, ha a kiválasztott entitások egyeznek: a** riasztások együtt vannak csoportosítva, ha azonos értékekkel rendelkeznek a leképezett entitások némelyikéhez (a legördülő listából választhat). Érdemes lehet ezt a beállítást használni, ha például különálló incidenseket szeretne létrehozni a forrás vagy a cél IP-címei alapján.

- A **lezárt egyeztetési incidensek újbóli megnyitása**: Ha az incidenst megoldották és lezárták, később pedig egy másik, az incidenshez tartozó riasztást generálnak, akkor a beállítást **engedélyezze** , ha azt szeretné, hogy a lezárt incidenst újra meg lehessen nyitni **, ha azt szeretné, hogy** a riasztás új incidenst hozzon létre.
    
    > [!NOTE]
    > **Legfeljebb 150 riasztás** lehet egyetlen incidensbe csoportosítva. Ha több mint 150 riasztást állít elő egy olyan szabály, amely egyetlen incidensbe csoportosítja őket, az eredetivel megegyező incidens-adatokkal új incidens jön létre, és a felesleges riasztások az új incidensbe lesznek csoportosítva.

## <a name="set-automated-responses-and-create-the-rule"></a>Automatikus válaszok beállítása és a szabály létrehozása

1. Az **automatikus válaszok** lapon válassza ki azokat a forgatókönyveket, amelyeket automatikusan szeretne futtatni, ha az egyéni szabály létrehoz egy riasztást. A forgatókönyvek létrehozásával és automatizálásával kapcsolatos további információkért lásd: [válaszadás a fenyegetésekre](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Az automatikus válasz beállításainak megadása":::

1. Az új riasztási szabály összes beállításának áttekintéséhez válassza a **felülvizsgálat és létrehozás** lehetőséget. Amikor megjelenik az "érvényesítési ellenőrzés" üzenet, válassza a **Létrehozás** lehetőséget a riasztási szabály inicializálásához.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Az összes beállítás áttekintése és a szabály létrehozása":::

## <a name="view-the-rule-and-its-output"></a>A szabály és a kimenet megtekintése
  
- Az újonnan létrehozott egyéni szabályt ("ütemezett") a fő **elemzési** képernyő **aktív szabályok** lapján található táblában találja. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

- A létrehozott riasztási szabályok eredményeinek megtekintéséhez nyissa meg az **incidensek** lapot, ahol osztályozást készíthet, [kivizsgálhatja az incidenseket](tutorial-investigate-cases.md), és elháríthatja a fenyegetéseket.

> [!NOTE]
> Az Azure Sentinelben létrehozott riasztások [Microsoft Graph biztonságon](/graph/security-concept-overview)keresztül érhetők el. További információ: [Microsoft Graph Security riasztások dokumentációja](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="issue-no-events-appear-in-query-results"></a>Probléma: a lekérdezés eredményeiben nem jelennek meg események

Ha az **esemény-csoportosítás** úgy van beállítva, hogy **riasztást indítson az egyes eseményekhez**, majd bizonyos esetekben a lekérdezés eredményeinek későbbi megtekintésekor (például egy incidensből riasztások visszavonásakor), lehetséges, hogy egyetlen lekérdezési eredmény sem fog megjelenni. Ennek az az oka, hogy az eseménynek a riasztáshoz való kapcsolódása az adott esemény adatainak kivonatolásával, valamint a kivonatnak a lekérdezésbe való belefoglalásával valósul meg. Ha a lekérdezés eredményei megváltoztak a riasztás létrehozása óta, a kivonat többé nem lesz érvényes, és a rendszer nem jeleníti meg az eredményeket. 

Az események megtekintéséhez manuálisan távolítsa el a sort a kivonatból a szabály lekérdezésében, majd futtassa a lekérdezést.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Probléma: egy ütemezett szabályt nem sikerült végrehajtani, vagy a rendszer automatikusan letiltottként jeleníti meg a nevet

Ritkán fordul elő, hogy egy ütemezett lekérdezési szabály nem fut, de ez megtörténhet. Az Azure Sentinel a meghibásodások meghatározott típusa és a hozzájuk kapcsolódó körülmények alapján osztályozza az átmeneti vagy állandó hibákat.

#### <a name="transient-failure"></a>Átmeneti hiba

Átmeneti hiba fordul elő, mert az ideiglenes állapotú, és hamarosan visszatér a normális kerékvágásba, ekkor a szabály végrehajtása sikeres lesz. Néhány példa azokra a hibákra, amelyeket az Azure Sentinel átmenetiként osztályoz:

- Egy szabály lekérdezése túl sokáig tart a futtatásához és az időtúllépéshez.
- Kapcsolódási problémák az adatforrások és a Log Analytics, illetve a Log Analytics és az Azure Sentinel között.
- Minden más új és ismeretlen hiba átmenetinek tekintendő.

Átmeneti hiba esetén az Azure Sentinel továbbra is megkísérli végrehajtani a szabályt az előre meghatározott és folyamatosan növekvő intervallumok után, akár egy pontot. Ezt követően a szabály csak a következő ütemezett időpontban fog futni. Egy szabály nem lesz automatikusan letiltva átmeneti hiba miatt.

#### <a name="permanent-failure---rule-auto-disabled"></a>Állandó hiba – a szabály automatikusan le van tiltva

Állandó hiba történt a szabály futtatását engedélyező feltételek változása miatt, amely emberi beavatkozás nélkül nem tér vissza a korábbi állapotukba. Az alábbiakban néhány példát láthat az állandóként besorolt hibákra:

- A célként megadott munkaterület (amelyen a szabály lekérdezése működik) törölve lett.
- A célként megadott tábla (amelyen a szabály lekérdezése működik) törölve lett.
- Az Azure Sentinel el lett távolítva a cél munkaterületről.
- A szabály lekérdezése által használt függvény már nem érvényes; módosult vagy el lett távolítva.
- A szabály lekérdezésének egyik adatforrásához tartozó engedélyek módosultak.
- A szabály lekérdezésének egyik adatforrása törölve lett vagy le lett választva.

**Ha előre meghatározott számú, egymást követő állandó hibát észlel, és ugyanazon a szabályon van,** akkor Az Azure Sentinel leállítja a szabály végrehajtásának kísérletét, és a következő lépéseket is végrehajtja:

- Letiltja a szabályt.
- Hozzáadja az **"automatikus LEtiltva"** kifejezést a szabály nevének elejéhez.
- Hozzáadja a hiba okát (és a letiltását) a szabály leírásához.

Egyszerűen meghatározhatja az automatikusan letiltott szabályok jelenlétét, ha a szabályok listáját név szerint rendezi. Az automatikusan letiltott szabályok a lista elején vagy közelében lesznek.

A SOC-kezelőknek rendszeresen ellenőriznie kell a szabályok listáját az automatikusan letiltott szabályok meglétének ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával.

- Ismerje meg, hogyan [vizsgálhatja meg az incidenseket az Azure sentinelben](tutorial-investigate-cases.md).
- Ismerje meg [Az Azure Sentinel entitásait](entities-in-azure-sentinel.md).
- Ismerje meg, hogyan [állíthatja be az automatizált veszélyforrások válaszait az Azure sentinelben](tutorial-respond-threats-playbook.md).
