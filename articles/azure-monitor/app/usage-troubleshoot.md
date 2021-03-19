---
title: Felhasználói elemzési eszközök – Azure Application Insights
description: Hibaelhárítási útmutató – a hely és az alkalmazás használatának elemzése a Application Insights használatával.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 02d1bd9d204d88ba90218b1254c66ac0da80be85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87323502"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>A Application Insights felhasználói viselkedési elemzési eszközeinek hibáinak megoldása
Kérdése van a [felhasználói viselkedést elemző eszközökkel kapcsolatban Application Insights](usage-overview.md): [felhasználók, munkamenetek, események](usage-segmentation.md), [tölcsérek](usage-funnels.md), [Felhasználókövetés](usage-flows.md), [megőrzés](usage-retention.md)vagy kohorszok? Íme néhány válasz.

## <a name="counting-users"></a>Felhasználók számlálása
**A felhasználói viselkedés elemzési eszközei azt mutatják, hogy az alkalmazásom egy felhasználóval vagy munkamenettel rendelkezik, de tudom, hogy az alkalmazásom sok felhasználóval vagy munkamenettel rendelkezik. Hogyan oldhatók meg ezek a helytelen számok?**

A Application Insights összes telemetria-eseményében szerepel egy [névtelen felhasználói azonosító](./data-model-context.md) és egy [munkamenet-azonosító](./data-model-context.md) a szabványos tulajdonságaik közül kettőként. Alapértelmezés szerint az összes használati elemzési eszköz a felhasználók és a munkamenetek számát ezen azonosítók alapján számítja ki. Ha ezeket a szabványos tulajdonságokat nem tölti fel egyedi azonosítók az alkalmazás minden felhasználója és munkamenete számára, akkor a használati elemzési eszközökben a felhasználók és a munkamenetek helytelen száma jelenik meg.

Ha webalkalmazást figyel, a legegyszerűbb megoldás a [Application Insights JavaScript SDK](./javascript.md) hozzáadása az alkalmazáshoz, és győződjön meg arról, hogy a parancsfájl-kódrészlet be van töltve minden figyelni kívánt oldalon. A JavaScript SDK automatikusan létrehoz névtelen felhasználói és munkamenet-azonosítókat, majd feltölti a telemetria-eseményeket az alkalmazásból elküldett azonosítókkal.

Ha webszolgáltatást figyel (nincs felhasználói felülete), [hozzon létre egy telemetria-inicializáló, amely feltölti a névtelen felhasználói azonosító és a munkamenet-azonosító tulajdonságait](usage-send-user-context.md) a szolgáltatás egyedi felhasználók és munkamenetek fogalmai alapján.

Ha az alkalmazás [hitelesített felhasználói azonosítókat](./api-custom-events-metrics.md#authenticated-users)küld, akkor a felhasználók eszközön a hitelesített felhasználói azonosítók alapján számíthat. A "Megjelenítés" legördülő menüben válassza a "hitelesített felhasználók" lehetőséget.

A felhasználói viselkedés elemzési eszközei jelenleg nem támogatják a felhasználók és a munkamenetek számlálását a névtelen felhasználói AZONOSÍTÓtól, a hitelesített felhasználói AZONOSÍTÓtól vagy a munkamenet-AZONOSÍTÓtól eltérő tulajdonságok alapján.

## <a name="naming-events"></a>Események elnevezése
**Az alkalmazásom több ezer különböző oldal-és egyéni esemény-névvel rendelkezik. Nehéz megkülönböztetni őket egymástól, és a felhasználói viselkedés elemzési eszközei gyakran nem válaszolnak. Hogyan javíthatom ezeket az elnevezési problémákat?**

Az oldal nézet és az egyéni események nevei a felhasználói viselkedés elemzési eszközein keresztül használatosak. A kiértékelési események is kritikus fontosságúak az ilyen eszközökből származó értékek beszerzéséhez. A cél egy olyan egyensúly, amely a túl kevés, túlságosan általános névvel ("gomb csattant"), és túl sok, túlságosan specifikus névvel rendelkezik ("Szerkesztés gomb rákattintott a http: \/ /www.contoso.com/index").

Ha módosítani szeretné a lap nézetét és az alkalmazás által küldött egyéni események nevét, meg kell változtatnia az alkalmazás forráskódját, és újra kell telepítenie. A **Application Insightsban lévő összes telemetria-adatérték 90 napig tárolódik, és nem törölhető**, így az események neveiben végzett módosítások teljes egészében a 90 nap lesz. A név módosítása után a 90 nappal a régi és az új esemény neve is megjelenik a telemetria, így a lekérdezéseket és a csapatokon belüli kommunikációt is módosítani kell.

Ha az alkalmazás túl sok nézet nevét küldi el, ellenőrizze, hogy a rendszer manuálisan adta-e meg ezeket a nézeteket a kódban, vagy hogy automatikusan elküldje-e őket a Application Insights JavaScript SDK:

* Ha az oldal nézetének neve manuálisan van megadva a kódban az [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)használatával, módosítsa a nevet kevésbé konkrétra. Kerülje a gyakori hibákat, például az URL-cím elhelyezését az oldal nézetének nevében. Ehelyett használja az API URL-paraméterét `trackPageView` . Más részletek áthelyezése az oldal nézetének nevéből egyéni tulajdonságokba.

* Ha a Application Insights JavaScript SDK automatikusan küldi az oldal nézetének neveit, módosíthatja a lapok címeit, vagy átválthat az oldalmegtekintések nevének manuális elküldéséhez. Az SDK alapértelmezés szerint az oldal nézet neveként elküldi az egyes lapok [címét](https://developer.mozilla.org/docs/Web/HTML/Element/title) . Megváltoztathatja a címeket, hogy általánosabbak legyenek, de figyelembe kell vennie a SEO-t és más, a változtatások következményeit is. Az oldalmegtekintési nevek manuális megadásával az `trackPageView` API felülbírálja az automatikusan összegyűjtött neveket, így több általános nevet is küldhet a telemetria a lapok címeinek módosítása nélkül.   

Ha az alkalmazás túl sok egyéni eseményazonosító küldését küldi el, módosítsa a kód nevét kevésbé konkrét értékre. Az URL-címek és más, oldalanként vagy dinamikus információk az egyéni események neveiben való közvetlen üzembe helyezésének elkerülése érdekében. Ehelyett helyezze át ezeket az adatokat az egyéni esemény egyéni tulajdonságaiba az API-val `trackEvent` . Például a helyett a következőt `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` javasoljuk: `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Következő lépések

* [A felhasználói viselkedés elemzési eszközeinek áttekintése](usage-overview.md)

## <a name="get-help"></a>Segítség kérése
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

