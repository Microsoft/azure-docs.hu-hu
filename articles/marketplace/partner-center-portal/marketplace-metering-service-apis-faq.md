---
title: Mérési szolgáltatás API-jai – gyakori kérdések – Microsoft kereskedelmi piactér
description: Gyakori kérdések a Microsoft AppSource és az Azure Marketplace-en található SaaS-ajánlatokkal kapcsolatos mérési szolgáltatás API-król.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320000"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace mért számlázási API-k – GYIK

Ha az ügyfél egy SaaS-szolgáltatásra, vagy egy felügyelt alkalmazási csomaggal rendelkező Azure-alkalmazásra előfizet, a díjszabással ellátott számlázással nyomon követheti a felhasznált számlázási dimenziók felhasználását.  Ha a felhasználás meghaladja az ügyfél által kiválasztott kifejezéshez beállított belefoglalt mennyiségeket, a szolgáltatás használati eseményeket bocsát ki a Microsoftnak.

## <a name="for-both-saas-offers-and-managed-apps"></a>SaaS-ajánlatok és felügyelt alkalmazások esetén is

### <a name="how-often-is-it-expected-to-emit-usage"></a>Milyen gyakran várható a használat kibocsátása?

Ideális esetben a használatot minden órában az elmúlt órában kell kibocsátani, csak akkor, ha az előző órában használatban van.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Van-e az egyik kibocsátás és a következő egy maximális időszak

Nincs ilyen korlátozás. Csak a használati adatok kibocsátása. Ha például csak egy használati egységet kell elküldenie az előfizetések élettartama során, ezt megteheti.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Mi a maximális késleltetés az esemény bekövetkezése időpontja és a Microsoft számára a használati idő kibocsátása között?

Ideális esetben a használati eseményt óránként bocsátjuk ki az elmúlt órában bekövetkezett események esetében. Azonban késések várhatók. A maximálisan engedélyezett késleltetés 24 óra, amely után a használati események nem lesznek elfogadva. Az ajánlott eljárás az óránkénti használat összegyűjtése és a kibocsátás az óra végén egy esemény.

Ha például egy nap 1 ÓRAKOR egy használati esemény történik, akkor a következő napon 1 óráig kell kibocsátania az eseményhez kapcsolódó használati eseményt.  Abban az esetben, ha a rendszer kibocsátja a használatot, a szolgáltatás helyreállíthatja, majd elküldheti a használati eseményt a használat során eltelt óra intervallumban, a hűség elvesztése nélkül.

Ha a tényleges használat után 24 óra telt el, továbbra is kibocsáthatja a felhasználható egységeket a későbbi használati eseményekkel.  Ez a gyakorlat azonban megsértheti a végfelhasználók számlázási eseményeinek jelentéseit.  Javasoljuk, hogy a mérési adatokat naponta, hetente/havonta ne küldje el.  Az ügyfél általi tényleges használatot nehéz megérteni, valamint a használati eseményekkel kapcsolatos problémák és kérdések megoldására.

Egy másik ok, hogy óránként küldi el a használatot, hogy elkerülje, hogy a felhasználó megszakítja az előfizetést, mielőtt a közzétevő elküldi a napi/heti/havi kibocsátási eseményt.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Mi történik, ha több használati eseményt küld egy adott órában?

Az egyórás időköz csak egy használati eseményt fogad el. Az óra intervalluma a 0. percben kezdődik, és 59-kor végződik.  Ha a rendszer egynél több használati eseményt bocsát ki ugyanarra az órára, a rendszer minden további használati eseményt duplikálja.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Mi történik, ha az ügyfél a lemondási szabályzat által megengedett időn belül megszakítja a vásárlást?

A átalánydíjas összeg nem lesz felszámítva, de a túlhasználati felhasználás lesz.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Használhatok egyéni mérési csomagokat az egyszeri kifizetésekhez?

Igen, egyéni dimenziót is meghatározhat egyszeri fizetési egységként, és csak egyszer bocsáthatja ki az egyes ügyfelek számára.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Használhatók-e egyéni mérési csomagok a lépcsőzetes díjszabási modellhez?

Igen, a rendszer minden egyéni dimenzióval megvalósítható, amely egyetlen árszintet jelöl.

Például a contoso a $0,5-as e-mail-címet szeretné felszámítani az első 1000 e-mailek, az $0,4/e-mailek esetében a 1000 és a 5000 e-mailek között, és a $0,2 e-mail-címéhez Meghatározhatnak három egyéni dimenziót, amelyek megfelelnek a három e-mailes díjszabási csomagnak. Egységeket bocsát ki az első dimenzióból, feltéve, hogy az e-mailek száma 1000 alatt marad, majd a második dimenzió egységei, amikor az e-mailek száma 1000 és 5000 között van, végül pedig a harmadik dimenzió egységei a fenti 5000 e-mailekhez.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Mi történik, ha a piactér-mérési szolgáltatás leállás miatt leáll?

Ha az ISV egyéni mérőszámot küld, és hibaüzenetet kap, a probléma oka lehet a Microsoft oldalán (általában a hasonló események elfogadását a hiba nélkül is), az ISV-nak várnia kell, majd újra kell próbálkoznia a kibocsátással.

Ha a hiba továbbra is fennáll, küldje el újra az egyéni mérőszámot a következő órában (a mennyiség felhalmozódása). Folytassa ezt a folyamatot, amíg nem érkezik a hiba a válaszba.

## <a name="for-saas-offers-only"></a>Csak SaaS-ajánlatok esetén

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Mi történik, ha már leiratkozott SaaS-előfizetésre vonatkozó használatot bocsát ki?

A Piactéri platformra kibocsátott használati események nem lesznek elfogadva az SaaS-előfizetés törlése után.

A használat csak az előfizetett állapotú előfizetések esetében állítható elő (és nem a `PendingFulfillmentStart` , `Suspended` , vagy az állapotra vonatkozó előfizetések esetén `Unsubscribed` ).

Az egyetlen kivétel az, hogy a rendszer a SaaS-előfizetés megszakítása előtti időpontra vonatkozó jelentéskészítési használatot.

Az ügyfél például még 3 órakor megszakította az SaaS-előfizetést. Immár 5 órakor a kiadó továbbra is kibocsáthatja a használatot a mai napig 6 PM és 3 miniszterelnök között, ehhez az SaaS-előfizetéshez.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Lekérheti az összes SaaS-előfizetés listáját, beleértve az aktív és a leiratkozott előfizetéseket is?

Igen, ha meghívja az [előfizetések listázása API beolvasása](pc-saas-fulfillment-api-v2.md#subscription-apis) lehetőséget, az tartalmazza az összes SaaS-előfizetés listáját. Az egyes SaaS-előfizetések válaszában az állapot mező rögzíti, hogy az előfizetés aktív vagy leiratkozott állapotban van-e.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>A SaaS-előfizetési időszak kezdő és záró dátuma, valamint a túlterhelési használathoz kapcsolódó emisszió?

A kereten túli események az *előfizetett* állapotú meglévő SaaS-előfizetések bármelyik időpontban kiállíthatók. A kiadó feladata a használati események kibocsátása a számlázási csomagban meghatározott szabályzat alapján. A túlhasználatot a SaaS-előfizetés kifejezésében meghatározott dátumok alapján kell kiszámítani. 

Ha például a közzétevő olyan SaaS-csomagot határoz meg, amely tartalmazza a 1000-es e-maileket a $100-es havi átalányösszegben, a $1 1000-nál korábbi e-maileket az egyéni dimenzión keresztül számoljuk fel.

Amikor az ügyfél megvásárolja és aktiválja az előfizetést január 6-án, az átalányban foglalt 1000-os e-mailt a rendszer ettől a naptól kezdve számítja fel. Tehát ha február 5-ig (az előfizetés első hónapjának végén) csak az 900 e-mailt küldi el, az ügyfél csak az előfizetés első hónapjára fizeti ki a rögzített díjat, és a kiadó a közzétételi eseményeket a közzétevőnek a január 6. és február 5. között fogja kiadni. Február 6-án az előfizetés automatikusan megújul, a szám pedig újra elindul. Ha február 15-én az ügyfél elérte az elküldött 1000 e-maileket, az 5. március 5-ig elküldött e-mailek fennmaradó részét a kiadó által kibocsátott túlhasználati események alapján ($1/e-mailben) számítjuk fel.

## <a name="next-steps"></a>Következő lépések

- További információ: Marketplace- [mérési szolgáltatás API](./marketplace-metering-service-apis.md)-k.
