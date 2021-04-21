---
title: Egyéni elemzési szabályok létrehozása a fenyegetések észlelésére Azure Sentinel| Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni elemzési szabályokat a biztonsági fenyegetések észlelésére a Azure Sentinel. Használja ki az események csoportosításának, a riasztások csoportosításának és a riasztások gazdagításának előnyeit, és értse meg az AUTOMATIKUS LETILTÁS funkcióját.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814855"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Oktatóanyag: Egyéni elemzési szabályok létrehozása a fenyegetések észlelésére

Most, hogy [](quickstart-onboard.md) csatlakoztatta az adatforrásokat a Azure Sentinel, egyéni elemzési szabályokat hozhat létre, amelyek segítségével felderítheti a környezetben jelen található fenyegetéseket és rendellenes viselkedéseket. Ezek a szabályok adott eseményeket vagy eseménykészleteket keresnek a környezetben, riasztást küldnek bizonyos eseményküszöbök vagy feltételek elérésekor, incidenseket hoznak létre az SOC-hez az osztályozás és a vizsgálat érdekében, valamint automatizált nyomkövetési és szervizelési folyamatokkal reagálnak a fenyegetésekre. 

Ez az oktatóanyag segít egyéni szabályokat létrehozni a fenyegetések észlelésére a Azure Sentinel.

Az oktatóanyag elvégzése után a következőket fogja tudni megtenni:
> [!div class="checklist"]
> * Elemzési szabályok létrehozása
> * Az események és riasztások feldolgozásának meghatározása
> * A riasztások és incidensek generálásának meghatározása
> * Automatizált fenyegetésekre adott válaszok kiválasztása a szabályokhoz

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Egyéni elemzési szabály létrehozása ütemezett lekérdezéssel

1. A navigációs Azure Sentinel válassza az **Elemzés lehetőséget.**

1. A felső műveletsávon válassza a **+Létrehozás,** majd az **Ütemezett lekérdezési szabály lehetőséget.** Ez megnyitja az **Analytics-szabály varázslót.**

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Ütemezett lekérdezés létrehozása" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Elemzési szabály varázsló – Általános lap

- Adjon meg egy egyedi **nevet és** egy **Leírást.** 

- A **Taktikák mezőben** több támadási kategória közül választhat, amelyek alapján osztályozza a szabályt. Ezek a [MITRE ATT CK-keretrendszerének&alapulnak.](https://attack.mitre.org/)

- Állítsa be a riasztás **súlyosságát** a megfelelő módon. 

- A szabály létrehozásakor az  Állapot  alapértelmezés szerint Engedélyezve lesz, ami azt jelenti, hogy a létrehozás befejezése után azonnal lefut. Ha nem szeretné, hogy azonnal fusson, válassza a Letiltva lehetőséget, és a szabály hozzá lesz adva az **Aktív** szabályok laphoz, és onnan engedélyezheti, amikor szüksége van rá. 

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Egyéni elemzési szabály létrehozásának első létrehozása":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>A szabálylekérdezés logikájának meghatározása és a beállítások konfigurálása

A **Szabálylogika** beállítása lapon közvetlenül a Szabálylekérdezés mezőben írhat lekérdezést, vagy létrehozhatja a lekérdezést a Log Analyticsben, majd másolhatja és beillesztheti ide. 

- A lekérdezések a Kusto lekérdezési nyelven (KQL) vannak megírva. További információ a KQL [alapfogalmairól](/azure/data-explorer/kusto/concepts/) és [lekérdezéseiről,](/azure/data-explorer/kusto/query/)és tekintse meg ezt a hasznos [rövid útmutatót.](/azure/data-explorer/kql-quick-reference)

- Az ezen a képernyőképen látható példa lekérdezi a *SecurityEvent* táblát a sikertelen Windows bejelentkezési események [típusának megjelenítéséhez.](/windows/security/threat-protection/auditing/event-4625)

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Lekérdezési szabály logikájának és beállításainak konfigurálása" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Példa egy másik lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az [Azure-tevékenységben.](../azure-monitor/essentials/activity-log.md)

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Szabálylekérdezés – ajánlott eljárások
    > - A lekérdezés hossza 1 és 10 000 karakter között lehet, és nem tartalmazhat `search *` " " vagy " `union *` "-t. A felhasználó által [definiált függvényekkel leküzdheti](/azure/data-explorer/kusto/query/functions/user-defined-functions) a lekérdezések hosszkorlátját.
    >
    > - Az ADX-függvények használata Azure Data Explorer lekérdezések létrehozására a Log Analytics lekérdezési ablakában **nem támogatott.**
    >
    > - Ha a függvényt lekérdezésben használja, és az oszlopokat mezőkként kivetíti a " " használatával, és az oszlop nem létezik, a **`bag_unpack`** `project field1` lekérdezés sikertelen lesz. Az ilyen események elleni védelemhez a következőképpen kell kivetítnie az oszlopot:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Riasztások gazdagítása

> [!IMPORTANT]
> A riasztások gazdagítási funkciói jelenleg előzetes verzióban **érhetőek el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkozó további jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.
    
- Az **Entitásleképezés** konfigurációja szakaszban leképezheti a lekérdezési eredmények paramétereit Azure Sentinel felismert entitásokhoz. Az entitások a szabályok kimenetét (riasztásokat és incidenseket) olyan alapvető információkkal bővítik, amelyek a következő vizsgálati folyamatok és szervizműveletek építőelemeiként szolgálnak. Ezek azok a feltételek is, amelyek alapján a riasztásokat incidensekbe csoportosíthatja az Incidens beállításai **lapon.**

    További információ az [entitásokkal kapcsolatban a Azure Sentinel.](entities-in-azure-sentinel.md)

    A [visszamenőleges kompatibilitással](map-data-fields-to-entities.md) kapcsolatos fontos információkért lásd: Adatmezők [](map-data-fields-to-entities.md#notes-on-the-new-version)leképezése a Azure Sentinel entitások entitásokhoz.

- Az Egyéni **részletek konfigurációja** szakasz használatával eseményadat-elemeket von ki a lekérdezésből, és ezeket a szabály által létrehozott riasztásokbe felszínre hozhatja, így azonnal láthatóvá teheti az események tartalmát a riasztások és incidensek számára.

    Tudjon meg többet az egyéni részletek riasztásokbe való behozásról, és tekintse meg a [teljes utasításokat.](surface-custom-details-in-alerts.md)

### <a name="query-scheduling-and-alert-threshold"></a>Lekérdezésütemezés és riasztási küszöbérték

- A Lekérdezés **ütemezése szakaszban** állítsa be a következő paramétereket:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Lekérdezés ütemezésének és eseménycsoportozásának beállítása" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Állítsa be a **Lekérdezés** futtatása adatokat, hogy szabályozni tudja, milyen gyakran futtassa a lekérdezést – akár 5 percenként, akár ritkán, 14 naponta egyszer.

    - Állítsa **be az** utolsótól származó keresési adatokat a lekérdezés által lefedett adatok időtartamának meghatározásához – például lekérdezheti az elmúlt 10 percet vagy az elmúlt 6 órányi adatot. A maximális érték 14 nap.

        > [!NOTE]
        > **Lekérdezési időközök és visszacsatolási időszak**
        >
        >  Ez a két beállítás egymástól független, egy pontig. Az időköznél hosszabb ideig (azaz átfedésben lévő lekérdezések esetén) futtathat egy lekérdezést rövid időintervallumban, de nem futtathat olyan lekérdezést, amely meghaladja a lefedettségi időszakot, különben a teljes lekérdezési lefedettségben hézagok lesznek.
        >
        > **A begesedés késése**
        >
        > A Azure Sentinel  ütemezett elemzési szabályait az ütemezett időponttól ötperces késéssel futtatja annak érdekében, hogy figyelembe veszi az esemény forrásnál történő létrehozása és az **Azure Sentinel-ba** való beúsítása közötti késést, és biztosítsa a teljes lefedettséget adatdedig.
        >
        > A késés szükségszerűségére és megoldására vonatkozó részletes technikai magyarázatért tekintse meg Ron Marsiano kiváló blogbejegyzését a témáról: "[Handling in ingestion](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)delay in Azure Sentinel scheduled alert rules ".

- A **Riasztási küszöbérték szakaszban** határozhatja meg a szabály érzékenységi szintjét. Ha például  azt szeretné, hogy a  szabály csak akkor hozzon létre riasztást, ha a lekérdezés futtatásakor több mint 1000 eredményt ad vissza, állítsa a Riasztás létrehozása, ha a lekérdezési eredmények száma nagyobb, mint, és adja meg az 1000-es számot. Ez egy kötelező mező, ezért ha nem szeretne küszöbértéket beállítani – azaz ha azt szeretné, hogy a riasztás minden eseményt regisztráljon – adja meg a 0-t a számmezőben.
    
### <a name="results-simulation"></a>Eredmények szimulációja

Az  Eredmények szimulációja területen, a varázsló jobb  oldalán válassza a Tesztelés az aktuális adatokkal lehetőséget, és az Azure Sentinel megmutatja a lekérdezés által az elmúlt 50 alkalommal létrehozott eredményeket (naplóeseményeket) az aktuálisan meghatározott ütemezésnek megfelelően. Ha módosítja a lekérdezést, válassza ismét a **Tesztelés az aktuális adatokkal** lehetőséget a gráf frissítéséhez. A diagram az eredmények számát mutatja a megadott időszakban, amelyet a Lekérdezés ütemezése szakaszban megadott **beállítások határoznak** meg.
  
Az eredményszimuláció a következő módon néz ki a fenti képernyőképen látható lekérdezéshez. A bal oldali az alapértelmezett nézet, a jobb oldal pedig az, amit akkor lát, ha a kurzort egy adott időpontra mutat a gráfon.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Eredmények szimulációja – képernyőképek":::

Ha azt látja, hogy a lekérdezés túl sok vagy túl gyakori riasztást vált  [](#query-scheduling-and-alert-threshold) ki, kísérletezhet a Lekérdezés ütemezése és a Riasztási küszöbérték szakaszban található beállításokkal, és újra kiválaszthatja a Tesztelés az aktuális **adatokkal** lehetőséget. 

### <a name="event-grouping-and-rule-suppression"></a>Eseménycsoportozás és szabályelfojtás

> [!IMPORTANT]
> Az eseménycsoportozás jelenleg ELŐZETES **VERZIÓban érhető el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy egyéb módon, általánosan elérhető Azure-funkciókra vonatkozó további jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.
    
- Az **Eseménycsoportozás alatt** válasszon az események riasztások  szerinti csoportosításának két **módja közül:** 

    - **Az összes eseményt egyetlen riasztásba csoportosítja** (ez az alapértelmezett beállítás). A szabály minden futtatáskor létrehoz egy riasztást, ha a lekérdezés több eredményt ad vissza, mint a fenti **riasztási küszöbérték.** A riasztás tartalmazza az eredményekben visszaadott összes esemény összegzését. 

    - **Riasztás aktiválása minden eseményhez**. A szabály egyedi riasztást hoz létre a lekérdezés által visszaadott összes eseményhez. Ez akkor hasznos, ha azt szeretné, hogy az események külön-külön jelennek meg, vagy ha bizonyos paraméterek szerint szeretné csoportosítni őket – felhasználó, állomásnév vagy valami más alapján. Ezeket a paramétereket a lekérdezésben definiálhatja.
    
        A szabályok jelenleg legfeljebb 20 riasztást tudnak létrehozni. Ha egy adott  szabályban az Eseménycsoportozás beállítása Riasztás aktiválása minden eseményhez **,** és a szabály lekérdezése több mint 20 eseményt ad vissza, az első 19 esemény egyedi riasztást hoz létre, és a 20. riasztás összegzi a visszaadott események teljes készletét. Más szóval a 20. riasztás lett volna létrehozva az Összes esemény csoportosítása egyetlen riasztásba **lehetőség** alatt.

    > [!NOTE]
    > Mi a különbség az események **és a** **riasztások között?**
    >
    > - Az **esemény** egy művelet egyszeri előfordulásának leírása. Egy naplófájl egyetlen bejegyzése például eseménynek is számíthat. Ebben a kontextusban az esemény egy elemzési szabályban egy lekérdezés által visszaadott egyetlen eredményre hivatkozik.
    >
    > - A **riasztások** olyan események gyűjteményei, amelyek biztonsági szempontból együtt jelentősek. A riasztások egyetlen eseményt is tartalmazhatnak, ha az esemény jelentős biztonsági következményekkel járt – például egy munkaidőn kívüli külső országból történő rendszergazdai bejelentkezés.
    >
    > - By the way, what are **incidents**? Azure Sentinel belső logikája riasztások **vagy**  riasztáscsoportok alapján hoz létre incidenseket. Az incidensek várólistája az SOC-elemzők munkához – osztályozás, vizsgálat és szervizelés – központi eleme.
    > 
    > Azure Sentinel egyes adatforrások nyers eseményeit, másoktól pedig már feldolgozott riasztásokat. Fontos megjegyezni, hogy melyikkel kell mindig foglalkozni.

- A **Mellőzés** szakaszban bekapcsolhatja a Stop running  lekérdezés **after alert is generated** (Futtatás leállítása riasztás generálása után) beállítást, ha a riasztás után a szabály működését a lekérdezési időközt meghaladó időtartamra szeretné felfüggeszteni. Ha ezt bekapcsolja, a **Lekérdezés** futásának leállítása beállításnál legfeljebb 24 órát kell beállítania arra az időre, amíg a lekérdezésnek le kell állnia.

## <a name="configure-the-incident-creation-settings"></a>Az incidens létrehozási beállításainak konfigurálása

Az **Incidensbeállítások lapon** kiválaszthatja, hogy a riasztásokat hogyan és hogyan Azure Sentinel a riasztásokat a művelethez használható incidensekbe. Ha ezt a lapot nem adja meg, a Azure Sentinel létrehoz egy különálló incidenst minden egyes riasztástól. Dönthet úgy, hogy nem hoz létre incidenseket, vagy több riasztást egyetlen incidensbe csoportosít, ha módosítja a beállításokat ezen a lapon.

> [!IMPORTANT]
> Az incidensbeállítások lap jelenleg előzetes verzióban **érhető el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkozó további jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Az incidens létrehozási és riasztáscsoportozási beállításainak meghatározása":::

### <a name="incident-settings"></a>Incidensbeállítások

Az  Incidensbeállítások szakaszban  az Ezen elemzési szabály által aktivált riasztások alapján létrehozott incidensek alapértelmezett beállítása **Engedélyezve,** ami azt jelenti, hogy az Azure Sentinel egyetlen, különálló incidenst hoz létre a szabály által kiváltott összes riasztástól.
    
- Ha nem szeretné, hogy ez a szabály incidensek létrehozását eredményezi (például ha ez a szabály csak a későbbi elemzéshez szükséges információk gyűjtésére vonatkozik), állítsa Letiltva **beállításra.**

- Ha azt szeretné, hogy a riasztások egy csoportja egyetlen incidenst hoz létre, ne pedig minden egyes riasztáshoz, tekintse meg a következő szakaszt.

### <a name="alert-grouping"></a>Riasztások csoportosítása

Ha a **Riasztások** csoportosítása szakaszban egyetlen incidenst szeretne generálni egy legfeljebb 150 hasonló vagy ismétlődő riasztásból álló csoportból (lásd a megjegyzést), állítsa a Csoporthoz kapcsolódó riasztásokat, amelyek az elemzési szabály által kiváltottak, az incidenseket Állítsa **Engedélyezve** **beállításra,** és állítsa be a következő paramétereket.

- **A csoport korlátozása a kiválasztott** időkereten belül létrehozott riasztásra: Határozza meg azt az időkeretet, amelyen belül a hasonló vagy ismétlődő riasztások csoportosítva lesznek. A megadott időszakon belül az összes megfelelő riasztás együttesen létrehoz egy incidenst vagy incidenskészletet (az alábbi csoportosítási beállításoktól függően). Az ezen időkereten kívüli riasztások külön incidenst vagy incidenskészletet hoznak létre.

- **Az elemzési szabály által aktivált** csoportos riasztások egyetlen incidensbe a következő szerint: Válassza ki azt az alapot, amelyen a riasztások csoportosítva lesznek:

    - **Riasztások** csoportosítása egyetlen incidensbe, ha az összes entitás megegyezik: A riasztások akkor vannak csoportosítva, ha azonos értékeken osztoznak az egyes leképezett entitások esetében (a fenti Szabály beállítása logika lapon vannak meghatározva). Ez az ajánlott beállítás.

    - **A szabály által kiváltott** összes riasztás csoportosítása egyetlen incidensbe: A rendszer a szabály által generált összes riasztást csoportosítja, még akkor is, ha nem azonos értékekkel osztoznak.

    - **Riasztások** csoportosítása egyetlen incidensbe, ha a kiválasztott entitások egyeznek: A riasztások akkor vannak csoportosítva, ha a leképezett entitások egy része azonos értékekkel osztozik (a legördülő listából választhat). Akkor érdemes ezt a beállítást használni, ha például külön incidenseket szeretne létrehozni a forrás vagy a cél IP-cím alapján.

- Lezárt egyezésű incidensek újra **megnyitása:** Ha egy incidens megoldódott és lezárult, és később egy  másik riasztás jön létre, amely az incidenshez tartozik, állítsa Ezt a beállítást Engedélyezve, ha szeretné, hogy a lezárt incidens újra megnyílt, és hagyja Letiltva értéken, ha azt szeretné, hogy a riasztás új incidenst hozzon létre. 
    
    > [!NOTE]
    > **Legfeljebb 150 riasztás** csoportosítható egyetlen incidensbe. Ha egy szabály több mint 150 riasztást hoz létre, amely egyetlen incidensbe csoportosítja őket, új incidens jön létre az eredetivel azonos incidens adataival, a felesleges riasztások pedig az új incidensbe lesznek csoportosítva.

## <a name="set-automated-responses-and-create-the-rule"></a>Automatikus válaszok beállítása és a szabály létrehozása

1. Az Automatikus **válaszok lapon** válassza ki azokat a forgatókönyveket, amelyek automatikusan futnak, ha az egyéni szabály riasztást hoz létre. A forgatókönyvekkel kapcsolatos további információkért lásd: [Válasz a fenyegetésekre.](tutorial-respond-threats-playbook.md)

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Az automatizált válaszbeállítások megadása":::

1. Válassza **az Áttekintés és létrehozás** lehetőséget az új riasztási szabály összes beállításának áttekintéséhez. Amikor megjelenik az "Ellenőrzés átveve" üzenet, válassza a **Létrehozás** lehetőséget a riasztási szabály inicializáláshoz.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Az összes beállítás áttekintése és a szabály létrehozása":::

## <a name="view-the-rule-and-its-output"></a>A szabály és kimenetének megtekintése
  
- Az újonnan létrehozott (ütemezett típusú) egyéni szabályt a tábla **Aktív** szabályok lapján találja a fő **Elemzés képernyőn.** Ezen a listán engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

- A létrehozott riasztási szabályok eredményeinek megtekintéséhez  kattintson az Incidensek oldalra, ahol osztályozást [készíthet,](tutorial-investigate-cases.md)kivizsgálhatja az incidenseket, és orvosolhatja a fenyegetéseket.

> [!NOTE]
> A biztonsági Azure Sentinel létrehozott riasztások a [Microsoft Graph keresztül érhetők el.](/graph/security-concept-overview) További információkért lásd a Microsoft Graph [riasztások dokumentációját.](/graph/api/resources/security-api-overview)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="issue-no-events-appear-in-query-results"></a>Probléma: A lekérdezési eredményekben nem jelennek meg események

Ha  az eseménycsoportozás úgy van beállítva, hogy minden eseményhez riasztást aktivál, akkor bizonyos esetekben a lekérdezési eredmények későbbi megtekintésekor (például amikor egy incidensből származó riasztásra vált vissza), lehetséges, hogy nem jelennek meg lekérdezési eredmények. Ennek az az oka, hogy az esemény és a riasztás kapcsolata az adott esemény információinak kivonatolása és a kivonat lekérdezésbe való belefoglalása révén valósíthető meg. Ha a lekérdezési eredmények a riasztás létrehozása óta módosultak, a kivonat már nem lesz érvényes, és nem jelenik meg eredmény. 

Az eseményeket manuálisan távolítsa el a kivonatot a szabály lekérdezésében, majd futtassa a lekérdezést.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Probléma: Egy ütemezett szabály végrehajtása sikertelen volt, vagy automatikus letiltás után jelenik meg a névhez

Ritkán fordul elő, hogy egy ütemezett lekérdezési szabály nem fut, de ez megtörténhet. Azure Sentinel előre, átmenetiként vagy állandóként osztályba adja a hibákat a hiba konkrét típusa és az ahhoz vezető körülmények alapján.

#### <a name="transient-failure"></a>Átmeneti hiba

Átmeneti hiba akkor fordul elő, ha egy körülmények ideiglenesek, és hamarosan visszatérnek a normál állapothoz, és a szabály végrehajtása sikeres lesz. Néhány példa az átmenetiként Azure Sentinel osztályba Azure Sentinel hibákra:

- A szabálylekérdezés futtatása túl sokáig tart, és időkor túl sok időt vesz igénybe.
- Csatlakozási problémák az adatforrások és a Log Analytics, illetve a Log Analytics és a Azure Sentinel.
- Minden más új és ismeretlen hiba átmenetinek minősül.

Átmeneti hiba esetén a Azure Sentinel megpróbálja újra végrehajtani a szabályt az előre meghatározott és egyre növekvő időközök után, egy pontig. Ezt követően a szabály csak a következő ütemezett időpontban fog ismét futni. Egy szabály átmeneti hiba miatt soha nem lesz automatikusan letiltva.

#### <a name="permanent-failure---rule-auto-disabled"></a>Állandó hiba – szabály automatikus letiltása

Állandó hiba történik a szabály futását engedélyező feltételek módosítása miatt, amely emberi beavatkozás nélkül nem tér vissza a korábbi állapotukhoz. Az alábbiakban néhány példát talál az állandóként besorolt hibákra:

- A cél munkaterület (amelyen a szabálylekérdezés üzemelt) törölve lett.
- A céltábla (amelyen a szabálylekérdezés üzemelt) törölve lett.
- Azure Sentinel el lett távolítva a cél munkaterületről.
- A szabálylekérdezés által használt függvények már nem érvényesek; vagy módosítva lett, vagy el lett távolítva.
- A szabálylekérdezés egyik adatforrásának engedélyei módosultak.
- A szabálylekérdezés egyik adatforrása törölve lett vagy le lett választva.

Előre meghatározott számú, azonos típusú és ugyanazon szabályon következő állandó hiba **esetén:** Azure Sentinel nem próbálja végrehajtani a szabályt, és a következő lépéseket is végrehajtja:

- Letiltja a szabályt.
- Hozzáadja **az "AUTO DISABLED" (AUTOMATIKUS LETILTVA)** szót a szabály nevének elejéhez.
- Hozzáadja a hiba okát (és letiltásának) a szabály leírását.

A szabálylista név alapján való rendezésével egyszerűen meghatározhatja az automatikusan letiltott szabályok jelenlétét. Az automatikusan letiltott szabályok a lista tetején vagy annak közelében lesznek.

Az SOC-kezelőknek rendszeresen ellenőrizniük kell a szabálylistát, hogy vannak-e automatikusan letiltott szabályok.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan észlelheti a fenyegetéseket a Azure Sentinel.

- Megtudhatja, hogyan [vizsgálható meg az incidensek a Azure Sentinel.](tutorial-investigate-cases.md)
- További információ [a Azure Sentinel.](entities-in-azure-sentinel.md)
- Ismerje meg, [hogyan állíthat be automatizált fenyegetésekre adott válaszokat a Azure Sentinel.](tutorial-respond-threats-playbook.md)
