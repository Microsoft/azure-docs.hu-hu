---
title: Az Azure app Configuration kulcs-érték tárolójának ismertetése
description: A kulcs-érték tároló ismertetése az Azure-alkalmazás konfigurációjában, amely kulcs-értékként tárolja a konfigurációs adatait. A Key-Values az Alkalmazásbeállítások ábrázolása.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930245"
---
# <a name="keys-and-values"></a>Kulcsok és értékek

Az Azure-alkalmazás konfigurációja kulcs-értékként tárolja a konfigurációs adatait. A kulcs-érték a fejlesztők által használt alkalmazások beállításainak egyszerű és rugalmas ábrázolása.

## <a name="keys"></a>Kulcsok

A kulcsok azonosítóként szolgálnak a kulcs-értékekhez, és a megfelelő értékek tárolására és lekérésére használatosak. Gyakori eljárás a kulcsok hierarchikus névtérbe rendezése egy karakter elválasztójának használatával, például `/` vagy `:` . Az alkalmazáshoz legjobban illeszkedő konvenciót használjon. Az alkalmazás konfigurációja a kulcsokat teljes egészében kezeli. Nem elemzi a kulcsokat, hogy megtudja, hogyan épülnek fel a nevük, és hogyan kényszerítenek rájuk.

Íme egy példa a hierarchiában a Komponensszolgáltatások alapján strukturált kulcsok nevére:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

A konfigurációs adatok alkalmazás-keretrendszereken belüli használata bizonyos elnevezési sémákat határozhat meg a kulcs-értékekhez. A Java Spring Cloud Framework például olyan erőforrásokat határoz meg, `Environment` amelyek a Spring Application beállításait adják meg.  Ezek az *alkalmazás nevét* és *profilját* tartalmazó változók paraméterei. A tavaszi felhőhöz kapcsolódó konfigurációs adatok kulcsai általában a két elem elválasztó karakterrel kezdődnek.

Az alkalmazás konfigurációjában tárolt kulcsok kis-és nagybetűket megkülönböztető, Unicode-alapú karakterláncok. Az *App1* és a *App1* kulcsok eltérnek az alkalmazás konfigurációs tárolójában. Ne feledje, hogy ha a konfigurációs beállításokat egy alkalmazáson belül használja, mert egyes keretrendszerek kis-és nagybetűk nélkül kezelik a konfigurációs kulcsokat. A kulcsok megkülönböztetésére nem ajánlott a Case használata.

Bármilyen Unicode-karaktert használhat a kulcsok neveiben, kivéve a következőt: `%` . A kulcs neve nem lehet `.` vagy `..` sem. A kulcs-érték együttes mérete legfeljebb 10 KB lehet. Ez a korlát a kulcs, a hozzá tartozó érték és az összes hozzá tartozó opcionális attribútum összes karakterét tartalmazza. Ezen a korláton belül számos hierarchikus szintet is megadhat a kulcsokhoz.

### <a name="design-key-namespaces"></a>Tervezési kulcs névterei

A konfigurációs adataihoz használt kulcsok elnevezésének két általános megközelítése van: Flat vagy hierarchikus. Ezek a módszerek hasonlóak az alkalmazás használati szempontjából, de a hierarchikus elnevezés számos előnyt kínál:

* Könnyebben olvasható. A hierarchikus kulcs nevének határolói a mondatokban szóközökként működnek. Természetes szüneteket is biztosítanak a szavak között.
* Könnyebben kezelhető. A kulcsnév-hierarchia a konfigurációs adategységek logikai csoportjait jelöli.
* Könnyebben használható. Egyszerűbb olyan lekérdezést írni, amely egy hierarchikus struktúra kulcsaira illeszkedik, és csak a konfigurációs adat egy részét kéri le. Emellett számos újabb programozási keretrendszer natív módon támogatja a hierarchikus konfigurációs adatokat, például hogy az alkalmazás képes legyen a különböző konfigurációs beállítások használatára.

Az alkalmazások konfigurációjában többféleképpen rendezheti a kulcsokat. Tekintse át ezeket a kulcsokat [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)-ként. Minden hierarchikus kulcs egy olyan erőforrás *elérési útja* , amely egy vagy több olyan összetevőből áll, amelyek elhatárolók egymáshoz csatlakoznak. Válassza ki az alkalmazás, a programozási nyelv vagy a keretrendszer igényei alapján elválasztóként használandó karaktert. Az alkalmazás konfigurációjában több határolójel is használható a különböző kulcsokhoz.

### <a name="label-keys"></a>Címkék kulcsai

Az alkalmazás konfigurációjának kulcs-értékeit opcionálisan címkéző attribútummal is elvégezheti. A címkék használatával megkülönböztethető a kulcs-érték ugyanazzal a kulccsal. Az *a* és *B* címkével ellátott *App1* két különálló kulcsot alkotnak az alkalmazás konfigurációs tárolójában. Alapértelmezés szerint a kulcs-érték nem rendelkezik címkével. Ha címkével nem rendelkező kulcsot szeretne explicit módon hivatkozni, használja a `\0` (URL-cím `%00` ) értéket.

A Label kényelmes módszert biztosít a kulcsok változatának létrehozásához. A címkék gyakori használata több környezet megadására szolgál ugyanahhoz a kulcshoz:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Verzió kulcs-értékei

Használjon címkéket úgy, hogy a kulcs-érték több verzióját hozza létre. Megadhat például egy alkalmazás verziószámát vagy egy git commit ID-t a címkékben az adott szoftveres buildhez társított kulcs-értékek azonosításához.

> [!NOTE]
> Ha változási verziókat keres, az alkalmazás konfigurációja megtartja a kulcs-érték összes módosítását az elmúlt bizonyos időszakban automatikusan. További részletekért tekintse meg az [időponthoz kapcsolódó pillanatképet](./concept-point-time-snapshot.md) .

### <a name="query-key-values"></a>Lekérdezési kulcs – értékek

Minden kulcs-érték egyedileg azonosítható a kulcsa és a címkéje, amely lehet `\0` . Egy alkalmazás-konfigurációs tárolót kérdez le a kulcs-értékekhez egy minta megadásával. Az alkalmazás konfigurációs tárolója visszaadja az összes olyan kulcs-értéket, amely megfelel a mintának, valamint a hozzájuk tartozó értékeknek és attribútumoknak. Az alkalmazás konfigurálásához REST API hívásokban használja az alábbi főbb mintákat:

| Kulcs | Leírás |
|---|---|
| `key` nincs megadva vagy `key=*` | Az összes kulcs egyezése |
| `key=abc` | Pontosan megfelel az **ABC** -kulcs nevének |
| `key=abc*` | Az **ABC** -vel kezdődő kulcsok neveinek felel meg |
| `key=abc,xyz` | Az **ABC** vagy az **XYZ** kulcsnévnek felel meg. Legfeljebb öt CSV |

A következő címke mintákat is tartalmazhatja:

| Címke | Description |
|---|---|
| `label` nincs megadva vagy `label=*` | Megfelel bármely címkének, amely tartalmazza a következőket `\0` |
| `label=%00` | Egyezések `\0` címkéje |
| `label=1.0.0` | Pontosan megfelel a Label **1.0.0** -nak |
| `label=1.0.*` | Az 1,0 karakterrel kezdődő címkéket tartalmazza **.** |
| `label=%00,1.0.0` | A címkékre `\0` vagy a **1.0.0**-ra korlátozódik, legfeljebb öt CSV |

> [!NOTE]
> `*`a, a `,` és `\` a a lekérdezésekben foglalt karakterek. Ha foglalt karaktert használ a kulcsok neveiben vagy címkéjén, el kell menekülnie a `\{Reserved Character}` lekérdezésekben.

## <a name="values"></a>Értékek

A kulcsokhoz rendelt értékek szintén Unicode karakterláncok. Az értékekhez az összes Unicode-karakter használható.

### <a name="use-content-type"></a>Tartalom típusának használata
Az alkalmazás konfigurációjában minden kulcs-érték egy Content-Type attribútummal rendelkezik. Ezt az attribútumot igény szerint tárolhatja egy olyan kulcs-érték típusú értékről, amely segít az alkalmazásnak megfelelően feldolgozni. Bármilyen formátumot használhat a Content-Type típushoz. Az alkalmazás konfigurációja [adathordozó-típusokat]( https://www.iana.org/assignments/media-types/media-types.xhtml) (más néven MIME-típusokat) használ a beépített adattípusokhoz, például a szolgáltatás-jelzők, a Key Vault referenciák és a JSON-kulcsok értékeit.

## <a name="next-steps"></a>Következő lépések

* [Adott időpontban készült pillanatképek](./concept-point-time-snapshot.md)
* [Funkciókezelés](./concept-feature-management.md)
* [Eseménykezelés](./concept-app-configuration-event.md)
