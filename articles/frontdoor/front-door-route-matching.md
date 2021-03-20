---
title: Azure bejárati ajtó – útválasztási szabály egyeztetése a figyeléssel | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure-beli bejárati ajtó hogyan illeszkedik a bejövő kérelmekhez használt útválasztási szabályokhoz
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449208"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>A kérések egyeztetése egy útválasztási szabállyal

A kapcsolat létesítése és a TLS-kézfogás elvégzése után, amikor egy kérelem a bejárati ajtón lévő egyik első dologra vonatkozik, a bejárati ajtón eldöntheti, hogy melyik útválasztási szabály felel meg a kérelemnek, majd végrehajtja a megadott műveletet a konfigurációban. Az alábbi dokumentum ismerteti, hogyan határozza meg, hogy a bejárati ajtó melyik útválasztási konfigurációt használja a HTTP-kérések feldolgozásához.

## <a name="structure-of-a-front-door-route-configuration"></a>Az előtérben lévő útvonal konfigurációjának szerkezete
A bevezető ajtó útválasztási szabályának konfigurálása két fő részből áll: egy "bal oldali" és "jobb oldal". A bejövő kérelem az útvonal bal oldalán található, míg a jobb oldalon a kérés feldolgozásának módját határozzák meg.

### <a name="incoming-match-left-hand-side"></a>Bejövő egyezés (bal oldali)
A következő tulajdonságok határozzák meg, hogy a bejövő kérelem megfelel-e az útválasztási szabálynak (vagy bal oldali):

* **Http-protokollok** (HTTP/HTTPS)
* **Gazdagépek** (például www \. foo.com, \* . bar.com)
* **Elérési utak** (például/ \* ,/users/ \* ,/file.gif)

Ezek a tulajdonságok belsőleg vannak kibontva, így a protokoll/gazdagép/elérési út minden kombinációja egy lehetséges egyezési készlet.

### <a name="route-data-right-hand-side"></a>Route-adat (jobb oldalon)
A kérelem feldolgozásának döntése attól függ, hogy engedélyezve van-e a gyorsítótárazás, vagy sem az adott útvonalon. Ha tehát nincs gyorsítótárazott válasz a kéréshez, a rendszer továbbítja a kérést a megfelelő háttérbe a konfigurált háttérrendszer-készletben.

## <a name="route-matching"></a>Útvonal egyeztetése
Ez a szakasz az adott bejárati ajtó útválasztási szabályának megfelelő módon fog foglalkozni. Az alapszintű koncepció az, hogy mindig a legpontosabb **egyezésnek** felelnek meg, amelyet először csak a "bal oldali" oldalon tekintünk.  Először a HTTP protokoll, majd a előtér-gazdagép, majd az elérési út alapján egyeztetünk.

### <a name="frontend-host-matching"></a>Előtér-gazdagép egyeztetése
A frontend-gazdagépek egyeztetése során az alábbi logikát használjuk:

1. Keresse meg a gazdagép pontos egyezését tartalmazó útválasztást.
2. Ha nincs pontosan megfelelő előtér-gazdagép, utasítsa el a kérést, és küldjön egy 400 hibás kérési hibát.

A folyamat további ismertetéséhez tekintse meg az első ajtós útvonalak konfigurációját (csak bal oldalon):

| Útválasztási szabály | Előtér-gazdagépek | Elérési út |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users/\* |
| C | www \. fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Ha a következő bejövő kérések a bejárati ajtóhoz lettek küldve, akkor a fenti útválasztási szabályoknak megfelelőek lesznek a következők:

| Bejövő előtér-gazdagép | Egyeztetett útválasztási szabály (ok) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www- \. fabrikam.com | C |
| images.fabrikam.com | 400-es hiba: hibás kérelem |
| foo.adventure-works.com | C |
| contoso.com | 400-es hiba: hibás kérelem |
| www- \. Adventure-Works.com | 400-es hiba: hibás kérelem |
| www- \. northwindtraders.com | 400-es hiba: hibás kérelem |

### <a name="path-matching"></a>Elérési út megfeleltetése
Miután meghatározta az adott előtér-gazdagépet, és a lehetséges útválasztási szabályok szűrését csak az adott előtér-gazdagéphez tartozó útvonalakra szűri, a bejárati ajtó ezután a kérés útvonalán alapuló útválasztási szabályokat szűri. A frontend-gazdagépekhez hasonló logikát használunk:

1. Keresse meg az útvonal pontos egyezését tartalmazó útválasztási szabályt
2. Ha nincs pontos egyezési útvonal, keressen olyan útválasztási szabályokat, amelyek megfelelnek a következő helyettesítő karaktereknek:
3. Ha nem található egyező elérési úttal rendelkező útválasztási szabály, akkor utasítsa el a kérelmet, és adja vissza a 400-es hibát: hibás kérelem HTTP-válasza.

>[!NOTE]
> A helyettesítő karakterek nélküli elérési utak pontos egyezési elérési utaknak tekintendők. Még akkor is, ha az elérési út perjelben ér véget, a pontos egyezésnek számít.

További részletekért tekintse meg a következő példát:

| Útválasztási szabály | Előtér-gazdagép    | Elérési út     |
|-------|---------|----------|
| A     | www- \. contoso.com | /        |
| B     | www- \. contoso.com | /\*      |
| C     | www- \. contoso.com | /ab      |
| T     | www- \. contoso.com | /abc     |
| E     | www- \. contoso.com | ABC    |
| F     | www- \. contoso.com | ABC\*  |
| G     | www- \. contoso.com | /abc/def |
| H     | www- \. contoso.com | /Path   |

A konfigurációtól függően a következő példában szereplő táblázat eredménye:

| Bejövő kérelem    | Egyeztetett útvonal |
|---------------------|---------------|
| www- \. contoso.com/            | A             |
| www- \. contoso.com/a           | B             |
| www- \. contoso.com/AB          | C             |
| www- \. contoso.com/ABC         | T             |
| www- \. contoso.com/abzzz       | B             |
| www- \. contoso.com/ABC/        | E             |
| www- \. contoso.com/ABC/d       | F             |
| www- \. contoso.com/ABC/def     | G             |
| www- \. contoso.com/ABC/defzzz  | F             |
| www- \. contoso.com/ABC/def/GHI | F             |
| www- \. contoso.com/Path        | B             |
| www- \. contoso.com/Path/       | H             |
| www- \. contoso.com/Path/ZZZ    | B             |

>[!WARNING]
> </br> Ha a pontos egyezést biztosító előtér-gazdagéphez nem tartoznak útválasztási szabályok, az összes útvonal útvonala ( `/*` ), akkor nem lesz egyezés egyetlen útválasztási szabályhoz sem.
>
> Példa konfiguráció:
>
> | Útvonal | Gazdagép             | Elérési út    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API\* |
>
> Egyező tábla:
>
> | Bejövő kérelem       | Egyeztetett útvonal |
> |------------------------|---------------|
> | profile.domain.com/other | Nincsenek. 400-es hiba: hibás kérelem |

### <a name="routing-decision"></a>Útválasztási döntés
Miután egyeztetett egy bejárati ajtó útválasztási szabályával, ki kell választania, hogyan kell feldolgozni a kérést. Ha az egyeztetett útválasztási szabályhoz a bejárati ajtón van egy gyorsítótárazott válasz, akkor ugyanazt a rendszer visszaküldi az ügyfélnek. Ellenkező esetben a következő dolog lesz kiértékelve, hogy beállította-e az [URL-újraírást (egyéni továbbítási útvonalat)](front-door-url-rewrite.md) a megfeleltetett útválasztási szabályhoz. Ha nincs definiálva egyéni továbbítási útvonal, akkor a kérés a megfelelő háttérbe kerül a konfigurált háttérrendszer-készletben. Más esetben a kérés elérési útja a megadott [Egyéni továbbítási útvonalon](front-door-url-rewrite.md) , majd a háttér felé továbbítva lesz frissítve.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
