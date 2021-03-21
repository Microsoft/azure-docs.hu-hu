---
title: Azure CDN használata a CORS használatával | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Content Delivery Network (CDN) szolgáltatást a több eredetű erőforrás-megosztás (CORS) használatával.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018647"
---
# <a name="using-azure-cdn-with-cors"></a>Azure CDN használata a CORS
## <a name="what-is-cors"></a>Mi az a CORS?
A CORS (eltérő eredetű erőforrások megosztása) egy olyan HTTP-szolgáltatás, amely egy adott tartományban futó webalkalmazás számára teszi lehetővé, hogy hozzáférjen egy másik tartomány erőforrásaihoz. A helyek közötti parancsfájlok elleni támadások lehetőségének csökkentése érdekében minden modern webböngészővel [azonos eredetű](https://www.w3.org/Security/wiki/Same_Origin_Policy)biztonsági korlátozást valósíthat meg.  Ez megakadályozza, hogy egy weblap más tartományba tartozó API-kat hívjon fel.  A CORS lehetővé teszi, hogy egy forrás (a forrás tartomány) egy másik forrásban lévő API-kat hívjon fel.

## <a name="how-it-works"></a>Működés
Kétféle CORS-kérés, *egyszerű kérelem* és *összetett kérelem van.*

### <a name="for-simple-requests"></a>Egyszerű kérelmek esetén:

1. A böngésző egy további **forrás** HTTP-kérelem fejlécével küldi el a CORS kérelmet. Ennek a fejlécnek az értéke a szülő lapot kiszolgáló forrás, amely a *protokoll,* a *tartomány* és a port kombinációjával van meghatározva *.*  Ha egy https \: //www.contoso.com-oldal megpróbál hozzáférni egy felhasználói adatokhoz a fabrikam.com-forrásban, a rendszer a következő fabrikam.com küldi el:

   `Origin: https://www.contoso.com`

2. A kiszolgáló a következők bármelyikével válaszolhat:

   * Egy **hozzáférés-vezérlési engedélyezési-származási** fejléc a válaszban, amely azt jelzi, hogy melyik hely engedélyezett. Például:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Egy HTTP-hibakód, például 403, ha a kiszolgáló nem engedélyezi az átszármazási kérést a forrás fejlécének ellenőrzése után.

   * **Hozzáférés-vezérlési-engedélyezési-származási** fejléc olyan helyettesítő karakterrel, amely lehetővé teszi az összes eredetét:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Összetett kérelmek esetén:

Egy összetett kérelem egy CORS-kérelem, amelyben a böngészőnek el kell küldenie egy *elővizsgálati kérelmet* (azaz egy előzetes mintavételt) a tényleges CORS-kérelem elküldése előtt. Az elővizsgálati kérelem arra kéri a kiszolgáló engedélyét, hogy az eredeti CORS-kérelem folytatható, és `OPTIONS` ugyanarra az URL-címre irányuló kérést küld.

> [!TIP]
> A CORS és a gyakori buktatókkal kapcsolatos további részletekért tekintse meg a [CORS for REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)-k útmutatóját.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Helyettesítő karakteres vagy önálló eredetű forgatókönyvek
A CORS on Azure CDN automatikusan fog működni további konfiguráció nélkül, ha a **hozzáférés-vezérlés-engedélyezési-Origin** fejléc helyettesítő karakter (*) vagy egyetlen forrás.  A CDN gyorsítótárazza az első választ, és az azt követő kérelmek ugyanazt a fejlécet fogják használni.

Ha a CDN-kérelmeket már a CORS megelőzően beállították, akkor a tartalomnak a **hozzáférés-vezérlés – Allow-Origin** fejléctel való újratöltéséhez ki kell ürítenie a tartalmat a végpont tartalmában.

## <a name="multiple-origin-scenarios"></a>Több eredetű forgatókönyv
Ha engedélyezni szeretné, hogy egy adott lista a CORS számára engedélyezett legyen, a dolgok egy kicsit bonyolultabbak lesznek. A probléma akkor fordul elő, amikor a CDN gyorsítótárazza a **hozzáférés-vezérlés-engedélyezés-Origin** fejlécet az első CORS-forráshoz.  Ha egy másik CORS-forrás hajtja végre ezt a kérést, a CDN a gyorsítótárazott **hozzáférés-vezérlés-engedélyezési-Origin** fejlécet fogja kiszolgálni, amely nem egyezik.  Ezt többféleképpen is kijavítani.

### <a name="azure-cdn-standard-profiles"></a>Szabványos profilok Azure CDN
A Microsoft Azure CDN standard verziójában létrehozhat egy szabályt a [standard szabályok motorban](cdn-standard-rules-engine-reference.md) a kérelemben található **forrás** fejléc vizsgálatához. Ha érvényes a forrás, a szabály a **hozzáférés-vezérlés – engedélyezés – forrás** fejlécet fogja beállítani a kívánt értékkel. Ebben az esetben a rendszer figyelmen kívül hagyja a fájl forrás-kiszolgálójának **hozzáférés-vezérlés-engedélyezés-eredet** fejlécét, és a CDN-szabályok motorja teljes mértékben felügyeli az engedélyezett CORS-eredeteket.

![Szabályok példa a Standard Rules Engine-re](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> További műveleteket is hozzáadhat a szabályhoz a további válaszok fejlécek, például a **hozzáférés-vezérlés-engedélyezés-metódusok** módosításához.
> 

A **Akamai Azure CDN standard** esetében az egyetlen olyan mechanizmus, amely lehetővé teszi, hogy a helyettesítő karakterek használata nélkül több kezdőpontot engedélyezzen a [lekérdezési karakterlánc gyorsítótárazásának](cdn-query-string.md)használata. Engedélyezze a CDN-végpont lekérdezési karakterlánc beállítását, majd használjon egy egyedi lekérdezési karakterláncot az egyes engedélyezett tartományokból érkező kérésekhez. Ennek eredményeképpen a CDN gyorsítótárazása külön objektumot ad meg minden egyes egyedi lekérdezési karakterlánchoz. Ez a megközelítés nem ideális, mivel ez a fájl több másolatát is eredményezi a CDN-gyorsítótárban.  

### <a name="azure-cdn-premium-from-verizon"></a>Prémium Azure CDN a Verizontól
A Verizon Premium Rules Engine használatával [létre kell hoznia egy szabályt](./cdn-verizon-premium-rules-engine.md) a kérelemben szereplő **forrás** fejléc vizsgálatához.  Ha érvényes a forrás, a szabály az **Access-Control-Allow-Origin** fejlécet fogja beállítani a kérelemben megadott forrással.  Ha **a forrás fejlécben** megadott forrás nem engedélyezett, a szabálynak el kell hagynia a **hozzáférés-vezérlés-engedélyezés-eredet** fejlécet, ami miatt a böngésző elutasítja a kérést. 

Ezt kétféleképpen teheti meg a prémium szabályok motorral. Mindkét esetben a rendszer figyelmen kívül hagyja a fájl forráskiszolgáló **hozzáférés-vezérlés-engedélyezés-eredet** fejlécét, és a CDN-szabályok motorja teljes mértékben felügyeli az engedélyezett CORS-eredeteket.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Egy reguláris kifejezés az összes érvényes eredetgel
Ebben az esetben hozzon létre egy reguláris kifejezést, amely tartalmazza az összes engedélyezni kívánt eredetet: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> A **Verizon Azure CDN Premium a Perl-** [kompatibilis reguláris kifejezéseket](https://pcre.org/) használja a reguláris kifejezések motorjának.  A reguláris kifejezéseket például az [101](https://regex101.com/) -as reguláris kifejezésekkel ellenőrizheti.  Vegye figyelembe, hogy a "/" karakter a reguláris kifejezésekben érvényes, ezért nem kell megszöknie, azonban az adott karakter kimaradása ajánlott eljárásnak minősül, és néhány regex-érvényesítő által várt.
> 
> 

Ha a reguláris kifejezés megfelel, a szabály a forrástól a kérést küldő forrásból származó **hozzáférés-vezérlési engedélyezési-származási** fejlécet (ha van) lecseréli.  További CORS-fejléceket is hozzáadhat, például a **hozzáférés-vezérlés-engedélyezési metódusokat**.

![Szabályok – példa reguláris kifejezéssel](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Kérelem fejlécének szabálya az egyes forrásokhoz.
A reguláris kifejezések helyett külön szabályt hozhat létre minden olyan forráshoz, amelyet engedélyezni szeretne a **kérelem fejlécének helyettesítő karakteres** [egyeztetési feltételének](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)használatával. A reguláris kifejezési módszerhez hasonlóan a szabályok motorja is beállítja a CORS fejléceit. 

![Szabályok – példa reguláris kifejezés nélkül](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> A fenti példában a helyettesítő karakter használata * megadja a szabályok motorjának a HTTP és a HTTPS egyeztetését.
> 
>