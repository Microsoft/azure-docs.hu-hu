---
title: Adaptív alkalmazásvezérlők az Azure Security Centerben
description: Ez a dokumentum segítséget nyújt az adaptív alkalmazásvezérlés Azure Security Center az Azure-gépeken futó alkalmazások listába való listához.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834614"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Adaptív alkalmazásvezérlők használata a gépek támadási felületének csökkentéséhez

Megismerheti a Azure Security Center adaptív alkalmazásvezérlőinek előnyeit, és megtudhatja, hogyan javíthatja a biztonságot ezzel az adatvezérelt, intelligens funkcióval.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Mik Security Center adaptív alkalmazásvezérlői?

Az adaptív alkalmazásvezérlők intelligens és automatizált megoldások, amelyek lehetővé teszik az ismerten biztonságos alkalmazások listáinak definiálása a gépeken. 

A szervezetek gyakran olyan gépgyűjteményekkel is vannak, amelyek rendszeresen ugyanazt a folyamatot futtatják. Security Center gépi tanulással elemzi a gépeken futó alkalmazásokat, és létrehoz egy listát az ismerten biztonságos szoftverekről. Az engedélyező listák az adott Azure-beli számítási feladatokon alapulnak, a javaslatokat pedig az alábbi utasítások alapján testreszabhatja.

Ha engedélyezte és konfigurálta az adaptív alkalmazásvezérlőket, biztonsági riasztásokat kap, ha az Ön által biztonságosként meghatározottakon kívül bármely alkalmazás fut.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Milyen előnyökkel jár az adaptív alkalmazásvezérlők használata?

Az ismerten biztonságos alkalmazások listáinak definiálása és a riasztások generálása bármely más végrehajtásakor több cél elérése is lehet:

- Azonosítsa a potenciális kártevőket, még azokat is, amelyek kimaradnak a kártevőirtó megoldások által
- A csak licencelt szoftverek használatát előírjákó helyi biztonsági házirendek megfelelőségét javítva
- Kerülje a régi vagy nem támogatott alkalmazások futtatását
- A szervezet által tiltott szoftverek megakadályozása
- A bizalmas adatokhoz hozzáférő alkalmazások felügyeletének növelése

Jelenleg nincsenek kényszerítési lehetőségek. Az adaptív alkalmazásvezérlők célja, hogy biztonsági riasztásokat adjanak, ha az Ön által biztonságosként meghatározottakon kívül bármely alkalmazás fut.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadási állapot:|Általánosan elérhető (GA)|
|Árképzés:|Kiszolgálókhoz [Azure Defender szükséges](defender-for-servers-introduction.md)|
|Támogatott gépek:|![Igen, Windowst és Linuxot futtató Azure- és nem ](./media/icons/yes-icon.png) Azure-beli gépek<br>![](./media/icons/yes-icon.png) [Igen Azure Arc](../azure-arc/index.yml) gépek|
|Szükséges szerepkörök és engedélyek:|**A Biztonsági olvasó** **és** az Olvasó szerepkör a csoportokat és az ismerten biztonságos alkalmazások listáját is megtekintheti<br>**A** **közreműködői és biztonsági** rendszergazdai szerepkörök a csoportokat és az ismerten biztonságos alkalmazások listáját is szerkeszthetik|
|Felhők:|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Országos/szuverén (US Gov, China Gov, Other Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Alkalmazásvezérlők engedélyezése gépek egy csoportján

Ha a Security Center olyan gépcsoportokat azonosított az előfizetésében, amelyek következetesen hasonló alkalmazásokat futtatnak, a rendszer a következő javaslatot fogja kérni: A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a **gépeken.**

Válassza ki a javaslatot, vagy nyissa meg az adaptív alkalmazásvezérlők lapot a javasolt, ismerten biztonságos alkalmazások és gépcsoportok listájának megtekintéséhez.

1. Nyissa meg Azure Defender irányítópultot, és a Speciális védelem területen válassza az **Adaptív alkalmazásvezérlők lehetőséget.**

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Adaptív alkalmazásvezérlők megnyitása az Azure-irányítópultról" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Megnyílik **az Adaptív alkalmazásvezérlők** lap, és a virtuális gépeket a következő lapokra csoportosítja:

    - **Konfigurálva** – Olyan gépek csoportjai, amelyek már rendelkezik meghatározott alkalmazás-engedélyezőlistával. A konfigurált lapon minden csoportnál a következő látható:
        - a csoportban a gépek száma
        - legutóbbi riasztások

    - **Ajánlott** – Olyan gépek csoportjai, amelyek konzisztensen futtatják ugyanezeket az alkalmazásokat, és nincsenek konfigurálva az engedélyezőlistával. Javasoljuk, hogy engedélyezze az adaptív alkalmazásvezérlőket ezekhez a csoportokhoz.
    
      > [!TIP]
      > Ha a csoport neve "REVIEWGROUP" előtaggal van megjelenik, akkor az alkalmazások részlegesen konzisztens listáját tartalmazó gépeket tartalmaz. Security Center minta nem látható, de javasolja ennek a csoportnak  az áttekintését annak meghatározásához, hogy definiálhat-e manuálisan adaptív alkalmazásvezérlő szabályokat a csoport adaptív alkalmazásvezérlő-szabályának szerkesztését ismertető dokumentumban leírtak [szerint.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > A gépeket áthelyezheti ebből a csoportból más csoportokba is a Gép áthelyezése egy csoportból [egy másikba.](#move-a-machine-from-one-group-to-another)

    - **Nincs javaslat** – A meghatározott alkalmazáslista nélküli gépek, amelyek nem támogatják a funkciót. Előfordulhat, hogy a gépe a következő okok miatt van ezen a lapon:
      - Hiányzik egy Log Analytics-ügynök
      - A Log Analytics-ügynök nem küld eseményeket
      - Windows rendszerű gép, amely egy csoportházirend-objektum vagy egy helyi biztonsági házirend által engedélyezett, már meglévő [AppLocker-szabályzattal](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview)

      > [!TIP]
      > Security Center legalább két hét adatra van szüksége az egyedi javaslatok gépcsoportonkénti meghatározásához. Azok a gépek, amelyeket nemrég hoztak létre, vagy amelyek csak a Azure Defender-val engedélyezett előfizetésekhez tartoznak, a **Nincs javaslat lapon jelennek** meg.


1. Nyissa meg **az Ajánlott** lapot. Megjelenik az ajánlott engedélyezőlistákkal a gépek csoportjai.

   ![Ajánlott lap](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Válasszon ki egy csoportot. 

1. Az új szabály konfigurálásához tekintse  át az Alkalmazásvezérlési szabályok konfigurálása lap különböző szakaszait és tartalmát, amelyek a gépek adott csoportjára egyediek lesznek:

   ![Új szabály konfigurálása](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Gépek kiválasztása** – Alapértelmezés szerint az azonosított csoportban minden gép ki van választva. Törölje az egyik kijelölését a szabályból való eltávolításukhoz.
   
   1. **Ajánlott alkalmazások** – Tekintse át az ebben a csoportban található gépeken gyakran használt alkalmazások listáját, és ajánlott a futtatás.
   
   1. **További alkalmazások** – Tekintse át ezt a listát azokról az alkalmazásokról, amelyek ritkábban láthatók a csoportban található gépeken, vagy amelyekről ismert, hogy kihasználhatók. A figyelmeztető ikon azt jelzi, hogy a támadók egy adott alkalmazás segítségével megkerülhetik az alkalmazások engedélyezőlistát. Javasoljuk, hogy alaposan tekintse át ezeket az alkalmazásokat.

      > [!TIP]
      > Mindkét alkalmazáslista lehetővé teszi egy adott alkalmazás adott felhasználókra való korlátozását. Amikor csak lehetséges, a legkisebb jogosultság elvének elfogadása.
      > 
      > Az alkalmazásokat a közzétevőik definiálják, ha egy alkalmazás nem tartalmaz közzétevői információt (nincs aláírva), akkor létrejön egy elérésiút-szabály az adott alkalmazás teljes elérési útjával.

   1. A szabály alkalmazáshoz válassza a Naplózás **lehetőséget.** 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Csoport adaptív alkalmazásvezérlési szabályának szerkesztése

Dönthet úgy, hogy a szervezet ismert változásai miatt szerkeszti a gépek egy csoportjának engedélyezőlistát. 

Gépek egy csoportjára vonatkozó szabályok szerkesztése:

1. Nyissa meg Azure Defender irányítópultot, és a Speciális védelem területen válassza az **Adaptív alkalmazásvezérlők lehetőséget.**

1. A **Konfigurált lapon** válassza ki azt a csoportot, amely a szerkeszteni kívánt szabályt adja meg.

1. Tekintse át az  Alkalmazásvezérlési szabályok konfigurálása lap különböző szakaszait az Adaptív alkalmazásvezérlők engedélyezése [gépek egy csoportján szakaszban leírtak szerint.](#enable-application-controls-on-a-group-of-machines)

1. Igény szerint hozzáadhat egy vagy több egyéni szabályt:

   1. Válassza **a Szabály hozzáadása lehetőséget.**

      ![Egyéni szabály hozzáadása](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Ha ismert biztonságos útvonalat definiál, módosítsa  a Szabály típusát "Elérési út" típusra, és adjon meg egyetlen elérési utat. Helyettesítő karaktereket is tartalmazhat az elérési útban.
   
      > [!TIP]
      > Bizonyos esetekben hasznosak lehetnek az elérési utak helyettesítő karakterei:
      > 
      > * Helyettesítő karakter használata az elérési út végén a mappában és az almappákban található összes végrehajtható fájl engedélyezése érdekében.
      > * Helyettesítő karakter használata az elérési út közepén egy ismert végrehajtható név és egy változó mappanév (például egy ismert végrehajtható fájlt tartalmazó személyes felhasználói mappák, automatikusan létrehozott mappanevek stb.) engedélyezéséhez.
  
   1. Határozza meg az engedélyezett felhasználókat és a védett fájltípusokat.

   1. Ha befejezte a szabály definiálása, válassza a Hozzáadás **lehetőséget.**

1. A módosítások alkalmazáshoz válassza a Mentés **lehetőséget.**


## <a name="review-and-edit-a-groups-settings"></a>Csoport beállításainak áttekintése és szerkesztése

1. A csoport részleteinek és beállításainak megtekintéséhez válassza a **Csoportbeállítások lehetőséget.**

    Ezen a panelen látható a csoport neve (amely módosítható), az operációs rendszer típusa, a hely és egyéb releváns részletek.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Az adaptív alkalmazásvezérlők csoportbeállítási oldala" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Ha szeretné, módosíthatja a csoport nevét vagy fájltípus-védelmi módjai.

1. Válassza az **Alkalmaz és** a **Mentés lehetőséget.**



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Válasz az adaptív alkalmazásvezérlési szabályzat engedélyezési szabályainak frissítésével kapcsolatos javaslatra

Ez a javaslat akkor látható, Security Center gépi tanulása olyan potenciálisan megbízható viselkedést azonosít, amely korábban nem volt engedélyezett. A javaslat új szabályokat javasol a meglévő definíciókhoz a téves riasztások számának csökkentése érdekében.

A problémák megoldása:

1. A javaslatok lapon válassza ki az **adaptív** alkalmazásvezérlési szabályzatBan található Engedélyezési szabályok frissítve javaslatokat az újonnan azonosított, potenciálisan megbízható viselkedéssel bíró csoportokhoz.

1. Válassza ki azt a csoportot, amely a szerkeszteni kívánt szabályt adja meg.

1. Tekintse át az Alkalmazásvezérlési szabályok konfigurálása lap különböző **szakaszait** az Adaptív alkalmazásvezérlők engedélyezése gépek egy [csoportján szakaszban leírtak szerint.](#enable-application-controls-on-a-group-of-machines)

1. A módosítások alkalmazáshoz válassza a Naplózás **lehetőséget.**




## <a name="audit-alerts-and-violations"></a>Riasztások és szabálysértések naplózása

1. Nyissa meg Azure Defender irányítópultot, és a Speciális védelem területen válassza az **Adaptív alkalmazásvezérlők lehetőséget.**

1. A legutóbbi riasztásokat figyelő gépekkel kapcsolatos csoportokért tekintse át a Konfigurált lapon felsorolt **csoportokat.**

1. A további vizsgálathoz válasszon ki egy csoportot.

   ![Legutóbbi riasztások](./media/security-center-adaptive-application/recent-alerts.png)

1. További részletekért és az érintett gépek listájáért válasszon ki egy riasztást.

    A riasztások lap a riasztások további részleteit jeleníti meg, és **a** Művelet művelet hivatkozását tartalmazza, amely a fenyegetés elhárítására vonatkozó javaslatokat tartalmaz.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Az adaptív alkalmazásvezérlési riasztások kezdési ideje a következő: ":::

    > [!NOTE]
    > Az adaptív alkalmazásvezérlők 12 óránként számítják ki az eseményeket. A riasztások oldalán látható "tevékenység kezdési ideje" az az idő,  amikor az adaptív alkalmazásvezérlők létrehozták a riasztást, nem pedig az az időpont, amikor a gyanús folyamat aktív volt.


## <a name="move-a-machine-from-one-group-to-another"></a>Gép áthelyezése egyik csoportból a másikba

Amikor áthelyez egy gépet az egyik csoportból a másikba, az arra alkalmazott alkalmazásvezérlési szabályzat annak a csoportnak a beállításaira módosul, amelybe áthelyezte. A gépeket át is helyezze egy konfigurált csoportból egy nem konfigurált csoportba, ezzel eltávolítja a gépre alkalmazott alkalmazásvezérlési szabályokat.

1. Nyissa meg Azure Defender irányítópultot, és a Speciális védelem területen válassza az **Adaptív alkalmazásvezérlők lehetőséget.**

1. Az **Adaptív alkalmazásvezérlők** lapon, a **Konfigurálva** lapon válassza ki az áthelyezni kívánt gépet tartalmazó csoportot.

1. Nyissa meg a Konfigurált **gépek listáját.**

1. Nyissa meg a számítógép menüjét a sor végén található három pontból, majd válassza az Áthelyezés **lehetőséget.** Megnyílik **a Gép áthelyezése másik csoportba** panel.

1. Válassza ki a célcsoportot, majd válassza a **Gép áthelyezése lehetőséget.**

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.





## <a name="manage-application-controls-via-the-rest-api"></a>Alkalmazásvezérlők kezelése a REST API 

Az adaptív alkalmazásvezérlők programon keresztüli kezeléséhez használja a REST API. 

A megfelelő API-dokumentáció az API-dokumentáció adaptív alkalmazásvezérlőkről Security Center szakaszában [érhető el.](/rest/api/securitycenter/adaptiveapplicationcontrols)

Néhány függvény, amely elérhető a REST API:

* **A Lista** lekéri az összes csoportra vonatkozó javaslatot, és egy JSON-t biztosít az egyes csoportokhoz egy-egy objektummal.

* **Lekéri** a JSON-t a teljes javaslatadatokkal (vagyis a gépek listájával, közzétevői/elérésiút-szabályokkal stb.).

* **A Put** konfigurálja a szabályt (használja azt a JSON-t, amely a kérés törzseként a **Get** használatával kérhető le).
 
   > [!IMPORTANT]
   > A **Put** függvény kevesebb paramétert vár, mint a Get parancs által visszaadott JSON.
   >
   > Távolítsa el a következő tulajdonságokat, mielőtt a JSON-t használná a Put kérésben: recommendationStatus, configurationStatus, issues, location és sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Gyakori kérdések – Adaptív alkalmazásvezérlők

- [Van lehetőség az alkalmazásvezérlők kényszerítésében?](#are-there-any-options-to-enforce-the-application-controls)
- [Miért látom a Qualys-alkalmazást az ajánlott alkalmazásokban?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Van lehetőség az alkalmazásvezérlők kényszerítésében?
Jelenleg nincsenek kényszerítési lehetőségek. Az adaptív alkalmazásvezérlők  célja, hogy biztonsági riasztásokat adjanak, ha az Ön által biztonságosként meghatározottakon kívül bármely alkalmazás fut. Számos előnyt kínálnak (mik az adaptív alkalmazásvezérlők[előnyei?),](#what-are-the-benefits-of-adaptive-application-controls)és rendkívül testreszabhatók, ahogy ezen az oldalon is látható.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Miért látom a Qualys-alkalmazást az ajánlott alkalmazásokban?
[Azure Defender a biztonsági rések](defender-for-servers-introduction.md) további költségek nélkül történő keresését is magában foglalja a gépeken. Nincs szükség Qualys-licencre vagy akár Qualys-fiókra – minden problémamentesen kezelhető a Security Center. A vizsgálat részleteiért és a telepítésével kapcsolatos utasításokért lásd a Defender integrált sebezhetőségi [felmérési megoldását.](deploy-vulnerability-assessment-vm.md)

Annak érdekében, hogy a képolvasó üzembe helyezése Security Center ne generáljon riasztásokat, az adaptív alkalmazásvezérlők javasolt engedélyezése lista tartalmazza az összes gép olvasóját. 


## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megtanulta, hogyan használhatja az adaptív alkalmazásvezérlést a Azure Security Center azure-beli és nem Azure-beli gépeken futó alkalmazások engedélyező listáinak meghatározásához. A felhőbeli számítási feladatok egyéb Security Center egyes funkcióival kapcsolatos további információkért lásd:

* [Az igény szerinti (JIT) virtuálisgép-hozzáférés ismertetése](just-in-time-explained.md)
* [Az Azure Kubernetes-fürtök biztonságossá tétele](defender-for-kubernetes-introduction.md)