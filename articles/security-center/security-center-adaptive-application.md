---
title: Adaptív alkalmazásvezérlők az Azure Security Centerben
description: Ez a dokumentum segít a Azure Security Center adaptív alkalmazás-vezérlésének használatában az Azure-gépeken futó alkalmazások listázásának engedélyezéséhez.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: b18b498f250e9ff62180ba21c3bcbda562b13b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735862"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Az adaptív alkalmazás-vezérlőelemek használatával csökkentheti a gépek támadási felületét

Ismerje meg az Azure Security Center adaptív alkalmazás-vezérlésének előnyeit, valamint azt, hogy miként javíthatja a biztonságot ezzel az adatvezérelt, intelligens szolgáltatással.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Mik azok az Security Center adaptív alkalmazások vezérlői?

Az adaptív alkalmazás-vezérlőelemek intelligens és automatizált megoldást biztosítanak a gépek ismert és biztonságos alkalmazásainak engedélyezési listájához. 

A szervezetek gyakran rendelkeznek olyan gyűjteményekkel, amelyek rendszeresen futtatják ugyanezeket a folyamatokat. Security Center a gépi tanulás segítségével elemzi a gépeken futó alkalmazásokat, és létrehoz egy listát az ismert biztonságos szoftverekről. Az engedélyezési listák az adott Azure-beli számítási feladatokon alapulnak, és a javaslatok az alábbi utasítások alapján is testreszabhatók.

Ha engedélyezte és konfigurálta az adaptív alkalmazások vezérlőit, akkor biztonsági riasztásokat fog kapni, ha bármely alkalmazás a biztonságosként definiált beállításoktól eltérő módon fut.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Milyen előnyökkel jár az adaptív alkalmazások vezérlése?

Ha az ismert alkalmazások listáját definiálja, és riasztásokat állít elő, amikor bármi más történik, több megerősítési célt is elérhet:

- Azonosíthatja a lehetséges kártevőket, még az antimalware-megoldások által esetlegesen kihagyható lehetőségeket is
- A csak licencelt szoftverek használatát diktáló helyi biztonsági házirendeknek való megfelelés javítása
- A régi vagy nem támogatott alkalmazások futtatásának elkerülése
- A szervezet által tiltott adott szoftverek letiltása
- Bizalmas adatokhoz hozzáférő alkalmazások áttekintésének fokozása

Jelenleg nem érhető el kényszerítési lehetőség. Az adaptív alkalmazások vezérlői a biztonsági riasztások biztosítására szolgálnak, ha bármely alkalmazás a biztonságosként megadott módon fut.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Támogatott gépek:|![Igen ](./media/icons/yes-icon.png) , Windows és Linux rendszerű Azure-és nem Azure-beli gépek<br>![Igen ](./media/icons/yes-icon.png) [Azure arc](../azure-arc/index.yml) -gépek|
|Szükséges szerepkörök és engedélyek:|A **biztonsági olvasó** és **olvasó** szerepkörök a csoportok és az ismert biztonságos alkalmazások listáját egyaránt megtekinthetik<br>A **közreműködő** és a **biztonsági rendszergazdai** szerepkörök egyaránt módosíthatják a csoportokat és az ismert alkalmazások listáját|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Alkalmazás-vezérlőelemek engedélyezése gépek csoportján

Ha a Security Center az előfizetésekben lévő olyan csoportokat azonosított, amelyek folyamatosan hasonló alkalmazásokat futtatnak, akkor a következő javaslattal kell megjelennie: az **adaptív alkalmazás-vezérlők a biztonságos alkalmazások definiálásához engedélyezni** kell a számítógépeken.

Válassza ki a javaslatot, vagy nyissa meg az adaptív alkalmazás-vezérlők lapot, ahol megtekintheti a javasolt ismert biztonságos alkalmazások és számítógépcsoportok listáját.

1. Nyissa meg az Azure Defender irányítópultját, és a speciális védelem területen válassza az **adaptív alkalmazás-vezérlőelemek** lehetőséget.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Az adaptív alkalmazások vezérlőinek megnyitása az Azure irányítópultról" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Megnyílik az **adaptív alkalmazás-vezérlők** lap a virtuális gépekkel a következő lapokon csoportosítva:

    - **Konfigurált** – olyan gépek csoportjai, amelyek már rendelkeznek meghatározott engedélyezési listával az alkalmazásokban. Az egyes csoportok esetében a konfigurált lap a következőket jeleníti meg:
        - a csoportban lévő gépek száma
        - Legutóbbi riasztások

    - **Ajánlott** – azonos alkalmazásokat futtató, és nem konfigurált engedélyezési listával rendelkező gépek csoportjai. Javasoljuk, hogy engedélyezze a csoportok adaptív alkalmazás-vezérlőit.
    
      > [!TIP]
      > Ha a "REVIEWGROUP" előtagú csoportnév jelenik meg, akkor az az alkalmazások részlegesen konzisztens listáját tartalmazó gépeket tartalmaz. Security Center nem lát mintázatot, de javasolja ennek a csoportnak a megtekintését annak ellenőrzéséhez, _hogy tud-_ e manuálisan definiálni valamilyen adaptív alkalmazás-vezérlési [szabályt a csoport adaptív alkalmazás-ellenőrzési szabályának szerkesztése](#edit-a-groups-adaptive-application-controls-rule)című részben leírtak szerint.
      >
      > A csoportba tartozó gépeket más csoportokra is áthelyezheti a [gép áthelyezése az egyik csoportból a másikba](#move-a-machine-from-one-group-to-another)című témakörben leírtak szerint.

    - **Nincs javaslat** – az alkalmazások meghatározott engedélyezési listáját nem tartalmazó gépek, és amelyek nem támogatják a szolgáltatást. A gép a következő okok miatt lehet ebben a lapon:
      - Hiányzik egy Log Analytics ügynök
      - Az Log Analytics ügynök nem küld eseményeket
      - Ez egy olyan Windows-gép, amelyen már meglévő [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) -házirend van engedélyezve egy csoportházirend-objektum vagy egy helyi biztonsági házirend segítségével.

      > [!TIP]
      > Security Centernek legalább két hétig kell lennie, hogy meghatározza a számítógépek csoportjaira vonatkozó egyedi ajánlásokat. Azok a gépek, amelyek nemrég lettek létrehozva, vagy olyan előfizetésekhez tartoznak, amelyek csak nemrég voltak engedélyezve az Azure Defenderrel, a **No javaslat** lapon jelennek meg.


1. Nyissa meg az **ajánlott** lapot. Megjelenik az ajánlott engedélyezési listával rendelkező gépek csoportjai.

   ![Ajánlott lap](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Válasszon ki egy csoportot. 

1. Az új szabály konfigurálásához tekintse át az **alkalmazás-ellenőrzési szabályok konfigurálása** oldal különböző részeit és a tartalmakat, amelyek egyediek lesznek az adott csoportba tartozó gépeken:

   ![Új szabály konfigurálása](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Gépek kiválasztása** – alapértelmezés szerint az azonosított csoportban lévő összes gép ki van választva. Törölje a jelölést, hogy eltávolítsa őket ebből a szabályból.
   
   1. **Ajánlott alkalmazások** – tekintse át azon alkalmazások listáját, amelyek a csoportban lévő gépekkel közösek, és azt javasoljuk, hogy futtasson.
   
   1. **További alkalmazások** – tekintse át azon alkalmazások listáját, amelyek vagy ritkábban láthatók a csoporton belüli gépeken, vagy ismertek a kihasználható. A figyelmeztető ikon azt jelzi, hogy egy támadó egy adott alkalmazást egy alkalmazás engedélyezési listájának megkerülésére használhat. Javasoljuk, hogy alaposan tekintse át ezeket az alkalmazásokat.

      > [!TIP]
      > Mindkét alkalmazás listája magában foglalja az adott alkalmazás bizonyos felhasználókra való korlátozásának lehetőségét. Ha lehetséges, alkalmazza a legalacsonyabb jogosultsági szint elvét.
      > 
      > Az alkalmazásokat a közzétevőik definiálják, ha az alkalmazás nem rendelkezik közzétevői információval (előjel nélküli), az adott alkalmazás teljes elérési útjához elérésiút-szabály jön létre.

   1. A szabály alkalmazásához válassza a **naplózás** lehetőséget. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Csoport adaptív alkalmazás-ellenőrzési szabályának szerkesztése

Dönthet úgy, hogy egy adott számítógépcsoport engedélyezési listáját a szervezete ismert változásai miatt szerkeszti. 

A számítógépek csoportjára vonatkozó szabályok szerkesztése:

1. Nyissa meg az Azure Defender irányítópultját, és a speciális védelem területen válassza az **adaptív alkalmazás-vezérlőelemek** lehetőséget.

1. A **konfigurált** lapon válassza ki a szerkeszteni kívánt szabályt tartalmazó csoportot.

1. Tekintse át az alkalmazás- **vezérlési szabályok konfigurálása** lap különböző szakaszait az [adaptív alkalmazás-vezérlőelemek engedélyezése gépek csoportjára](#enable-application-controls-on-a-group-of-machines)című részben leírtak szerint.

1. Szükség esetén adjon hozzá egy vagy több egyéni szabályt:

   1. Válassza a **szabály hozzáadása** elemet.

      ![Egyéni szabály hozzáadása](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Ha ismert biztonságos elérési utat definiál, módosítsa a **szabály típusát** "Path" értékre, és adjon meg egy elérési utat. Az elérési úthoz helyettesítő karaktereket is használhat.
   
      > [!TIP]
      > Bizonyos esetekben hasznos lehet az elérési út helyettesítő karakterei:
      > 
      > * Egy elérési út végén található helyettesítő karakter használatával engedélyezheti a mappában és az almappákban található összes végrehajtható fájl használatát.
      > * Egy elérési út közepén található helyettesítő karakter használatával engedélyezhető egy ismert végrehajtható név (például egy ismert végrehajtható fájlt tartalmazó személyes felhasználói mappák neve, automatikusan létrehozott mappanevek stb.).
  
   1. Adja meg az engedélyezett felhasználók és a védett fájltípusok típusát.

   1. Ha befejezte a szabály definiálását, válassza a **Hozzáadás** lehetőséget.

1. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.


## <a name="review-and-edit-a-groups-settings"></a>Csoport beállításainak áttekintése és szerkesztése

1. A csoport részleteinek és beállításainak megtekintéséhez válassza a **csoport beállításai** lehetőséget.

    Ez a panel a csoport nevét (amely módosítható), az operációs rendszer típusát, a helyet és az egyéb releváns adatokat jeleníti meg.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="A csoport beállításai lap az adaptív alkalmazás-vezérlőkhöz" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Szükség esetén módosíthatja a csoport nevét vagy fájltípusának védelmi módjait.

1. Válassza az **alkalmaz** és a **Mentés** lehetőséget.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Válaszoljon a "engedélyezési-szabályokra az adaptív alkalmazás-vezérlési szabályzatban" frissítés ajánlott

Ez a javaslat akkor jelenik meg, ha Security Center Machine learning azonosítja a korábban nem engedélyezett, potenciálisan legitim viselkedést. Az ajánlás új szabályokat javasol a meglévő definíciók számára a hamis pozitív riasztások számának csökkentése érdekében.

A problémák megoldásához:

1. A javaslatok lapon válassza ki az **adaptív alkalmazás-vezérlési házirend engedélyezési-szabályait** , hogy az újonnan azonosított, vélhetően legitim viselkedést tartalmazó csoportok megjelenítéséhez javasolt legyen a javaslat frissítése.

1. Válassza ki a szerkeszteni kívánt szabályt tartalmazó csoportot.

1. Tekintse át az alkalmazás- **vezérlési szabályok konfigurálása** lap különböző szakaszait az [adaptív alkalmazás-vezérlőelemek engedélyezése gépek csoportjára](#enable-application-controls-on-a-group-of-machines)című részben leírtak szerint.

1. A módosítások alkalmazásához válassza a **naplózás** lehetőséget.




## <a name="audit-alerts-and-violations"></a>Riasztások és szabálysértések naplózása

1. Nyissa meg az Azure Defender irányítópultját, és a speciális védelem területen válassza az **adaptív alkalmazás-vezérlőelemek** lehetőséget.

1. Ha olyan gépeket szeretne látni, amelyeken a legutóbbi riasztások vannak, tekintse át a **configured (konfigurált** ) lapon felsorolt csoportokat.

1. A további vizsgálathoz válasszon ki egy csoportot.

   ![Legutóbbi riasztások](./media/security-center-adaptive-application/recent-alerts.png)

1. További részletekért és az érintett gépek listájáért válassza ki a riasztást.



## <a name="move-a-machine-from-one-group-to-another"></a>Gép áthelyezése egyik csoportból a másikba

Ha a gépet az egyik csoportból a másikba helyezi át, a rendszer az alkalmazás-vezérlési házirendet annak a csoportnak a beállításaira módosítja, amelyre áthelyezte. A számítógépeket egy konfigurált csoportból nem konfigurált csoportba is áthelyezheti, ezzel eltávolítja a gépre alkalmazott összes alkalmazás-ellenőrzési szabályt.

1. Nyissa meg az Azure Defender irányítópultját, és a speciális védelem területen válassza az **adaptív alkalmazás-vezérlőelemek** lehetőséget.

1. Az **adaptív alkalmazás vezérlői** lap **konfigurált** lapján válassza ki az áthelyezni kívánt gépet tartalmazó csoportot.

1. Nyissa meg a  **konfigurált gépek** listáját.

1. Nyissa meg a gép menüjét a sor végén található három pontról, majd kattintson az **Áthelyezés** elemre. Megnyílik a **gép áthelyezése másik csoportba** panel.

1. Válassza ki a célcsoportot, és válassza a **gép áthelyezése** lehetőséget.

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.





## <a name="manage-application-controls-via-the-rest-api"></a>Alkalmazás-vezérlőelemek kezelése a REST API használatával 

Az adaptív alkalmazások programozott módon felügyeletéhez használja a REST API. 

A megfelelő API-dokumentáció a [Security Center API-docs adaptív alkalmazás-vezérlők szakaszában](/rest/api/securitycenter/adaptiveapplicationcontrols)érhető el.

A REST API elérhető függvények:

* A **lista** lekéri az összes csoportos javaslatát, és egy olyan JSON-t biztosít, amely minden csoporthoz tartalmaz egy objektumot.

* A **Get** beolvassa a JSON-t a teljes körű ajánlási adattal (azaz a gépek listáját, a közzétevő/elérési utak szabályait stb.).

* A **put** konfigurálja a szabályt (a kérés törzsének használatával a **beolvasott JSON-t használja** ).
 
   > [!IMPORTANT]
   > A **put** függvény kevesebb paramétert vár, mint amennyit a Get parancs által visszaadott JSON tartalmaz.
   >
   > Távolítsa el a következő tulajdonságokat, mielőtt a JSON-t használja a Put kérelemben: recommendationStatus, configurationStatus, Issues, Location és sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Gyakori kérdések – adaptív alkalmazások vezérlői

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Van lehetőség az alkalmazás vezérlőinek betartatására?
Jelenleg nem érhető el kényszerítési lehetőség. Az adaptív alkalmazások vezérlői a **biztonsági riasztások** biztosítására szolgálnak, ha bármely alkalmazás a biztonságosként megadott módon fut. Számos előnnyel rendelkeznek ([Milyen előnyökkel jár az adaptív alkalmazások vezérlése?](#what-are-the-benefits-of-adaptive-application-controls)), és az ezen az oldalon látható módon rendkívül testreszabható.

 

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan használhatja az adaptív alkalmazások vezérlését Azure Security Center az Azure-beli és nem Azure-alapú gépeken futó alkalmazások engedélyezési listájainak definiálásához. Ha többet szeretne megtudni Security Center más felhőalapú munkaterhelés-védelmi funkcióival kapcsolatban, tekintse meg a következőt:

* [Az igény szerinti (JIT) virtuálisgép-hozzáférés ismertetése](just-in-time-explained.md)
* [Az Azure Kubernetes-fürtök védelme](defender-for-kubernetes-introduction.md)