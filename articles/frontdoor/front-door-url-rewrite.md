---
title: Azure bejárati ajtó – URL-cím újraírása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure bevezető ajtaja hogyan írja le az útvonalak URL-címét, ha be van állítva.
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91445488"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-átírás (egyéni továbbítási útvonal)
Az Azure bevezető ajtaja támogatja az URL-átírást úgy, hogy egy opcionális **Egyéni továbbítási útvonalat** konfigurál, amelyet a rendszer a háttérbe való továbbításra irányuló kérés összeállításakor használ Alapértelmezés szerint, ha nincs megadva egyéni továbbítási útvonal, a bejárati ajtó a továbbított kérelemben használt URL-címhez másolja a bejövő URL-útvonalat. A továbbított kérelemben használt állomásfejléc megegyezik a kiválasztott háttérrendszer számára konfigurált állomásfejléccel. Olvassa el a [háttérbeli állomásfejléc-fejlécet](front-door-backend-pool.md#hostheader) , hogy megtudja, mit csinál, és hogyan konfigurálhatja.

Az URL-cím újraírásának hatékony része az, hogy az egyéni továbbítási útvonal a bejövő elérési út bármely olyan részét másolja, amely a helyettesítő karakteres elérési útra illeszkedik a továbbított útvonalhoz (ezek az elérésiút-szegmensek az alábbi példában szereplő **zöld** szegmensek):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure bejárati ajtó URL-címének újraírása":::

## <a name="url-rewrite-example"></a>URL-átírási példa
Tekintse át az útválasztási szabályokat a következő, az előtér-gazdagépek és-elérési utak kombinációjával:

| Hosts      | Elérési utak       |
|------------|-------------|
| www- \. contoso.com | /\*   |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

Az alábbi táblázat első oszlopa a beérkező kérelmekre mutat példákat, a második oszlop pedig azt mutatja be, hogy mi lenne a "legpontosabban egyező" útvonal.  A táblázat harmadik és az azt követő oszlopai a konfigurált **Egyéni továbbítási útvonalakra** mutatnak példákat.

Ha például beolvasjuk a második sort, azt mondja, hogy a bejövő kérelem esetében, `www.contoso.com/sub` Ha az egyéni továbbítási útvonal volt `/` , akkor a továbbított elérési út a következő lesz: `/sub` . Ha az egyéni továbbítási útvonal volt `/fwd/` , akkor a továbbított elérési út a következő lesz: `/fwd/sub` . És így tovább, a fennmaradó oszlopokhoz. Az alábbi elérési utak **Kiemelt** részei a helyettesítő karakteres egyezés részét képező részeket jelölik.

| Bejövő kérelem       | A legpontosabb egyezési elérési út | /          | fwd          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www- \. contoso.com/            | /\*                      | /          | fwd          | foo          | /foo/bar/          |
| www \. contoso.com/**Sub**     | /\*                      | /**Sub**   | /fwd/**Sub**   | /foo/**Sub**   | /foo/Bar/**Sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/Bar/**a/b/c** |
| www- \. contoso.com/foo         | /foo                     | /          | fwd          | foo          | /foo/bar/          |
| www- \. contoso.com/foo/        | foo\*                  | /          | fwd          | foo          | /foo/bar/          |
| www \. contoso.com/foo/**sáv** | foo\*                  | /**Bár**   | /fwd/**sáv**   | /foo/**sáv**   | /foo/Bar/**sáv**   |

## <a name="optional-settings"></a>Választható beállítások
További választható beállítások is megadhatók a megadott útválasztási szabályok beállításaihoz:

* **Gyorsítótár-konfiguráció** – ha le van tiltva vagy nincs megadva, akkor az ezen útválasztási szabálynak megfelelő kérelmek nem kísérlik meg a gyorsítótárazott tartalom használatát, hanem mindig a háttérből fognak beolvasni. További információ a [bejárati ajtók gyorsítótárazásáról](front-door-caching.md).

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
