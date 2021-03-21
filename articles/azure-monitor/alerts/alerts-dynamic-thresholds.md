---
title: Riasztások létrehozása dinamikus küszöbértékekkel Azure Monitor
description: Riasztások létrehozása gépi tanuláson alapuló dinamikus küszöbértékekkel
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718063"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Metrikus riasztások dinamikus küszöbértékekkel Azure Monitor

A dinamikus küszöbértékek észlelésével rendelkező metrikus riasztások speciális gépi tanulást (ML) biztosítanak a mérőszámok korábbi viselkedésének megismeréséhez, a lehetséges szolgáltatási problémákat jelző mintázatok és rendellenességek azonosításához. Támogatja mind az egyszerű felhasználói felületet, mind a nagy léptékű műveletek támogatását azáltal, hogy lehetővé teszi a felhasználók számára, hogy a riasztási szabályokat a Azure Resource Manager API-n keresztül, teljesen automatizált módon konfigurálja.

A riasztási szabály létrehozása után csak akkor fog bejárni, ha a figyelt metrika nem a várt módon működik, a testre szabott küszöbértékek alapján.

Szívesen vesszük a visszajelzését, és megtartjuk a jövőt <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Miért és Mikor ajánlott használni a dinamikus feltétel típusát?

1. **Skálázható riasztás** – a dinamikus küszöbérték riasztási szabályai egyszerre több száz metrikai sorozatra szabott küszöbértékeket hozhatnak létre, ugyanakkor a riasztási szabályok egyetlen mérőszámon való definiálását is lehetővé teszik. Kevesebb riasztást biztosítanak a létrehozásához és kezeléséhez. A létrehozáshoz Azure Portal vagy a Azure Resource Manager API-t használhatja. A skálázható megközelítés különösen akkor hasznos, ha metrikus dimenziókat használ, vagy ha több erőforrásra, például az összes előfizetési erőforrásra alkalmazza.  [További információ a metrikus riasztások dinamikus küszöbértékekkel való konfigurálásáról a sablonok használatával](./alerts-metric-create-templates.md).

1. **Intelligens metrikai minták felismerése** – a ml-technológia használatával automatikusan észlelhetők a metrikus mintázatok, és a metrikai változásokhoz is alkalmazkodnak az idő múlásával, ami gyakran magában foglalhatja a szezonális (óránkénti/napi/heti) időszakot. A mérőszámok viselkedésének időbeli változása és a mintázattól való eltérések alapján történő riasztása csökkenti az egyes mérőszámok "jobb" küszöbértékét. A dinamikus küszöbértékekben használt ML-algoritmus úgy van kialakítva, hogy megakadályozza a várt mintázattal nem rendelkező zajos (kis pontosságú) vagy nagy (alacsony visszahívás) küszöbértékek használatát.

1. **Intuitív konfiguráció** – a dinamikus küszöbértékek lehetővé teszik a metrikus riasztások magas szintű fogalmakkal történő beállítását, ami csökkenti a metrikával kapcsolatos kiterjedt tartományi ismereteket.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Riasztási szabályok konfigurálása dinamikus küszöbértékekkel

A dinamikus küszöbértékekkel rendelkező riasztások a Azure Monitorban metrikai riasztások segítségével konfigurálhatók. [További információ a metrikai riasztások konfigurálásáról](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hogyan számítják ki a küszöbértékeket?

A dinamikus küszöbértékek folyamatosan tanulják a metrikai sorozat adatait, és algoritmusok és metódusok használatával próbálják meg modellezni azokat. Az adatok olyan mintázatait észleli, mint például a szezonális (óránkénti/napi/heti), és képes kezelni a zajos metrikákat (például a gépi PROCESSZORt vagy a memóriát), valamint az alacsony szórású metrikákat (például a rendelkezésre állást és a hibák arányát).

A küszöbértékek úgy vannak kiválasztva, hogy a küszöbértéktől való eltérés a metrika viselkedésében anomália.

> [!NOTE]
> A dinamikus küszöbértékek óránkénti, napi vagy heti minták esetében is észlelhetők. Előfordulhat, hogy a rendszer nem észleli más mintákat, például a kétórás vagy a részben heti szezonális évszakot. A heti szezonális megjelenítés észleléséhez legalább három héttel korábbi adatmennyiségre van szükség. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Mit jelent a dinamikus küszöbértékek "érzékenység" beállítása?

A riasztási küszöbértékek érzékenysége olyan magas szintű fogalom, amely a riasztások kiváltásához szükséges metrikai viselkedéstől való eltérés mértékét vezérli.
Ehhez a beállításhoz nem szükséges tartomány-ismeret a mérőszámhoz, például a statikus küszöbértékhez. Az elérhető lehetőségek:

- High (magas) – a küszöbértékek feszesek lesznek, és a metrikák sorozatának mintázata is megközelíthető. A rendszer riasztási szabályt indít el a legkisebb eltérés miatt, ami több riasztást eredményez.
- Közepes – kevésbé szűk és kiegyensúlyozottabb küszöbértékek, kevesebb riasztás, mint a magas érzékenység (alapértelmezett).
- Alacsony – a küszöbértékek a metrikus adatsorozat mintájának nagyobb távolságával lesznek megoldva. Egy riasztási szabály csak nagy eltéréseket indít el, így kevesebb riasztást fog eredményezni.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Mik az "operátor" beállítási lehetőségei a dinamikus küszöbértékekben?

A dinamikus küszöbértékek riasztási szabálya olyan személyre szabott küszöbértékeket hozhat létre, amelyek a felső és az alsó határ metrikájának viselkedése alapján azonos riasztási szabályt használnak.
A következő három feltétel egyikén aktiválhatja a riasztást:

- Nagyobb, mint a felső küszöbérték, vagy alacsonyabb az alsó küszöbértéknél (alapértelmezett)
- Nagyobb, mint a felső küszöbérték
- Alacsonyabb, mint az alsó küszöbérték.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Mit jelentenek a dinamikus küszöbértékek speciális beállításai?

**Sikertelen időszakok** – a dinamikus küszöbértékek lehetővé teszik a "a riasztás kiváltásához szükséges számok megsértésének megadását", a rendszer egy adott időszakon belül egy adott időintervallumban megkövetelt eltérések minimális számát (az alapértelmezett időablak négy eltérés 20 perc alatt). A felhasználó beállíthatja a sikertelen időszakokat, és kiválaszthatja, hogy mi legyen a riasztás, ha módosítja a sikertelen időszakokat és az időintervallumot. Ez a képesség csökkenti az átmeneti tüskék által generált riasztási zajt. Például:

Ha a probléma 20 percen belül folyamatos, 4 egymást követő alkalommal, 5 perces csoportosításban, akkor a következő beállításokat kell használnia:

![Sikertelen időszakok beállításai a folyamatos hibákhoz 20 percen keresztül, 4 egymást követő alkalommal 5 percen belül csoportosítva](media/alerts-dynamic-thresholds/0008.png)

Ha riasztást szeretne indítani, ha a dinamikus küszöbértékek megsértése miatt 20 perccel az elmúlt 30 percen belül megsérti az 5 perces időszakot, használja a következő beállításokat:

![Sikertelen időszakok beállításai a probléma 20 percen belül az elmúlt 30 percben az 5 perces időszakos csoportosítással](media/alerts-dynamic-thresholds/0009.png)

**Adatok figyelmen kívül hagyása** – a felhasználók tetszés szerint meghatározhatnak egy kezdő dátumot, amelyből a rendszernek meg kell kezdenie a küszöbértékek kiszámítását. Egy tipikus használati eset akkor fordulhat elő, ha egy erőforrás tesztelési módban fut, és most már az éles számítási feladatok kiszolgálására lett előléptetve, ezért a tesztelési fázisban lévő bármely metrika viselkedését figyelmen kívül kell hagynia.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Honnan tudhatja meg, miért váltott ki a dinamikus küszöbértékek riasztása?

Az aktivált riasztási példányokat a riasztások nézetben tekintheti meg, ha az e-mailben vagy szöveges üzenetben található hivatkozásra kattint, vagy a böngészőben a riasztások nézetét szeretné látni a Azure Portal. [További információ a riasztások nézetről](./alerts-overview.md#alerts-experience).

A riasztás nézet a következőket jeleníti meg:

- A dinamikus küszöbértékek riasztásának pillanatában az összes metrikai adat.
- Annak az időszaknak a diagramja, amelyben a riasztás aktiválva lett, beleértve az adott időpontban használt dinamikus küszöbértékeket is.
- A dinamikus küszöbértékekkel kapcsolatos riasztások és a riasztások megtekintésének lehetősége, amely javíthatja a jövőbeli észleléseket.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>A metrikában a lassú viselkedés változása riasztást vált ki?

Valószínűleg nem. A dinamikus küszöbértékek kiválóan alkalmasak a jelentős eltérések észlelésére, nem pedig lassan változó problémákra.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Mennyi adattal kell megtekinteni, majd kiszámítani a küszöbértékeket?

A riasztási szabályok első létrehozásakor a diagramon megjelenő küszöbértékek kiszámítása az óra vagy napi szezonális mintázatok (10 nap) alapján számított, elegendő múltbeli érték alapján történik. A riasztási szabály létrehozása után a dinamikus küszöbértékek a rendelkezésre álló összes szükséges korábbi információt használják, és az új értékek alapján folyamatosan tanulnak és alkalmazkodnak, hogy a küszöbértékek pontosabbak legyenek. Ez azt jelenti, hogy a számítás után a diagram hetente mintákat is fog megjeleníteni.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Mennyi információra van szükség a riasztás indításához?

Ha új erőforrással vagy hiányzó metrikai adatokkal rendelkezik, a dinamikus küszöbértékek nem indítanak riasztásokat három nap előtt, és legalább 30 metrikus adat érhető el, így biztosítva a pontos küszöbértékeket.
A megfelelő metrikus adatokkal rendelkező meglévő erőforrások esetében a dinamikus küszöbértékek azonnal aktiválják a riasztásokat.

## <a name="dynamic-thresholds-best-practices"></a>Dinamikus küszöbértékek – ajánlott eljárások

A dinamikus küszöbértékek a Azure Monitor legtöbb platformján és egyéni metrikáján alkalmazhatók, és az általános alkalmazás-és infrastruktúra-metrikára is be lettek hangolva.
A következő elemek ajánlott eljárások arra vonatkozóan, hogyan konfigurálhat riasztásokat ezen mérőszámok némelyikén dinamikus küszöbértékek használatával.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dinamikus küszöbértékek a virtuális gép CPU-százalékos metrikái esetében

1. [Azure Portal](https://portal.azure.com)kattintson a **figyelés** elemre. A figyelő nézet egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

2. Kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.

    > [!TIP]
    > A legtöbb erőforrás-panel a **figyelés** alatt is tartalmaz **riasztásokat** az erőforrás menüjében, és onnan is létrehozhat riasztásokat.

3. Kattintson a **cél kiválasztása** lehetőségre, a betöltött környezet ablaktáblán válassza ki azt a cél erőforrást, amelyre a riasztást szeretné használni. Az **előfizetés** és a **"Virtual Machines" erőforrástípus** legördülő lista használatával keresse meg a figyelni kívánt erőforrást. Az erőforrást a keresősáv használatával is megkeresheti.

4. A cél erőforrás kiválasztása után kattintson a **feltétel hozzáadása** elemre.

5. Válassza ki a **"CPU-százalék" értéket**.

6. Szükség esetén pontosíthatja a metrikát az **időszak** és az **Összesítés** beállításával. A rendszer nem használja fel a "maximális" összesítési típust ehhez a metrikai típushoz, mert kevésbé jellemző a viselkedésre. A "maximális" összesítési típusnál a statikus küszöbérték talán megfelelőbb.

7. Ekkor megjelenik egy diagram a metrikához az elmúlt 6 órában. A riasztás paramétereinek megadása:
    1. **Feltétel típusa** – válassza a "dinamikus" lehetőséget.
    1. **Érzékenység** – a riasztási zaj csökkentése érdekében válassza a közepes/alacsony érzékenység lehetőséget.
    1. **Operátor** – válassza a "nagyobb, mint" lehetőséget, ha a viselkedés az alkalmazás használatát jelenti.
    1. **Gyakoriság** – csökkentse a riasztás üzleti hatásának csökkentését.
    1. **Sikertelen időszakok** (speciális beállítás) – a visszatekintő ablaknak legalább 15 percnek kell lennie. Ha például az időtartam öt percre van beállítva, akkor a sikertelen időszakoknak legalább háromnak kell lenniük.

8. A metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket.

9. Kattintson a **Kész** gombra.

10. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.

11. Adjon hozzá egy műveleti csoportot a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.

12. Kattintson a **kész** gombra a metrika riasztási szabályának mentéséhez.

> [!NOTE]
> A portálon létrehozott metrikai riasztási szabályok ugyanabban az erőforráscsoporthoz jönnek létre, mint a célként megadott erőforrás.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dinamikus küszöbértékek Application Insights HTTP-kérelem végrehajtási idején

1. [Azure Portal](https://portal.azure.com)kattintson a **figyelés** elemre. A figyelő nézet egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

2. Kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.

    > [!TIP]
    > A legtöbb erőforrás-panel a **figyelés** alatt is tartalmaz **riasztásokat** az erőforrás menüjében, és onnan is létrehozhat riasztásokat.

3. Kattintson a **cél kiválasztása** lehetőségre, a betöltött környezet ablaktáblán válassza ki azt a cél erőforrást, amelyre a riasztást szeretné használni. Az **előfizetés** és a **"Application Insights" erőforrástípus** legördülő lista használatával keresse meg a figyelni kívánt erőforrást. Az erőforrást a keresősáv használatával is megkeresheti.

4. A cél erőforrás kiválasztása után kattintson a **feltétel hozzáadása** elemre.

5. Válassza a **"http-kérelem végrehajtási ideje"** lehetőséget.

6. Szükség esetén pontosíthatja a metrikát az **időszak** és az **Összesítés** beállításával. A rendszer nem használja fel a "maximális" összesítési típust ehhez a metrikai típushoz, mert kevésbé jellemző a viselkedésre. A "maximális" összesítési típusnál a statikus küszöbérték talán megfelelőbb.

7. Ekkor megjelenik egy diagram a metrikához az elmúlt 6 órában. A riasztás paramétereinek megadása:
    1. **Feltétel típusa** – válassza a "dinamikus" lehetőséget.
    1. **Operátor** – válassza a "nagyobb, mint" lehetőséget, hogy csökkentse a riasztások időbeli javulását.
    1. **Gyakoriság** – csökkentse a riasztás üzleti hatásának csökkentését.

8. A metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket.

9. Kattintson a **Kész** gombra.

10. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.

11. Adjon hozzá egy műveleti csoportot a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.

12. Kattintson a **kész** gombra a metrika riasztási szabályának mentéséhez.

> [!NOTE]
> A portálon létrehozott metrikai riasztási szabályok ugyanabban az erőforráscsoporthoz jönnek létre, mint a célként megadott erőforrás.

## <a name="interpreting-dynamic-threshold-charts"></a>Dinamikus küszöbértékű diagramok értelmezése

A következő táblázat egy mérőszámot, a dinamikus küszöbértékeit és néhány riasztást mutat be, amikor az érték az engedélyezett küszöbértékeken kívül esik.

![További információ a metrikus riasztások konfigurálásáról](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Az előző diagram értelmezéséhez használja az alábbi információkat.

- **Kék vonal** – a tényleges mért metrika az idő függvényében.
- **Kék árnyékolású térség** – a metrika megengedett tartományát mutatja. Ha a metrika értékei ezen a tartományon belül maradnak, nem kerül sor riasztásra.
- **Kék pontok** – ha a diagram egy részén bal gombbal kattint, majd a kék vonal fölé viszi, megjelenik egy kék pont a kurzor alatt, amely egy egyedi összesített metrikai értéket jelenít meg.
- **Felugró ablak kék ponttal** – megjeleníti a mért metrikai értéket (a kék pontot), valamint az engedélyezett tartomány alsó és felső értékét.  
- **Piros pont és egy fekete kör** – a megengedett tartomány első metrikai értékét jeleníti meg. Ez az az érték, amely egy metrikai riasztást indít el, és aktív állapotba helyezi.
- **Piros pontok**– a megengedett tartományon kívül eső további mért értékeket jelez. Nem fognak további metrikai riasztásokat kiváltani, a riasztás azonban aktív marad.
- **Vörös terület** – azt az időpontot jeleníti meg, amikor a metrika értéke kívül esik az engedélyezett tartományon. A riasztás aktív állapotban marad mindaddig, amíg a későbbi mért értékek az engedélyezett tartományon kívül esnek, de új riasztások nem jelennek meg.
- A **vörös terület vége** – ha a kék vonal az engedélyezett értékeken belül van, a vörös terület leáll, a mért érték pedig kék színűre változik. A metrika riasztásának állapota a piros pont és a fekete körvonal közötti időszakban megoldott értékre van állítva.