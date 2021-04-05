---
title: Speciális kérelemszabályozás az Azure API Managementtel
description: Ismerje meg, hogyan hozhat létre és alkalmazhat rugalmas kvótát és díjszabást korlátozó házirendeket az Azure API Management használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 36b21196207f65975dae950f43ec0c7094991dad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362029"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Speciális kérelemszabályozás az Azure API Managementtel
A bejövő kérések szabályozása az Azure API Management kulcsfontosságú szerepe. A kérések sebességének vagy az átvitt kérelmeknek/adatoknak a szabályozásával API Management lehetővé teszi az API-szolgáltatók számára, hogy az API-kat felhasználják a visszaéléstől, és értéket hozzanak létre különböző API-termékekhez.

## <a name="rate-limits-and-quotas"></a>A díjszabási korlátok és kvóták
A díjszabási korlátok és a kvóták különböző célokra használatosak.

### <a name="rate-limits"></a>Sebességkorlátok
A díjszabási határértékek általában a rövid és az intenzív mennyiségű adattörések elleni védelemhez használatosak. Ha például ismeri a háttérrendszer szűk keresztmetszetét az adatbázisában, magas hívási kötettel, beállíthatja, `rate-limit-by-key` hogy a házirend ne engedélyezze a nagy hívási kötetet e beállítás használatával.

### <a name="quotas"></a>Kvóták
A kvóták általában hosszabb időn keresztül vezérlik a hívások sebességét. Megadhatják például, hogy az egyes előfizetők hány hívást végezhetnek egy adott hónapban. Az API-k bevétele érdekében a kvótákat különbözőképpen is megadhatja a többszintű előfizetések esetében. Előfordulhat például, hogy egy alapszintű csomag előfizetése legfeljebb 10 000 hívást tud készíteni havonta, de a prémium szint akár 100 000 000 hívást is felhasználhat havonta.

Az Azure API Managementon belül a díjszabási korlátok általában gyorsabban továbbítódnak a csomópontok között a tüskékkel szembeni védelem érdekében. Ezzel szemben a használati kvóta adatait hosszabb távon használják, így a megvalósításuk eltérő.

> [!CAUTION]
> A szabályozási architektúra elosztott jellege miatt a díjszabási korlátozás soha nem teljesen pontos. A konfigurált és az engedélyezett kérelmek valós száma közötti különbség a kérés mennyisége és sebessége, a háttérbeli késés és egyéb tényezők alapján változhat.

## <a name="product-based-throttling"></a>Termék-alapú szabályozás
Egy adott előfizetésre hatókörrel rendelkező sávszélesség-szabályozási képességek akkor hasznosak, ha az API-szolgáltató korlátozásokat alkalmaz azon fejlesztők számára, akik regisztráltak az API használatára. Ez azonban nem segít például az API egyes végfelhasználóinak szabályozásában. Lehetséges, hogy a fejlesztői alkalmazás egyetlen felhasználója használja fel a teljes kvótát, majd megakadályozza, hogy a fejlesztő más ügyfelei is használhassák az alkalmazást. Emellett a nagy mennyiségű kérést előidéző ügyfelek is korlátozhatják az alkalmi felhasználók hozzáférését.

## <a name="custom-key-based-throttling"></a>Egyéni kulcs-alapú szabályozás

> [!NOTE]
> A `rate-limit-by-key` és a `quota-by-key` házirendek nem érhetők el, ha az Azure API Management felhasználási szintjében van. 

A [díjszabási kulcs](./api-management-access-restriction-policies.md#LimitCallRateByKey) és a [kvóta-alapú](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) házirendek rugalmasabb megoldást biztosítanak a forgalom szabályozására. Ezekkel a szabályzatokkal meghatározhatja a forgalom használatának nyomon követéséhez használt kulcsok azonosítására szolgáló kifejezéseket. Ennek a működésnek a példája a legkönnyebb illusztrálva. 

## <a name="ip-address-throttling"></a>IP-cím szabályozása
Az alábbi házirendek egyetlen ügyfél IP-címét korlátozzák percenként 10 hívásra, összesen 1 000 000 hívást és havi 10 000 kilobájt sávszélességet. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Ha az interneten lévő összes ügyfél egyedi IP-címet használt, akkor ez a felhasználó általi használat korlátozásának hatékony módja lehet. Azonban valószínű, hogy több felhasználó oszt meg egyetlen nyilvános IP-címet, mert az Internet NAT-eszközön keresztül fér hozzá az internethez. Ennek ellenére a nem hitelesített hozzáférést engedélyező API-k esetében a `IpAddress` legjobb megoldás lehet.

## <a name="user-identity-throttling"></a>Felhasználói identitás szabályozása
Ha egy végfelhasználó hitelesítése megtörtént, a rendszer egy szabályozási kulcsot generálhat olyan információk alapján, amelyek egyedileg azonosítják az adott felhasználót.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ez a példa azt mutatja be, hogyan lehet kibontani az engedélyezési fejlécet, alakítsa át az `JWT` objektumra, és a token tárgya alapján azonosítsa a felhasználót, és használja az értéket a kulcs korlátozására. Ha a felhasználói identitást a `JWT` más jogcímek egyikében tárolják, akkor ez az érték a helyén is felhasználható.

## <a name="combined-policies"></a>Kombinált házirendek
Bár a felhasználó-alapú szabályozási szabályzatok nagyobb mértékben szabályozzák az előfizetésen alapuló szabályozási szabályzatokat, a két funkció együttes használata továbbra is fennáll. A termék előfizetési kulcsának szabályozása (a hívások számának[korlátozása előfizetés alapján](./api-management-access-restriction-policies.md#LimitCallRate) , [használati kvóta beállítása előfizetéssel](./api-management-access-restriction-policies.md#SetUsageQuota)) nagyszerű lehetőség arra, hogy a használati szintek alapján megszámolja az API-k bevételeit. A felhasználó általi szabályozáshoz szükséges finomabb szabályozás kiegészíthető, és meggátolja, hogy az egyik felhasználó viselkedése ne legyen egy másik felhasználói felületének romlása. 

## <a name="client-driven-throttling"></a>Ügyfél-vezérelt szabályozás
Ha a szabályozási kulcs egy [házirend-kifejezéssel](./api-management-policy-expressions.md)van definiálva, akkor ez az API-szolgáltató, amely kiválasztja a szabályozás hatókörét. A fejlesztőknek azonban érdemes megszabni, hogyan korlátozzák a saját ügyfeleiket. Ezt az API-szolgáltató engedélyezheti egy egyéni fejléc bevezetésével, amely lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy az API-val kommunikáljon a kulccsal.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy kiválassza, hogyan szeretné létrehozni a ráta-korlátozó kulcsot. Az ügyfél fejlesztői létrehozhatják a saját díjszabási szintjeiket a kulcsok készletének a felhasználók számára való kiosztásával és a kulcshasználat elforgatásával.

## <a name="summary"></a>Összefoglalás
Az Azure API Management díjszabást és árajánlatot biztosít a védelemhez, és értéket adhat hozzá az API-szolgáltatáshoz. Az új szabályozási szabályzatok egyéni hatóköri szabályokkal lehetővé teszik, hogy az ügyfelek még jobb alkalmazásokat hozzanak létre. A jelen cikkben szereplő példák a kulcsok az ügyfél IP-címeivel, a felhasználói identitással és az ügyfél által generált értékekkel való korlátozásával mutatják be az új szabályzatok használatát. Azonban az üzenet számos más része használható, például a felhasználói ügynök, az URL-cím elérési útja, az üzenet mérete.

## <a name="next-steps"></a>Következő lépések
Küldje el nekünk visszajelzését GitHub-problémaként ebben a témakörben. Érdemes meghallgatni azokat a lehetséges kulcsfontosságú értékeket, amelyek a forgatókönyvekben logikus választásnak bizonyultak.
