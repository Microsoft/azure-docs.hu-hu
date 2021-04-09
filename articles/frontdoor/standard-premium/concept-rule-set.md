---
title: 'Azure-beli bejárati ajtó: szabálykészlet'
description: Ez a cikk áttekintést nyújt az Azure bejárati standard/Premium Rules set szolgáltatásról.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099151"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Mi az Azure bejárati ajtó standard/Premium (előzetes verzió) szabályainak beállítása?

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

A szabálykészlet egy testreszabott szabályrendszer, amely a szabályok kombinációját egyetlen készletbe csoportosítja, amely több útvonalhoz is társítható. A szabálykészlet segítségével testre szabhatja a kérések feldolgozásának folyamatát, és azt, hogy az Azure bejárati ajtó hogyan kezeli ezeket a kéréseket.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Gyakori támogatott forgatókönyvek

* A biztonsági fejlécek alkalmazásával megakadályozhatja a böngészőalapú biztonsági réseket, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy, az X-frame-Options és a hozzáférés-vezérlés-engedélyezési-Origin fejléceket a különböző eredetű erőforrás-megosztási (CORS) forgatókönyvekhez. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.

* A kérések továbbítása az alkalmazás mobil vagy asztali verziójára az ügyfél-eszköz típusa alapján.

* Az átirányítási képességekkel 301, 302, 307 és 308 értékre térhet vissza, és átirányítja az ügyfelet az új állomásnévre, elérési utakra, lekérdezési karakterláncokra vagy protokollokra.

* Dinamikusan módosítja az útvonal gyorsítótárazási konfigurációját a bejövő kérelmek alapján.

* Írja át újra a kérelem URL-címét, és továbbítja a kérést a megfelelő forrásnak a konfigurált Origin csoportba.

* Kérés/válasz fejléc hozzáadása, módosítása vagy eltávolítása a bizalmas információk elrejtéséhez vagy a fontos információk a fejléceken keresztüli rögzítéséhez.

* A támogatási kiszolgáló változói a kérés/válasz fejlécek vagy URL-írási útvonalak/lekérdezési karakterláncok dinamikus módosítására szolgálnak, például új oldal betöltésekor vagy űrlap közzétételekor. A kiszolgálói változó jelenleg csak **[szabálykészlet műveletekben](concept-rule-set-actions.md)** támogatott.

## <a name="architecture"></a>Architektúra

A szabálykészlet kezeli a kérelmeket az Edge-ben. Amikor egy kérés érkezik az Azure bejárati ajtó standard/prémium végpontján, a rendszer először a WAF hajtja végre, amelyet az útvonalon konfigurált beállítások követnek. Ezek a beállítások tartalmazzák az útvonalhoz társított szabálykészlet körét. A szabálykészlet az útvonalon felülről lefelé haladva lesz feldolgozva. Ugyanez vonatkozik a szabálykészlet szabályaira is. Az egyes szabályokban végrehajtott összes művelet végrehajtásához az adott szabályban található összes egyezési feltételnek teljesülnie kell. Ha egy kérelem nem felel meg a szabálykészlet konfigurációjának egyik feltételének sem, akkor a rendszer csak az útvonal konfigurációit fogja végrehajtani.

Ha a **fennmaradó szabályok kiértékelésének leállítása** lehetőség be van jelölve, akkor az útvonalhoz társított összes fennmaradó szabálykészlet nem lesz végrehajtva.  

### <a name="example"></a>Példa

A következő ábrán az WAF-szabályzatok a végrehajtás első lépései. Egy szabálykészlet a válasz fejlécének hozzáfűzésére van konfigurálva. Ezután a fejléc megváltoztatja a gyorsítótár-vezérlő maximális életkorát, ha az egyeztetési feltétel teljesül.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="A szabálykészlet architektúráját ábrázoló diagram." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminológia

Az Azure-beli bejárati szabály beállításával létrehozhat egy szabálykészlet-konfiguráció kombinációját, amely a szabályok egy halmaza. A következő kimeneti sorok néhány hasznos terminológiát mutatnak be a szabálykészlet konfigurálásakor.

További kvóta esetén tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Szabálykészlet: egy* vagy több [útvonalhoz](concept-route.md)társított szabályok halmaza. Mindegyik konfiguráció 25 szabályra van korlátozva. Akár 10 konfigurációt is létrehozhat.

* *Szabályok beállítása szabály*: egy legfeljebb 10 egyeztetési feltételből és 5 műveletből álló szabály. A szabályok helyiek egy szabálykészlet számára, és nem exportálhatók a szabálykészlet között. A felhasználók több szabálykészlet esetében is létrehozhatják ugyanazt a szabályt.

* *Egyeztetési feltétel*: a beérkező kérések elemzéséhez számos egyezési feltételt lehet használni. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltételek a **és** a operátorral vannak kiértékelve. *A reguláris kifejezés feltételek esetén támogatott*. Az egyeztetési feltételek teljes listája megtalálható a [szabálykészlet feltételében](concept-rule-set-match-conditions.md).

* *Művelet*: a műveletek azt írják le, hogy a AFD hogyan kezeli a beérkező kéréseket a megfelelő feltételek alapján. Módosíthatja a gyorsítótárazási viselkedéseket, módosíthatja a kérések fejléceit/válaszait, valamint az URL-újraírást és az URL-átirányítást. *A kiszolgálói változók működés közben támogatottak*. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. A műveletek teljes listája megtalálhatja a [szabálykészlet műveleteit](concept-rule-set-actions.md).

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
* Megtudhatja, hogyan konfigurálhatja az első [szabálykészlet](how-to-configure-rule-set.md)beállítását.
 
