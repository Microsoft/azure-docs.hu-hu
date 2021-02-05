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
ms.openlocfilehash: bd64ca877f0bfd81597e091ef8ef000ad1b76992
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99526432"
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
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Biztonsági ellenőrzés, pontszám és leírás</b><br></th>
    <th class="tg-cly1"><b>Javaslatok</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">MFA engedélyezése (legfeljebb 10. pontszám)</p></strong>Ha csak jelszó használatával hitelesíti a felhasználót, a támadási vektor nyitva marad. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval?<br>Ha az <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> engedélyezve van, a fiókok biztonságosabbak, és a felhasználók továbbra is elvégezhetik a hitelesítést az egyszeri bejelentkezéssel (SSO) rendelkező alkalmazásokkal.</td>
    <td class="tg-lboi"; width=55%>- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon<br>- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonságos felügyeleti portok (max. pontszám 8)</p></strong>A találgatásos támadás célja, hogy hozzáférjen a virtuális géphez. Mivel a portok nem mindig nyílnak meg, az egyik kockázatcsökkentő stratégia az igény szerinti hálózati hozzáférés-vezérléssel, a hálózati biztonsági csoportokkal és a virtuálisgép-portok felügyeletével való kitettség csökkentése.<br>Mivel számos informatikai szervezet nem blokkolja a hálózatról kimenő SSH-kommunikációt, a támadók olyan titkosított alagutakat hozhatnak létre, amelyek lehetővé teszik a fertőzött rendszereken lévő RDP-portok számára a támadók számára a kiszolgálók vezérlését. A támadók a Rendszerfelügyeleti webszolgáltatások alrendszerével később áthelyezhetik a környezetét, és ellopott hitelesítő adatokkal érhetik el a hálózat egyéb erőforrásait.</td>
    <td class="tg-lboi"; width=55%>- A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni<br>- A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>- A felügyeleti portokat be kell zárni a virtuális gépeken</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Rendszerfrissítések alkalmazása (max. pontszám 6)</p></strong>A rendszerfrissítések biztosítják a szervezetek számára a működési hatékonyság fenntartását, csökkentik a biztonsági réseket, és stabilabb környezetet biztosítanak a végfelhasználók számára. A frissítések nem vonatkoznak a nem javított biztonsági rések és a támadásokra fogékony környezetek eredményeire. Ezek a sebezhetőségek felhasználhatók, és az adatvesztés, az adatkiszűrése, a ransomware és az erőforrás-visszaélések eléréséhez vezethetnek. A rendszerfrissítések központi telepítéséhez a <a href="/azure/automation/update-management/overview">Update Management megoldás segítségével kezelheti a virtuális gépek javításait és frissítéseit</a> . Az Update Management a szoftverek kiadásainak központi telepítésének és karbantartásának szabályozása.</td>
    <td class="tg-lboi"; width=55%>- A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>- A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>- A figyelési ügynököt telepíteni kell a gépekre<br>- Az operációsrendszer-verziót frissíteni kell a Cloud Service-szerepkörökhöz<br>- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell<br>- A számítógépekre telepíteni kell a rendszerfrissítéseket<br>- A számítógépeket újra kell indítani a rendszerfrissítések alkalmazásához<br>- A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni<br>- A figyelési ügynököt telepíteni kell a virtuális gépekre<br>- Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>- Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonsági rések szervizelése (max. pontszám 6)</p></strong>A biztonsági rések olyan gyengeségek, amelyeket a veszélyforrások használhatnak, így veszélyeztethetik az erőforrások titkosságát, rendelkezésre állását vagy integritását. A <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">sebezhetőségek kezelése</a> csökkenti a szervezeti expozíciót, megerősíti a végpont felületét, növeli a szervezeti rugalmasságot, és csökkenti az erőforrások támadási felületét. A fenyegetések és a biztonsági rések kezelése a szoftveres és biztonsági konfigurációs hibák láthatóságát és a megoldásokra vonatkozó ajánlásokat biztosít.</td>
    <td class="tg-lboi"; width=55%>- A speciális adatbiztonságot engedélyezni kell SQL Database<br>- A Azure Container Registry lemezképek biztonsági réseit szervizelni kell<br>- Az SQL-adatbázisok biztonsági réseit szervizelni kell<br>- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni<br>- A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon<br>- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon<br>- A sebezhetőség-felmérési megoldást telepíteni kell a virtuális gépekre<br>- A tároló lemezképeit csak megbízható nyilvántartásból kell telepíteni (előzetes verzió)<br>- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Titkosítás engedélyezése nyugalmi állapotban (max. pontszám 4)</p></strong>A inaktív adatok <a href="/azure/security/fundamentals/encryption-atrest">titkosítása</a> adatvédelmet biztosít a tárolt adatok számára. A REST-adatok elleni támadások közé tartozik az adatok tárolására szolgáló hardver fizikai hozzáférésének megszerzésére irányuló kísérlet. Az Azure szimmetrikus titkosítást használ a nagy mennyiségű inaktív adatok titkosításához és visszafejtéséhez. A szimmetrikus titkosítási kulcs használatával titkosíthatja az adattárakat a tárolóba való írás során. Ezt a titkosítási kulcsot is használja a rendszer, hogy visszafejtse az adott adatmennyiséget, mert az readied a memóriában való használathoz. A kulcsokat biztonságos helyen kell tárolni, az identitás-alapú hozzáférés-vezérléssel és a naplózási házirendekkel. Egy ilyen biztonságos hely Azure Key Vault. Ha egy támadó megszerzi a titkosított, de nem a titkosítási kulcsokat, a támadó nem fér hozzá az adatforráshoz a titkosítás megszakítása nélkül.</td>
    <td class="tg-lboi"; width=55%>- A lemezes titkosítást a virtuális gépeken kell alkalmazni<br>- Engedélyezve kell lennie a SQL Database transzparens adattitkosítás<br>- Az Automation-fiók változóit titkosítani kell<br>- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani<br>- Az SQL Server TDE-védőt a saját kulccsal kell titkosítani</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Adatforgalom titkosítása (max. pontszám 4)</p></strong>Az adatok átvitele az összetevők, a helyszínek és a programok között történik. Azok a szervezetek, amelyek nem védik az adatátvitelt, a támadók, a lehallgatás és a munkamenet-eltérítések érzékenyek. Az adatcseréhez és a VPN-hez ajánlott SSL/TLS protokollt használni. Amikor titkosított adatokat küld egy Azure-beli virtuális gép és egy helyszíni hely között az interneten keresztül, egy virtuális hálózati átjárót (például az <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">azure VPN Gateway</a> -t) használhat a titkosított forgalom elküldéséhez.</td>
    <td class="tg-lboi"; width=55%>- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el<br>- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el<br>- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve<br>- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell<br>- A webalkalmazás csak HTTPS protokollon keresztül érhető el<br>- A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz<br>- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében<br>- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon<br>- A TLS-t frissíteni kell az API-alkalmazás legújabb verziójára<br>- A TLS-t frissíteni kell a Function alkalmazás legújabb verziójára<br>- A TLS-t a webalkalmazás legújabb verziójára kell frissíteni<br>- FTPS szükséges az API-alkalmazásban<br>- A FTPS kötelező megadni a Function alkalmazásban<br>- A FTPS kötelező megadni a webalkalmazásban</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Hozzáférés és engedélyek kezelése (max. pontszám 4)</p></strong>A biztonsági program alapvető része annak biztosítása, hogy a felhasználók a feladataik elvégzéséhez szükséges hozzáféréssel rendelkezzenek, de nem több mint: a <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">legkevésbé privilegizált hozzáférési modell</a>.<br>Az erőforrásokhoz való hozzáférés szabályozása szerepkör-hozzárendelések létrehozásával az <a href="/azure/role-based-access-control/overview">Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)</a>. A szerepkör-hozzárendelés három elemet tartalmaz:<br>- <strong>Rendszerbiztonsági tag</strong>: a felhasználó által hozzáférést kérő objektum<br>- <strong>Szerepkör-definíció</strong>: az engedélyeik<br>- <strong>Hatókör</strong>: azon erőforrások készlete, amelyekre az engedélyek érvényesek</td>
    <td class="tg-lboi"; width=55%>- Az elavult fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- Az előfizetéshez egynél több tulajdonos rendelhető hozzá<br>- Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) kell használni a Kubernetes-szolgáltatásokban (előzetes verzió)<br>- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez<br>- Az egyszerű szolgáltatásokat a felügyeleti tanúsítványok helyett az előfizetések megóvására kell használni<br>- A minimális jogosultsági szintű Linux-funkciókat kötelező kikényszeríteni a tárolók számára (előzetes verzió)<br>- Nem módosítható (csak olvasható) rendszerindító fájlrendszert kell kikényszeríteni a tárolók számára (előzetes verzió)<br>- A jogosultság-eszkalációs tárolót el kell kerülni (előzetes verzió)<br>- A tárolók futtatását root felhasználóként el kell kerülni (előzetes verzió)<br>- A bizalmas gazdagépek névtereit megosztó tárolókat el kell kerülni (előzetes verzió)<br>- A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni (előzetes verzió)<br>- Az emelt szintű tárolókat el kell kerülni (előzetes verzió)<br>- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön (előzetes verzió)<br>- A felügyelt identitást az API-alkalmazásban kell használni<br>- A felügyelt identitást a Function alkalmazásban kell használni<br>- A felügyelt identitást a webalkalmazásban kell használni</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonsági konfigurációk szervizelése (max. pontszám 4)</p></strong>A helytelenül konfigurált informatikai eszközök nagyobb kockázatot jelentenek a támadásra. Az alapszintű megerősítési műveleteket gyakran elfelejtette az eszközök üzembe helyezése, és a határidőknek teljesülnie kell. A biztonsági beállítások az infrastruktúra bármely szintjén lehetnek: az operációs rendszertől és a hálózati berendezésektől a Felhőbeli erőforrásokhoz.<br>Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. Ha konfigurálta a szervezete számára fontos "megfelelőségi csomagokat" (szabványokat és alapkonfigurációkat), akkor a hiányosságok a CCEID tartalmazó biztonsági javaslatokat, valamint az esetleges biztonsági hatás magyarázatát eredményezik.<br>A gyakran használt csomagok az <a href="/azure/security/benchmarks/introduction">Azure Security benchmark</a> és a <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure founds benchmark Version 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell<br>- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell<br>- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell<br>- A figyelési ügynököt telepíteni kell a virtuális gépekre<br>- A figyelési ügynököt telepíteni kell a gépekre<br>- Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>- Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>- A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>- A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>- A tárolók AppArmor-profiljának felülbírálásával vagy letiltásával korlátozva kell lennie (előzetes verzió)<br>- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Nem engedélyezett hálózati hozzáférés korlátozása (max. pontszám 4)</p></strong>A szervezeten belüli végpontok közvetlen kapcsolódást biztosítanak a virtuális hálózatról a támogatott Azure-szolgáltatásokhoz. Az alhálózatban lévő virtuális gépek kommunikálhatnak az összes erőforrással. Ha korlátozni szeretné az alhálózaton belüli és az erőforrások közötti kommunikációt, hozzon létre egy hálózati biztonsági csoportot, és rendelje hozzá az alhálózathoz. A szervezetek a bejövő és kimenő szabályok létrehozásával korlátozhatják és védhetők a jogosulatlan forgalom ellen.</td>
    <td class="tg-lboi"; width=55%>- A virtuális gépen lévő IP-továbbítást le kell tiltani<br>- A Kubernetes-szolgáltatásokban (előzetes verzió) meg kell határozni a jóváhagyott IP-tartományokat.<br>- ELAVULT A App Services elérését korlátozni kell (előzetes verzió)<br>- ELAVULT A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni<br>- A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>- A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését<br>- A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését<br>- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz<br>- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz<br>- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás<br>- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz<br>- A hozzáférésnek korlátozva kell lennie az internetre irányuló virtuális gépekkel rendelkező, engedékeny hálózati biztonsági csoportoknak<br>- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni<br>- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön (előzetes verzió)<br>- A tárolók csak az engedélyezett portokat figyelik (előzetes verzió)<br>- A szolgáltatásoknak csak az engedélyezett portok figyelésére kell figyelniük (előzetes verzió)<br>- A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell (előzetes verzió)<br>- A virtuális hálózatokat Azure Firewall kell védeni (előzetes verzió)<br>- A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon<br>- A magánhálózati végpontot engedélyezni kell a MySQL-kiszolgálókon<br>- A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Adaptív alkalmazások vezérlésének alkalmazása (max. pontszám 3)</p></strong>Az adaptív alkalmazás-vezérlés (AAC) egy intelligens, automatizált és teljes körű megoldás, amellyel szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban és a nem Azure-beli gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen.<br>A Security Center gépi tanulással hozza létre a számítógépek egy csoportjának ismert, biztonságos alkalmazásainak listáját.<br>A jóváhagyott alkalmazások listájának innovatív megközelítése a kezelési komplexitás nélkül biztosítja a biztonsági előnyöket.<br>Az AAC különösen fontos az olyan célra kiépített kiszolgálók esetében, amelyeknek adott alkalmazások futtatására van szükségük.</td>
    <td class="tg-lboi"; width=55%>- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken<br>- A figyelési ügynököt telepíteni kell a virtuális gépekre<br>- A figyelési ügynököt telepíteni kell a gépekre<br>- Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>- Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>- A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Az adatbesorolás alkalmazása (max. pontszám 0)</p></strong>A szervezet adatai érzékenység és üzleti hatás alapján történő besorolásával meghatározhatja és hozzárendelheti az adatértékeket, valamint megadhatja a stratégia és az irányítás alapját.<br>Az <a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> segíthet az adatbesorolásban. Titkosítási, identitás-és engedélyezési házirendeket használ az adatok védelme és az adatokhoz való hozzáférés korlátozása érdekében. A Microsoft által használt egyes besorolások nem üzleti, nyilvános, általános, bizalmas és szigorúan bizalmas jellegűek.</td>
    <td class="tg-lboi"; width=55%>- Az SQL-adatbázisokban lévő bizalmas adatokat osztályozni kell</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Alkalmazások elleni védelem a DDoS-támadások ellen (max. pontszám 2)</p></strong>Az elosztott szolgáltatásmegtagadási (DDoS) támadások elárasztják az erőforrásokat és az alkalmazások használhatatlanná teszik a szolgáltatást. A <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection standard</a> használatával védje szervezetét a DDOS-támadások három fő típusával:<br>A - <strong>térfogatmérő támadásokkal</strong> a hálózat legitim forgalommal elárasztható. DDoS Protection a standard csökkenti ezeket a támadásokat úgy, hogy automatikusan elnyeli vagy súrolja őket.<br>A - <strong>protokollok elleni támadások</strong> a 3. rétegbeli és a 4. rétegbeli protokollon alapuló gyengeségek kiaknázásával elérhetetlenné teszik a célt. DDoS Protection a standard csökkenti ezeket a támadásokat a kártékony forgalom blokkolásával.<br>Az - <strong>erőforrás (alkalmazás) réteg támadásokat</strong> céloz meg a webalkalmazások csomagjaiban. Védje ezt a típust egy webalkalmazási tűzfallal és DDoS Protection Szabványsal.</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection a standardot engedélyezni kell<br>- A tároló CPU-és memória-korlátozásait kényszeríteni kell (előzetes verzió)<br>- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Endpoint Protection engedélyezése (max. pontszám 2)</p></strong>Annak biztosítása érdekében, hogy a végpontok védve legyenek a kártevők ellen, a viselkedési érzékelők gyűjtik és dolgozzák fel az adatokat a végpontok operációs rendszereiből, és elküldik ezeket az adatokat a privát felhőbe elemzés céljából. A biztonsági elemzések nagy adatmennyiségeket, gépi tanulást és más forrásokat használhatnak a fenyegetésekre való reagálásra. A <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft DEFENDER ATP</a> például a fenyegetések felderítését használja a támadási módszerek azonosítására és a biztonsági riasztások előállítására.<br>Security Center a következő Endpoint Protection-megoldásokat támogatja: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, a Windowshoz készült McAfee v10, a Linux és a Sophos v9 for Linux. Ha Security Center észleli ezeket a megoldásokat, az Endpoint Protection telepítésére vonatkozó javaslat már nem fog megjelenni.</td>
    <td class="tg-lboi"; width=55%>- Az Endpoint Protection állapotával kapcsolatos hibákat a virtuálisgép-méretezési csoportokban kell szervizelni<br>- Az Endpoint Protection állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni<br>- Endpoint Protection-megoldás telepítése virtuális gépeken<br>- A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>- A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>- A figyelési ügynököt telepíteni kell a gépekre<br>- A figyelési ügynököt telepíteni kell a virtuális gépekre<br>- Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>- Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>- Endpoint Protection-megoldás telepítése a számítógépekre</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Naplózás és naplózás engedélyezése (max. pontszám 1)</p></strong>A naplózási adat a múltbeli problémák elemzését biztosítja, megelőzheti a lehetséges problémákat, javíthatja az alkalmazások teljesítményét, és automatizálhatja azokat a műveleteket, amelyek egyébként manuálisan történnek.<br>A - <strong>vezérlési és felügyeleti naplók</strong> a <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> műveletekkel kapcsolatos információkat biztosítanak.<br>Az - <strong>adatsíkok naplói</strong> az Azure-erőforrások használatának részeként kiváltott eseményekről nyújtanak információt.<br>A - <strong>feldolgozott események</strong> információt nyújtanak a feldolgozott elemzett eseményekről/riasztásokról.</td>
    <td class="tg-lboi"; width=55%>- Az SQL Server naplózását engedélyezni kell<br>- A App Services lévő diagnosztikai naplókat engedélyezni kell<br>- A Azure Data Lake Store lévő diagnosztikai naplókat engedélyezni kell<br>- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell<br>- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell<br>- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell<br>- Az Event hub diagnosztikai naplóit engedélyezni kell<br>- A IoT Hub lévő diagnosztikai naplókat engedélyezni kell<br>- A Key Vault lévő diagnosztikai naplókat engedélyezni kell<br>- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell<br>- A keresési szolgáltatásban engedélyezni kell a diagnosztikai naplókat.<br>- A Service Bus lévő diagnosztikai naplókat engedélyezni kell<br>- A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell<br>- A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni<br>- Az SQL naplózási beállításainak Action-Groups konfigurálva kell lennie a kritikus tevékenységek rögzítéséhez<br>- Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Komplex veszélyforrások elleni védelem engedélyezése (0 pontszám max.)</p></strong>Azure Security Center opcionális Azure Defender Threat Protection-csomagok átfogó védelmet biztosítanak a környezet számára. Ha Security Center fenyegetést észlel a környezet bármely területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt szervizelési lépéseket, valamint bizonyos esetekben a logikai alkalmazások válaszként való aktiválásának lehetőségét.<br>Minden Azure Defender-csomag egy különálló, opcionális ajánlat, amelyet a jelen biztonsági ellenőrzéshez kapcsolódó javaslat alapján engedélyezhet.<br><a href="/azure/security-center/threat-protection">További információ a veszélyforrások elleni védelemről Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- A speciális adatbiztonságot engedélyezni kell Azure SQL Database-kiszolgálókon<br>- A speciális adatbiztonságot engedélyezni kell a gépeken futó SQL-kiszolgálókon<br>- A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Virtual Machines<br>- A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure App Service-csomagokon<br>- Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Storage-fiókokban<br>- Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Kubernetes Service-fürtökön<br>- A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Container Registry-jegyzékeken<br>- A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Key Vault-tárolón</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ajánlott biztonsági eljárások implementálása (max. pontszám 0)</p></strong>Modern biztonsági eljárások "a hálózati peremhálózat megsértésének feltételezése". Emiatt a szabályozás számos ajánlott eljárása az identitások kezelésére koncentrál.<br>A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> védi a kulcsokat és a titkos kulcsokat a kulcsok, a. pfx fájlok és a jelszavak titkosításával.<br>A virtuális magánhálózatok (VPN) biztonságos módon férhetnek hozzá a virtuális gépekhez. Ha a VPN-ek nem érhetők el, használjon összetett hozzáférési kódot és kétfaktoros hitelesítést, például az <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD multi-Factor Authentication</a>. A kétfaktoros hitelesítés megakadályozza, hogy a rendszer csak a felhasználóneveket és a jelszavakat használja.<br>Az erős hitelesítési és engedélyezési platformok használata egy másik ajánlott eljárás. Az összevont identitások használata lehetővé teszi, hogy a szervezetek delegálják a meghatalmazott identitások kezelését. Ez akkor is fontos, ha az alkalmazottak le vannak zárva, és a hozzáférését vissza kell vonni.</td>
    <td class="tg-lboi"; width=55%>- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni<br>- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon<br>- A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell<br>- A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből<br>- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni<br>- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon<br>- Az Event hub-példány engedélyezési szabályait definiálni kell<br>- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra<br>- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra<br>- Az alhálózatokat hálózati biztonsági csoporttal kell társítani<br>- Előnézet A Windows Exploit Guard-nek engedélyezve kell lennie <br>- Előnézet Telepíteni kell a vendég konfigurációs ügynököt<br>- A nem internetkapcsolattal rendelkező virtuális gépeket hálózati biztonsági csoportokkal kell védeni<br>- Azure Backup engedélyezni kell a virtuális gépeket<br>- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB<br>- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL<br>- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL<br>- A PHP-t az API-alkalmazás legújabb verziójára kell frissíteni<br>- A PHP-t a webalkalmazás legújabb verziójára kell frissíteni<br>- A Java-t az API-alkalmazás legújabb verziójára kell frissíteni<br>- A Java-t a Function app legújabb verziójára kell frissíteni<br>- A Java-t a webalkalmazás legújabb verziójára kell frissíteni<br>- A Pythont az API-alkalmazás legújabb verziójára kell frissíteni<br>- A Pythont a Function alkalmazás legújabb verziójára kell frissíteni<br>- A Pythont a webalkalmazás legújabb verziójára kell frissíteni<br>- Az SQL serverek naplózási megőrzését legalább 90 napra kell beállítani<br>- A webalkalmazásoknak SSL-tanúsítványt kell igényelnie minden bejövő kérelemhez</td>
  </tr>
</tbody>
</table>


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