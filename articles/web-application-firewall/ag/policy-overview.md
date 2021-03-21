---
title: Az Azure webalkalmazási tűzfal (WAF) szabályzatának áttekintése
description: Ez a cikk áttekintést nyújt a webalkalmazási tűzfal (WAF) globális, a webhelyről és az URI-szabályzatokról.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518736"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Az Azure webalkalmazási tűzfal (WAF) szabályzatának áttekintése

A webalkalmazási tűzfal házirendjei a WAF összes beállítását és konfigurációját tartalmazzák. Ide tartoznak a kizárások, az egyéni szabályok, a felügyelt szabályok stb. Ezeket a szabályzatokat ezután egy Application Gateway (Global), egy figyelő (hely) vagy egy elérésiút-alapú szabály (URI) társítják, amelyek érvénybe lépnek.

A létrehozható szabályzatok száma nincs korlátozva. Amikor létrehoz egy házirendet, az alkalmazásnak hozzá kell rendelnie egy Application Gateway-hez, amely érvénybe lép. Az Application Gateway, a figyelők és az elérésiút-alapú szabályok tetszőleges kombinációjával társítható.

## <a name="global-waf-policy"></a>Globális WAF házirend

Ha globálisan társít egy WAF-házirendet, a Application Gateway WAF mögötti összes hely védelme ugyanazokkal a felügyelt szabályokkal, egyéni szabályokkal, kizárásokkal és egyéb konfigurált beállításokkal történik.

Ha egyetlen szabályzatot szeretne alkalmazni az összes webhelyre, társíthatja a szabályzatot az Application gatewayhez. További információkért lásd: [webalkalmazási tűzfal házirendjeinek létrehozása Application Gateway](create-waf-policy-ag.md) számára a WAF szabályzat létrehozásához és alkalmazásához a Azure Portal használatával. 

## <a name="per-site-waf-policy"></a>Helyek közötti WAF szabályzat

A webhelyenként eltérő WAF-szabályzatokkal több, különböző biztonsági igényekkel rendelkező webhelyet is védelem alá helyezhet egyetlen WAF mögött. Ha például öt webhely található a WAF mögött, akkor megadhat öt különálló WAF-szabályzatot (figyelőnként egyet), így testre szabhatja a kizárásokat, az egyéni szabályokat, a felügyelt szabálykészleteket és az egyes webhelyek összes többi WAF-beállítását.

Tegyük fel, hogy az Application Gateway esetében érvényben van egy globális szabályzat. Az Application Gateway egyik figyelőjén ezt követően egy másik szabályzatot alkalmaz. Az adott figyelő szabályzata csak azon az egy figyelőn érvényes. Az Application Gateway globális szabályzata továbbra is érvényes az összes többi olyan figyelőre és elérésiút-alapú szabályra, amelyeknek nincs külön hozzárendelt szabályzatuk.

## <a name="per-uri-policy"></a>URI-szabályzat

Az URI szintjének még részletesebb testreszabásához WAF szabályzatot rendelhet hozzá elérésiút-alapú szabállyal. Ha vannak olyan lapok, amelyek különböző házirendeket igényelnek egy adott helyen, akkor módosíthatja a WAF szabályzatot, amely csak egy adott URI-t érint. Ez egy fizetési vagy bejelentkezési oldalra, vagy bármely más olyan URI-ra vonatkozhat, amely még konkrétabb WAF-házirendet igényel, mint a WAF mögött található többi hely.

A WAF-házirendekhez hasonlóan a konkrétabb házirendek felülbírálják a kevésbé specifikuskat. Ez azt jelenti, hogy egy URL-elérésiút-térképhez tartozó URI-szabályzat felülbírálja a fölötte lévő vagy a globális WAF szabályzatot.

### <a name="example"></a>Példa

Tegyük fel, hogy három hellyel rendelkezik: a contoso.com, a fabrikam.com és a adatum.com az Application Gateway mögött. Azt szeretné, hogy a WAF mind a három helyre legyen alkalmazva, de a adatum.com-mel kell felvennie a biztonságot, mert az ügyfelek meglátogatják, böngészhetik és vásárolhatják meg a termékeket.

Globális házirendet alkalmazhat a WAF, bizonyos alapbeállításokkal, kizárásokkal vagy egyéni szabályokkal, ha szükséges, hogy leállítsa a hamis pozitív adatokat a forgalom blokkolására. Ebben az esetben nincs szükség a globális SQL-befecskendezési szabályok futtatására, mivel a fabrikam.com és a contoso.com SQL-háttér nélküli statikus lapok. Így letilthatja ezeket a szabályokat a globális szabályzatban.

Ez a globális szabályzat alkalmas a contoso.com és a fabrikam.com, de körültekintően kell megfelelnie a adatum.com, ahol a bejelentkezési adatokat és a befizetéseket kezelik. A adatum-figyelőre alkalmazhat egy helyi házirendet, és megtarthatja a futó SQL-szabályokat. Azt is feltételezi, hogy egy cookie blokkolja a forgalmat, így a cookie-k kizárásával leállíthatja a hamis pozitív értéket. 

A adatum.com/payments URI-ja az, ahol óvatosnak kell lennie. Ezért alkalmazzon egy másik szabályzatot az URI-ra, és hagyja az összes szabályt, és távolítsa el az összes kivételt is.

Ebben a példában egy globális szabályzattal rendelkezik, amely két helyre vonatkozik. Van egy helyhez tartozó szabályzata, amely egy adott helyre vonatkozik, majd egy URI-házirend, amely egy adott elérésiút-alapú szabályra vonatkozik. A jelen példában a megfelelő PowerShell-lel kapcsolatban lásd: [WAF házirendek konfigurálása Azure PowerShell használatával](per-site-policies.md) .

## <a name="existing-waf-configurations"></a>Meglévő WAF-konfigurációk

Az új webalkalmazási tűzfal WAF-beállításai (egyéni szabályok, felügyelt szabálykészlet-konfigurációk, kizárások stb.) léteznek egy WAF-szabályzatban. Ha rendelkezik meglévő WAF, ezek a beállítások továbbra is előfordulhatnak a WAF-konfigurációban. Az új WAF házirendre való áttéréssel kapcsolatos további információkért [telepítse át a WAF config-t egy WAF-házirendbe](./migrate-policy.md). 


## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy hely és egy URI-házirendet a Azure PowerShell használatával](per-site-policies.md).
