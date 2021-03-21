---
title: Mi az Azure bejárati ajtó standard/Premium útvonala?
description: Ebből a cikkből megtudhatja, hogy az Azure bejárati ajtó standard/Premium milyen útválasztási szabályt használ a bejövő kérésekhez.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101099199"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Mi az Azure bejárati ajtó standard/Premium (előzetes verzió) útvonala?

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárat standard/Premium útvonala határozza meg, hogy a rendszer hogyan kezelje a forgalmat, amikor a bejövő kérelem az Azure bejárati környezetében érkezik. Az útvonal beállításain keresztül a rendszer egy társítást határoz meg egy tartomány és egy háttérbeli származási csoport között. Ha bekapcsolja az előzetes funkciókat, például a minta a Mach-be, a szabálykészlet, a forgalom részletesebb szabályozása is elérhető.

A standard/prémium szintű útválasztási konfiguráció két fő részből áll: "bal oldali" és "jobb oldal". Az útvonal bal oldalán lévő beérkező kérést egyeztetjük, a jobb oldalon pedig a kérés feldolgozásának módját határozzák meg.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Bejövő egyezés (bal oldali)

A következő tulajdonságok határozzák meg, hogy a bejövő kérelem megfelel-e az útválasztási szabálynak (vagy bal oldali):

* **Http-protokollok** (HTTP/HTTPS)
* **Gazdagépek** (például www \. foo.com, \* . bar.com)
* **Elérési utak** (például/ \* ,/users/ \* ,/file.gif)

Ezek a tulajdonságok belsőleg vannak kibontva, így a protokoll/gazdagép/elérési út minden kombinációja egy lehetséges egyezési készlet.

### <a name="route-data-right-hand-side"></a>Route-adat (jobb oldalon)

A kérelem feldolgozásának döntése attól függ, hogy engedélyezve van-e a gyorsítótárazás, vagy sem az útvonalon. Ha a gyorsítótárazott válasz nem érhető el, a rendszer továbbítja a kérést a megfelelő háttérbe.

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

Az Azure Front Door standard/Premium értéke határozza meg az adott előtér-gazdagépet, és a lehetséges útválasztási szabályok szűrésével csak az adott előtér-gazdagép útvonalait használja. A bejárati ajtó ezután a kérés elérési útján szűri az útválasztási szabályokat. A frontend-gazdagépekhez hasonló logikát használunk:

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

Ha az Azure bejárati ajtót a standard/Premium egyetlen útválasztási szabálynak felel meg, akkor a kérelem feldolgozásának módját kell választania. Ha az Azure bejárati ajtó standard/prémium szintű gyorsítótárbeli válasza elérhető az egyeztetett útválasztási szabályhoz, akkor a rendszer visszaküldi a kérést az ügyfélnek. A következő lépés az Azure bejárati ajtó standard/Premium kiértékelése, hogy van-e beállítva szabály a megfeleltetett útválasztási szabályhoz. Ha nincs definiálva szabálykészlet, a kérést a rendszer a háttérbeli készletbe továbbítja. Ellenkező esetben a szabálykészlet a konfigurált sorrendben lesz végrehajtva.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
