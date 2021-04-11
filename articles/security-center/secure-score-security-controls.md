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
ms.openlocfilehash: 2f5d98dd9bf893065f2bf9c37cbec4384d0f7c94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727140"
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


> [!div class="nextstepaction"]
> [A biztonságos pontszám elérése és nyomon követése](secure-score-access-and-track.md)