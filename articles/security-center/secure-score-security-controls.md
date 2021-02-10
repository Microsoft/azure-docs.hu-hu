---
title: Biztonsági pontszám az Azure Security Centerben
description: Azure Security Center biztonságos pontszámának és biztonsági ellenőrzésének leírása
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007676"
---
# <a name="secure-score-in-azure-security-center"></a>Biztonsági pontszám az Azure Security Centerben

## <a name="introduction-to-secure-score"></a>A biztonságos pontszám bemutatása

Azure Security Center két fő célja van: 

- az aktuális biztonsági helyzet megismerése érdekében
- a biztonság hatékonyságának és hatékony javításának elősegítése érdekében

A Security Center központi funkciója, amely lehetővé teszi a célok elérését **biztonságos pontszámként**.

Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákra. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint.

A biztonságos pontszám a Azure Portal oldalakon százalékos értékként jelenik meg, de a mögöttes értékeket is világosan mutatja:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Általános biztonsági pontszám a portálon látható módon":::

A biztonság növelése érdekében tekintse át a Security Center ajánlásainak lapját a pontszám növeléséhez szükséges kiemelkedő műveletekhez. Minden javaslat útmutatást tartalmaz az adott probléma megoldásához.

A javaslatok **biztonsági vezérlőkbe** vannak csoportosítva. Az egyes vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai, és a sebezhető támadási felületeket tükrözik. A pontszám csak akkor javul, ha egy vezérlőelemen belül egy adott erőforráshoz tartozó *összes* javaslatot szervizeli. Ha szeretné megtudni, hogy a szervezet milyen jól védi az egyes támadási felületeket, tekintse át az egyes biztonsági vezérlők pontszámait.

További információ: [a biztonságos pontszám kiszámítása](secure-score-security-controls.md#how-your-secure-score-is-calculated) alább. 


## <a name="access-your-secure-score"></a>Hozzáférés a biztonságos pontszámhoz

Az alábbi szakaszokban leírtak szerint megtalálhatja a teljes biztonsági pontszámot, valamint a pontszám/előfizetés alapján a Azure Portal vagy a programozott módon:

- [A portál biztonságos pontszámának beolvasása](#get-your-secure-score-from-the-portal)
- [Szerezze be biztonságos pontszámát a REST API](#get-your-secure-score-from-the-rest-api)
- [Az Azure Resource Graph (ARG) biztonságos pontszámának beolvasása](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>A portál biztonságos pontszámának beolvasása

Security Center a pontszám kiemelten jelenik meg a portálon: ez az első fő csempe az Security Center áttekintés oldalon. A csempe kiválasztásával a dedikált biztonságos pontszám oldalra kerül, ahol a pontszám az előfizetés alapján lebontva jelenik meg. Válasszon ki egy előfizetést, és tekintse meg a rangsorolt javaslatok részletes listáját, valamint azt, hogy milyen hatással lehet a szervizelését rájuk az előfizetés pontszáma. 

Az emlékeztetőhöz a biztonságos pontszám a Security Center portáljának oldalain a következő helyekről jelenik meg.

- Security Center **áttekintésében** lévő csempén (fő irányítópult):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A biztonságos pontszám Security Center irányítópultján":::

- A dedikált **biztonságos pontszám** lapon az előfizetés és a felügyeleti csoportok biztonságos pontszáma látható:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Az előfizetések biztonságos pontszáma Security Center biztonságos pontszám oldalon":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A felügyeleti csoportok biztonságos pontszáma Security Center biztonságos pontszám lapján":::

    > [!NOTE]
    > Azok a felügyeleti csoportok, amelyekhez nem rendelkezik megfelelő engedélyekkel, a pontszámát "korlátozott" értékként jelenítik meg. 

- A **javaslatok** lap tetején:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A biztonságos pontszám Security Center a javaslatok oldalon":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Szerezze be biztonságos pontszámát a REST API

A pontszám a Secure score API-n keresztül érhető el. Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Használhatja például a [Secure scores API](/rest/api/securitycenter/securescores) -t egy adott előfizetés pontszámának lekéréséhez. Emellett a [Secure score Controls API](/rest/api/securitycenter/securescorecontrols) használatával is listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát.

![Egyetlen biztonságos pontszám beolvasása az API-n keresztül](media/secure-score-security-controls/single-secure-score-via-api.png)

A biztonságos pontszám API-ra épülő eszközökre vonatkozó példákért tekintse meg [a GitHub-Közösség biztonságos pontszám területét](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Az Azure Resource Graph (ARG) biztonságos pontszámának beolvasása

Az Azure Resource Graph azonnali hozzáférést biztosít az erőforrás-információkhoz a felhőalapú környezetekben, robusztus szűrési, csoportosítási és rendezési képességekkel. Az Azure-előfizetések programozott vagy a Azure Portalon keresztüli lekérdezésének gyors és hatékony módja. [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).

Az ARG-sel rendelkező több előfizetés biztonságos pontszámának elérése:

1. A Azure Portal nyissa meg az **Azure Resource Graph Explorert**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Az Azure Resource Graph Explorer * * ajánlási oldalának elindítása" :::

1. Adja meg a Kusto-lekérdezést (az alábbi példák használatával útmutatást talál).

    - Ez a lekérdezés visszaadja az előfizetés-azonosítót, az aktuális pontszámot a pontokban és százalékban, valamint az előfizetés maximális pontszámát. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Ez a lekérdezés az összes biztonsági vezérlő állapotát adja vissza. Az egyes vezérlőknél a nem megfelelő állapotú erőforrások számát, az aktuális pontszámot és a maximális pontszámot kell megszereznie. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Válassza a **lekérdezés futtatása** lehetőséget.




## <a name="tracking-your-secure-score-over-time"></a>A biztonságos pontszám nyomon követése az idő függvényében

Ha Ön egy Pro-fiókkal rendelkező Power BI felhasználó, akkor a biztonságos pontszámot az **idő múlásával** Power bi irányítópulton követheti nyomon a biztonságos pontszámot az idő múlásával, és megvizsgálhatja a módosításokat.

> [!TIP]
> Ezt az irányítópultot és más eszközöket is megtalálhatja a biztonságos programozott módon való munkavégzéshez a GitHubon a Azure Security Center Közösség dedikált területén: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Az irányítópulton a következő két jelentés található, amelyek segítenek a biztonsági állapot elemzésében:

- **Erőforrások összegzése** – az erőforrások állapotával kapcsolatos összesített információkat biztosít.
- **Biztonságos pontszámok összegzése** – összesített információkat biztosít a pontszám előrehaladásával kapcsolatban. A pontszám változásainak megtekintéséhez használja a "biztonságos pontszám az idő múlásával egy időben" diagramot. Ha drámai változást tapasztal a pontszámban, tekintse meg a "a biztonságos pontszámra hatással lehet a védett pontok" táblázatot a módosítást okozó lehetséges változásokhoz. Ez a táblázat a törölt erőforrásokat, az újonnan telepített erőforrásokat, illetve az egyik javaslatban a biztonsági állapotukban megváltoztatott erőforrásokat ismerteti.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="A választható biztonsági pontszám az idő múlásával Power BI irányítópult a biztonságos pontszám nyomon követéséhez az idő múlásával és a változások kivizsgálásával":::





## <a name="how-your-secure-score-is-calculated"></a>A biztonságos pontszám kiszámításának módja 

Az egyes biztonsági ellenőrzéseknek az általános biztonsági pontszámhoz való hozzájárulása egyértelműen a javaslatok oldalon látható.

[![A fokozott biztonságú pontszám bevezeti a biztonsági vezérlőket](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

A biztonsági ellenőrzés összes lehetséges pontjának lekéréséhez minden erőforrásnak meg kell felelnie a biztonsági ellenőrzésen belüli összes biztonsági javaslatnak. Security Center például több javaslattal is rendelkezik a felügyeleti portok biztonságossá tételével kapcsolatban. Ahhoz, hogy a biztonságos pontszáma eltérő legyen, az összeset javítania kell.

Például a "rendszerfrissítések alkalmazása" nevű biztonsági vezérlő legfeljebb hat ponttal rendelkezik, amelyet az elemleírásban láthat a vezérlő lehetséges növelési értékeként:

[![A rendszerfrissítések alkalmazásának biztonsági vezérlése](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A vezérlő maximális pontszáma, rendszerfrissítések alkalmazása, mindig 6. Ebben a példában 50 erőforrás van. Így a maximális pontszámot 50-re osztjuk, és az eredmény az, hogy minden erőforrás a 0,12 pontot. 

* **Lehetséges növekedés** (0,12 x 8 nem megfelelő állapotú erőforrások = 0,96) – a vezérlőn belül elérhető további pontok. Ha kijavítja az ebben a vezérlőben található összes javaslatot, a pontszám 2%-kal nő (ebben az esetben az 0,96 pont 1 pontra kerekítve). 
* **Aktuális pontszám** (0,12 x 42 kifogástalan erőforrások = 5,04) – a vezérlő aktuális pontszáma. Az egyes vezérlőelemek a teljes pontszám irányába járulnak hozzá. Ebben a példában a vezérlő a 5,04-as számú aktuális biztonságos összegre mutat.
* Maximális **pontszám** – az összes, a vezérlőn belüli javaslat végrehajtásával elnyerhető pontok maximális száma. Egy vezérlőelem maximális pontszáma a vezérlőelem relatív jelentőségét jelzi. A maximális pontszám értékekkel osztályozhatja a problémákat, hogy először működjön. 


### <a name="calculations---understanding-your-score"></a>Számítások – a pontszám megismerése

|Metric|Képlet és példa|
|-|-|
|**Biztonsági vezérlő aktuális pontszáma**|<br>![A biztonsági vezérlő pontszámának kiszámításához használt egyenlet](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Minden egyes biztonsági ellenőrzés hozzájárul a biztonsági pontszám eléréséhez. A vezérlőn belüli javaslat által érintett összes erőforrás a vezérlő aktuális pontszámának irányába járul hozzá. Az egyes vezérlők aktuális pontszáma *a vezérlőben lévő erőforrások* állapotának mértéke.<br>![A biztonsági vezérlő aktuális pontszámának kiszámításakor használt értékeket megjelenítő elemleírások](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Ebben a példában a 6 maximális pontszáma 78-re osztható, mert ez az egészséges és a nem kifogástalan erőforrások összege.<br>6/78 = 0,0769<br>A jelenlegi pontszám a (4) kifogástalan állapotú erőforrások számával való szorzását eredményezi:<br>0,0769 * 4 = **0,31**<br><br>|
|**Biztonsági pontszám**<br>Egyetlen előfizetés|<br>![Az előfizetés biztonságos pontszámának kiszámításához használt egyenlet](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Egyszeri előfizetés biztonságos pontszáma minden engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Ebben a példában egyetlen előfizetés van az összes rendelkezésre álló biztonsági vezérlővel (a 60-pontok lehetséges maximális pontszáma). A pontszám 28 pontot mutat a lehetséges 60-ből, a fennmaradó 32 pont pedig a biztonsági vezérlők "lehetséges pontszám növelésének" számadatait tükrözi.<br>![A vezérlőelemek listája és a lehetséges pontszám növekedése](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Biztonsági pontszám**<br>Több előfizetés|<br>![Több előfizetés biztonságos pontszámának kiszámításához használt egyenlet](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Több előfizetés összesített pontszámának kiszámításakor Security Center az egyes előfizetésekhez tartozó *súlyozást* is tartalmaz. Az előfizetések relatív súlyozását az Security Center határozza meg, az olyan tényezők alapján, mint az erőforrások száma.<br>Az egyes előfizetések aktuális pontszámát ugyanúgy számítjuk ki, mint egyetlen előfizetésnél, de a súlyozást a rendszer az egyenletben látható módon alkalmazza.<br>Több előfizetés megtekintésekor a biztonságos pontszám kiértékeli az összes engedélyezett szabályzaton belüli összes erőforrást, és a biztonsági vezérlők maximális pontszámára vonatkozó együttes hatásukat.<br>![Több előfizetés biztonságos pontszáma minden engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Az összesített pontszám **nem** átlag; Ehelyett az összes előfizetés összes erőforrása állapotának kiértékelt testtartása.<br>Itt is, ha a javaslatok lapra lép, és hozzáadja az elérhető lehetséges pontokat, akkor a jelenlegi pontszám (24) és a rendelkezésre álló maximális pontszám (60) közötti különbség jelenik meg.|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Mely javaslatok szerepelnek a biztonságos pontszámok számításában?

Csak a beépített javaslatok befolyásolhatják a biztonságos pontszámot.

Az **előzetesként** megjelölt javaslatok nem szerepelnek a biztonságos pontszám számításában. Ha lehetséges, még mindig szervizelni kell őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul.

Példa az előzetes verziójú javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat az előnézet jelölővel":::

## <a name="improve-your-secure-score"></a>Biztonsági pontszám javítása

A biztonságos pontszám javítása érdekében javítsa a javaslatok listáját a biztonsági javaslatok kijavításával. Az egyes javaslatokat minden erőforráshoz manuálisan, vagy a **gyors javítás** használatával javíthatja. lehetőség (ha elérhető) egy, az erőforrások egy csoportjára vonatkozó javaslatra vonatkozó szervizelés alkalmazásához. További információ: [javaslatok szervizelése](security-center-remediate-recommendations.md).

A pontszám javításának egy másik módja, és gondoskodhat arról, hogy a felhasználók ne hozzanak létre olyan erőforrásokat, amelyek negatív hatással vannak a pontszámra, hogy konfigurálja a vonatkozó javaslatok érvényesítési és megtagadási beállításait. További információ a [helytelen konfigurációkkal kapcsolatos kényszerítő/megtagadási javaslatok megelőzéséről](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Biztonsági vezérlők és javaslataik

Az alábbi táblázat a Azure Security Center biztonsági vezérlőit sorolja fel. Az egyes vezérlőknél megtekintheti a biztonságos pontszámhoz adható pontok maximális számát, ha a vezérlőben felsorolt *összes* javaslatot kijavítja az *összes* erőforráshoz. 

A Security Center által biztosított biztonsági javaslatok készlete az egyes szervezeti környezetekben elérhető erőforrásokra van szabva. A javaslatok részletesebben testreszabhatók, ha [letiltják a házirendeket](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) , és az [adott erőforrásokra vonatkozó javaslat alól kivételt jelentenek](exempt-resource.md). 
 
Javasoljuk, hogy minden szervezet körültekintően tekintse át a hozzájuk rendelt Azure Policy kezdeményezéseket. 

> [!TIP]
> A kezdeményezések áttekintésével és szerkesztésével kapcsolatos részletekért lásd: [a biztonsági szabályzatok használata](tutorial-security-policy.md). 

Bár az Security Center alapértelmezett biztonsági kezdeményezése az iparági ajánlott eljárások és szabványok alapján történik, előfordulhat, hogy az alább felsorolt beépített javaslatok nem teljesen illeszkednek a szervezetéhez. Ennek következtében időnként szükség lehet az alapértelmezett kezdeményezés módosítására – a biztonság veszélyeztetése nélkül – annak biztosítása érdekében, hogy az összhangban legyen a szervezet saját házirendjeivel. az iparági szabványok, a szabályozási szabványok és a szükséges referenciaértékek.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-Collapse: Collapse; szegély – térköz: 0;}. TG TF {Border-Color: fekete; szegély stílusa: Solid; Border-width: 1px; betűkészlet-család: Arial, Sans-Serif; betűméret: 14px; túlcsordulás: rejtett; kitöltés: 10px 5px; Word-break: Normal;}. TG th {Border-Color: Black; Border-Style: Solid; Szegély szélessége: 1px; betűkészlet-család: Arial, Sans-Serif; betűkészlet-méret: 18px; font-Weight: Normal; túlcsordulás: rejtett; kitöltés: 10px 5px; Word-break: Normal;}. TG. TG-cly1 {Text-igazítás: balra; függőleges igazítás: középső}. TG. TG-lboi {Border-Color: öröklés; szöveg-igazítás: balra; függőleges igazítás: középső} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Biztonságos pontszám – gyakori kérdések

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Ha egy biztonsági ellenőrzésben csak három négy javaslat közül hármat találok, akkor a biztonságos pontszámom módosul?
Nem. Ez addig nem változik meg, amíg az egyetlen erőforráshoz tartozó összes ajánlást nem szervizeli. Egy vezérlőelem maximális pontszámának lekéréséhez az összes erőforrásra vonatkozóan az összes javaslatot szervizelni kell.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Ha egy javaslat nem alkalmazható a számomra, és letiltom a szabályzatban, akkor a biztonsági ellenőrzésem teljesül, és a biztonságos pontszám frissítve lett?
Igen. Javasoljuk, hogy tiltsa le a javaslatokat, ha azok nem alkalmazhatók a környezetben. A konkrét javaslatok letiltásával kapcsolatos utasításokért lásd: [biztonsági szabályzatok letiltása](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Ha egy biztonsági vezérlő nulla pontot biztosít a biztonságos pontszám felé, figyelmen kívül hagyhatom?
Bizonyos esetekben a vezérlőelem maximális pontszáma nullánál nagyobb lesz, de a hatás nulla. Ha az erőforrások kijavításának növekményes pontszáma elhanyagolható, a rendszer nulla értékűre kerekíti. Ne hagyja figyelmen kívül ezeket az ajánlásokat, mivel azok továbbra is biztonsági javítást tesznek lehetővé. Az egyetlen kivétel a "további ajánlott eljárás" vezérlő. Szervizelését ezekkel az ajánlásokkal nem növeli a pontszámát, de a teljes biztonságot is növeli.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a biztonságos pontszámot és az általa bevezetett biztonsági ellenőrzéseket ismerteti. A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket:

- [Tudnivalók a javaslatok különböző elemeiről](security-center-recommendations.md)
- [Útmutató a javaslatok megoldásához](security-center-remediate-recommendations.md)
- [A GitHub-alapú eszközök megtekintése a biztonságos pontszám használatával történő programozott munkához](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)