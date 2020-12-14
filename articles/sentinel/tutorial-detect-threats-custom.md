---
title: Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez az Azure Sentinel használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni elemzési szabályokat a biztonsági fenyegetések észleléséhez az Azure Sentinel használatával. Használja ki az események csoportosításának és a riasztások csoportosításának előnyeit, és Ismerje meg az automatikus letiltást.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 390d2c8488fd2b35c775eabe43677b9349b547a1
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401644"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Oktatóanyag: egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure sentinelhez, létrehozhat olyan egyéni szabályokat, amelyek meghatározott feltételeket kereshetnek a környezetében, és incidenseket hozhatnak létre, amikor a feltételek teljesülnek, így kivizsgálhatja azokat. Ez az oktatóanyag segít egyéni szabályok létrehozásában az Azure Sentinel használatával észlelt fenyegetések észleléséhez.

Ez az oktatóanyag segítséget nyújt az Azure Sentinel-fenyegetések észlelésében.
> [!div class="checklist"]
> * Elemzési szabályok létrehozása
> * Fenyegetési válaszok automatizálása

## <a name="create-custom-analytics-rules"></a>Egyéni elemzési szabályok létrehozása

Egyéni elemzési szabályokat hozhat létre, amelyek segítségével megkeresheti a környezetben gyanús fenyegetések és rendellenességek típusait. A szabály ellenőrzi, hogy azonnal értesítést kap-e, hogy a fenyegetések osztályozását, kivizsgálását és szervizelését el tudja végezni.

1. Az Azure Sentinel alatti Azure Portal válassza az **elemzés** lehetőséget.

1. A felső menüsorban válassza a **+ Létrehozás** lehetőséget, és válassza az **ütemezett lekérdezési szabályt**. Ekkor megnyílik az **elemzési szabály varázsló**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Ütemezett lekérdezés létrehozása":::

1. Az **általános** lapon adjon meg egy egyedi **nevet** és egy **leírást**. A **taktikák** mezőben választhat a támadási kategóriák közül, amelyek alapján osztályozni tudja a szabályt. Szükség szerint állítsa be a riasztás **súlyosságát** . A szabály létrehozásakor az **állapota** alapértelmezés szerint **engedélyezve** van, ami azt jelenti, hogy a létrehozás befejezését követően azonnal elindul. Ha nem szeretné, hogy azonnal fusson, válassza a **Letiltva** lehetőséget, és a szabály hozzá lesz adva az **aktív szabályok** laphoz, és szükség esetén is engedélyezheti.

    ![Egyéni elemzési szabály létrehozásának megkezdése](media/tutorial-detect-threats-custom/general-tab.png)

1. A **szabály logikájának beállítása** lapon közvetlenül is írhat egy lekérdezést a **szabály lekérdezése** mezőbe, vagy létrehozhatja a lekérdezést log Analytics, majd onnan másolhatja és beillesztheti.
 
   ![Lekérdezés létrehozása az Azure Sentinelben](media/tutorial-detect-threats-custom/settings-tab.png)

   - Tekintse meg az **eredmények előnézetének** területét a jobb oldalon, ahol az Azure Sentinel megjeleníti a lekérdezés által generált eredmények (log Events) számát, és a lekérdezés írásakor és konfigurálásakor menet közben változik. A diagram a megadott időszakon belüli eredmények számát jeleníti meg, amelyet a **lekérdezés ütemezése** szakaszban megadott beállítások határoznak meg.
    - Ha úgy látja, hogy a lekérdezés túl sok vagy túl gyakori riasztást vált ki, beállíthatja az alapkonfigurációt a **riasztási küszöbérték** szakaszban.

      Íme egy példa a lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az Azure-tevékenységben.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - A lekérdezés hosszának 1 és 10 000 karakter közöttinek kell lennie, és nem tartalmazhat "Search \* " vagy "Union \* " karaktert.
        >
        > - Az ADX függvények használata az Azure Adatkezelő-lekérdezések létrehozásához a Log Analytics lekérdezési ablakban **nem támogatott**.

    1. A **leképezési entitások** szakasz segítségével a lekérdezési eredményekből származó paramétereket kapcsolhat az Azure Sentinel által elismert entitásokhoz. Ezek az entitások a további elemzés alapjául szolgálnak, beleértve az incidensek **beállításai** lapon lévő riasztások csoportosítását.
  
    1. A **lekérdezés ütemezése** szakaszban adja meg a következő paramétereket:

       1. Az **összes lekérdezés futtatása** beállítással beállíthatja, hogy a lekérdezés milyen gyakran fusson – akár 5 percenként, akár ritkábban, akár naponta egyszer.

       1. A **legutóbbi keresési adatok** beállítása a lekérdezés által jelzett adatok időtartamának meghatározásához – például lekérdezheti az elmúlt 10 perc adatait, vagy az elmúlt 6 óra adatait.

          > [!NOTE]
          > **Lekérdezési időközök és lookback időszak**
          > - Ez a két beállítás egymástól független, egy pontra. A lekérdezéseket rövid idő alatt futtathatja, amely hosszabb időt is igénybe vehet az intervallumnál (egymást átfedő lekérdezésekkel), de nem futtathat lekérdezést olyan intervallumban, amely meghaladja a lefedettségi időt, ellenkező esetben a teljes lekérdezési lefedettség hiányában marad.
          >
          > **Betöltési késleltetés**
          > - Annak érdekében, hogy az események a forráshoz és az Azure Sentinelbe való betöltéséhez esetlegesen előforduló **késést** is figyelembe lehessen venni, és az adatok ismétlődése nélkül az Azure Sentinel ütemezett elemzési szabályokat futtat az ütemezett időponttól számított **öt perces késleltetéssel** .

    1. Az alapkonfiguráció meghatározásához használja a **riasztási küszöbérték** szakaszt. Például állítsa be a **riasztást, ha a lekérdezés eredményeinek száma** **nagyobb, mint** , és adja meg a 1000 számot, ha azt szeretné, hogy a szabály csak akkor hozzon létre riasztást, ha a lekérdezés több, mint 1000 eredményt ad vissza, amikor az fut. Ez egy kötelező mező, ezért ha nem szeretné beállítani az alapkonfigurációt – azaz ha azt szeretné, hogy a riasztás minden eseményt regisztráljon – a 0 értéket adja meg a szám mezőben.
    
    1. Az **események** **csoportosítása** lehetőségnél válassza ki a két módszer egyikét a **riasztások** csoportosításának kezelésére: 

       - Az **összes esemény egyetlen riasztásba csoportosítható** (az alapértelmezett beállítás). A szabály minden futtatáskor egyetlen riasztást hoz létre, ha a lekérdezés több eredményt ad vissza, mint a fent megadott **riasztási küszöbérték** . A riasztás az eredményekben visszaadott összes esemény összegzését tartalmazza. 

       - **Riasztás elindítása az egyes eseményekhez**. A szabály egyedi riasztást állít elő a lekérdezés által visszaadott egyes eseményekhez. Ez akkor hasznos, ha azt szeretné, hogy az események egyenként jelenjenek meg, vagy ha azokat bizonyos paraméterek szerint szeretné csoportosítani – felhasználó, állomásnév vagy valami más. Ezeket a paramétereket megadhatja a lekérdezésben.
    
       A szabály által generált riasztások száma jelenleg 20-ra van korlátozva. Ha egy adott szabályban az **esemény-csoportosítás** úgy van beállítva, hogy **riasztást indítson az egyes eseményekhez**, és a szabály lekérdezése több mint 20 eseményt ad vissza, akkor az első 19 esemény mindegyike egyedi riasztást hoz létre, a huszadik riasztás pedig összegzi a visszaadott események teljes készletét. Ez azt jelenti, hogy a huszadik riasztás a **csoport összes eseményének egyetlen riasztási** lehetőségbe való létrehozásakor lett létrehozva.

       > [!NOTE]
       > Mi a különbség az **események** és a **riasztások** között?
       >
       > - Az **esemény** egy egyszeri előfordulás leírása. Például egy naplófájl egyetlen bejegyzése eseménynek számít. Ebben a kontextusban egy esemény egyetlen, a lekérdezés által egy elemzési szabályban visszaadott eredményre hivatkozik.
       >
       > - A **riasztások** olyan események gyűjteményei, amelyek együttesen jelentősek a biztonsági szempontból. A riasztások egyetlen eseményt tartalmazhatnak, ha az esemény jelentős biztonsági következményekkel járt – például az Office-munkaidőn kívüli külföldi országtól érkező rendszergazdai bejelentkezés.
       >
       > - Mellesleg mi az **incidens**? Az Azure Sentinel belső logikája **incidenseket** vagy  riasztási csoportokat hoz létre. Az incidensek várólistája az elemzők munkahelyi osztályozásának, kivizsgálásának és szervizelésének központi eleme.
       > 
       > Az Azure Sentinel a különböző adatforrásokból származó nyers eseményeket és mások által már feldolgozott riasztásokat is tartalmaz. Fontos megjegyezni, hogy az Ön által felmerülő, bármikor felhasználható.

       > [!IMPORTANT]
       > Az események csoportosítása jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. A **letiltási** szakaszban bekapcsolhatja a **futó lekérdezés leállítása a riasztás létrehozása után** beállítást,  ha a riasztást követően felfüggeszti a szabály működését egy olyan időszakra, amely meghaladja a lekérdezési időközt. Ha bekapcsolja ezt a beállítást, be kell állítania a **lekérdezés leállítása leállítását** azon időtartamra, ameddig a lekérdezésnek futnia kell, akár 24 óráig.

1. Az **incidens beállításai** lapon megadhatja, hogy az Azure Sentinel hogyan kapcsolja be a riasztásokat a gyakorlatban előforduló incidensekre. Ha ez a lap egyedül marad, az Azure Sentinel egyetlen, külön incidenst hoz létre minden riasztásból. Dönthet úgy, hogy nem hozott létre incidenseket, vagy egyetlen incidensbe csoportosítja a több riasztást. ehhez módosítsa az ezen a lapon található beállításokat.

   > [!IMPORTANT]
   > Az incidens beállításai lap jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Az **incidens beállításai** szakaszban az **ezen elemzési szabály által aktivált riasztásokból származó incidensek létrehozása** alapértelmezés szerint **engedélyezve** értékre van állítva, ami azt jelenti, hogy az Azure Sentinel egyetlen, külön incidenst hoz létre minden egyes, a szabály által aktivált riasztás alapján.
       - Ha nem szeretné, hogy ez a szabály bármilyen incidens létrehozását eredményezje (például ha ez a szabály csak a további elemzéshez szükséges adatok gyűjtésére szolgál), állítsa ezt a lehetőséget **Letiltva** értékre.

    1. Ha azt szeretné, hogy a **riasztások csoportosítása** szakaszban egyetlen incidenst hozzon létre egy akár 150 hasonló vagy ismétlődő riasztásból álló csoportból (lásd: Megjegyzés), állítsa be a **csoporttal kapcsolatos riasztásokat, az elemzési szabály által aktivált incidensekre** **, és** állítsa be a következő paramétereket.

    - A **csoport korlátozása a kiválasztott időkereten belül létrehozott riasztásokra**: határozza meg azt az időkeretet, amelyen belül a hasonló vagy ismétlődő riasztások együtt lesznek csoportosítva. Az adott időkereten belül az összes vonatkozó riasztás együttesen létrehoz egy incidenst vagy incidensek készletét (az alábbi csoportosítási beállításoktól függően). Az ebben az időkereten kívüli riasztások külön incidenst vagy incidenseket hoznak létre.

    - Az **elemzési szabály által aktivált riasztások csoportosítása egyetlen incidensre a következő módon**: válassza ki, hogy melyik alapján csoportosítsa a rendszer a riasztásokat:

        - **Riasztások csoportosítása egyetlen incidensbe, ha az összes entitás egyezik**: a riasztások együtt vannak csoportosítva, ha az egyes leképezett entitások azonos értékekkel rendelkeznek (a szabály logikájának beállítása lapon definiálva). Ez az ajánlott beállítás.

        - A **szabály által aktivált összes riasztás egyetlen incidensbe csoportosítva**: a szabály által létrehozott összes riasztás együtt van csoportosítva, még akkor is, ha azok nem azonos értékeket használnak.

        - **Riasztások csoportosítása egyetlen incidensbe, ha a kiválasztott entitások egyeznek: a** riasztások együtt vannak csoportosítva, ha azonos értékekkel rendelkeznek a leképezett entitások némelyikéhez (a legördülő listából választhat). Érdemes lehet ezt a beállítást használni, ha például különálló incidenseket szeretne létrehozni a forrás vagy a cél IP-címei alapján.

    - A **lezárt egyeztetési incidensek újbóli megnyitása**: Ha az incidenst megoldották és lezárták, később pedig egy másik, az incidenshez tartozó riasztást generálnak, akkor a beállítást **engedélyezze** , ha azt szeretné, hogy a lezárt incidenst újra meg lehessen nyitni **, ha azt szeretné, hogy** a riasztás új incidenst hozzon létre.
    
        > [!NOTE]
        > Legfeljebb 150 riasztás lehet egyetlen incidensbe csoportosítva. Ha több mint 150 riasztást állít elő egy olyan szabály, amely egyetlen incidensbe csoportosítja őket, az eredetivel megegyező incidens-adatokkal új incidens jön létre, és a felesleges riasztások az új incidensbe lesznek csoportosítva.

1. Az **automatikus válaszok** lapon válassza ki azokat a forgatókönyveket, amelyeket automatikusan szeretne futtatni, ha az egyéni szabály létrehoz egy riasztást. A forgatókönyvek létrehozásával és automatizálásával kapcsolatos további információkért lásd: [válaszadás a fenyegetésekre](tutorial-respond-threats-playbook.md).

1. Válassza a **felülvizsgálat és létrehozás** lehetőséget az új riasztási szabály összes beállításának áttekintéséhez, majd válassza a létrehozás lehetőséget a **riasztási szabály inicializálásához**.
  
1. A riasztás létrehozása után a rendszer egy egyéni szabályt ad hozzá a táblához az **aktív szabályok** területen. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

1. A létrehozott riasztási szabályok eredményeinek megtekintéséhez nyissa meg az **incidensek** lapot, ahol osztályozást készíthet, [kivizsgálhatja az incidenseket](tutorial-investigate-cases.md), és elháríthatja a fenyegetéseket.


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

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).