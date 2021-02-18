---
title: Az Azure Application Insights rendelkezésre állási tesztek megoldása
description: Webtesztek hibakeresése az Azure Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/19/2020
ms.reviewer: sdash
ms.openlocfilehash: 849701caf73a4fb289773c67bccaab2e0f39dbd1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583734"
---
# <a name="troubleshooting"></a>Hibaelhárítás

Ez a cikk segítséget nyújt a rendelkezésre állás figyelése során esetlegesen előforduló gyakori hibák elhárításához.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>A ping tesztek hibaelhárítási jelentésének lépései

A hibaelhárítási jelentés segítségével egyszerűen diagnosztizálhatja a **ping teszteket** okozó gyakori problémákat.

![A rendelkezésre állás lapról való navigáláshoz szükséges animáció, ha kijelöl egy hibát a végpontok közötti tranzakció részletei közül a hibaelhárítási jelentés megtekintéséhez.](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. A Application Insights erőforrás rendelkezésre állás lapján válassza az általános lehetőséget, vagy az egyik rendelkezésre állási tesztet.
2. Válassza a **nem sikerült** lehetőséget, majd a bal oldalon a "részletezés" alatt egy tesztet, vagy válassza ki az egyik pontot a scatter parcellán.
3. A végpontok közötti tranzakció részletei lapon válasszon ki egy eseményt, majd a "hibaelhárítási jelentés összefoglalása" területen válassza a **[ugrás lépésre]** lehetőséget a hibaelhárítási jelentés megtekintéséhez.

> [!NOTE]
>  Ha a kapcsolatok újrafelhasználásának lépése megtalálható, akkor a DNS-feloldás, a kapcsolatok létrehozása és a TLS-átviteli lépések nem lesznek jelen.

|Lépés | Hibaüzenet | Lehetséges ok |
|-----|---------------|----------------|
| Kapcsolatok újrafelhasználása | n.a. | Általában egy korábban létesített kapcsolattól függ, hogy a webes teszt lépés függ-e. Így nincs szükség DNS-, kapcsolat-vagy SSL-lépésre. |
| DNS feloldása | A távoli név nem oldható fel: "az URL-cím" | A DNS-feloldási folyamat sikertelen volt, valószínűleg helytelenül konfigurált DNS-rekordok vagy ideiglenes DNS-kiszolgáló meghibásodása miatt. |
| Kapcsolatok létrehozása | A kapcsolódási kísérlet sikertelen volt, mert a csatlakoztatott fél egy adott idő elteltével nem válaszolt megfelelően. | Általánosságban azt jelenti, hogy a kiszolgáló nem válaszol a HTTP-kérelemre. Gyakori ok, hogy a tesztelési ügynököket a kiszolgálón lévő tűzfal blokkolja. Ha egy Azure-Virtual Networkon belül szeretne tesztelni, vegye fel a rendelkezésre állási szolgáltatás címkéjét a környezetbe.|
| TLS-átvitel  | Az ügyfél és a kiszolgáló nem tud kommunikálni, mert nem rendelkeznek közös algoritmussal.| Csak a TLS 1,0, 1,1 és 1,2 támogatottak. Az SSL nem támogatott. Ez a lépés nem ellenőrzi az SSL-tanúsítványokat, és csak biztonságos kapcsolatot létesít. Ez a lépés csak akkor jelenik meg, ha hiba történik. |
| Válasz fejlécének fogadása | Nem olvashatók be az adatok a szállítási kapcsolatban. A csatlakoztatás bezárult. | A kiszolgáló protokollhiba hibát jelzett a válasz fejlécében. Például a kiszolgáló lezárta a csatlakozást, ha a válasz nem teljes. |
| Válasz törzsének fogadása | Nem olvashatók be az adatok a szállítási kapcsolatban: a kapcsolódás bezárult. | A kiszolgáló protokollhiba hibát jelzett a válasz törzsében. Például a kiszolgáló lezárta a csatlakozást, ha a válasz nem teljesen olvasható, vagy az adathalmaz mérete helytelen a darabolásos válasz törzsében. |
| Átirányítási korlát ellenőrzése | A weblap túl sok átirányítással rendelkezik. Ez a hurok itt lesz megszakítva, mivel ez a kérelem túllépte az automatikus átirányítások korlátját. | A tesztekhez legfeljebb 10 átirányításra van lehetőség. |
| Állapotkód ellenőrzése | `200 - OK` nem felel meg a várt állapotnak `400 - BadRequest` . | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve. |
| Tartalom érvényesítése | A válaszban nem szerepelt a kötelező "Hello" szöveg. | A karakterlánc nem pontos kis-és nagybetűket megkülönböztető egyezés a válaszban, például a "Welcome!" karakterlánc. Egyszerű sztringnek kell lennie, helyettesítő karakterek nélkül (például csillag). Ha a lap tartalma megváltozik, előfordulhat, hogy frissítenie kell a karakterláncot. A tartalmi egyezés csak az angol karaktereket támogatja. |
  
## <a name="common-troubleshooting-questions"></a>Gyakori hibaelhárítási kérdések

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>A webhely megfelelőnek tűnik, de tesztelési hibák láthatók? Miért Application Insights a riasztás?

   * A tesztben engedélyezve vannak a **függő kérelmek** elemzése? Ez szigorú ellenőrzési eredményeket eredményez, például szkripteket, képeket stb. Előfordulhat, hogy az ilyen típusú hibák nem észlelhetők a böngészőben. Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha bármelyikük meghibásodik, a teszt sikertelenként lesz jelentve, még akkor is, ha a fő HTML-oldal probléma nélkül betöltődik. Ha a tesztet ilyen erőforrás-hibákra szeretné leegyszerűsíteni, egyszerűen törölje a függő kérelmek elemzését a tesztelési konfigurációból.

   * Annak érdekében, hogy csökkentse a zaj esélyét az átmeneti hálózati visszavertség stb. esetében, ellenőrizze, hogy be van-e jelölve az újrapróbálkozások tesztelési hibákhoz beállítás Azt is megteheti, hogy több helyről is teszteli, és ennek megfelelően kezeli a riasztási szabály küszöbértékét, hogy megakadályozza az indokolatlan riasztásokat okozó helyadatok

   * Kattintson bármelyik piros pontra a rendelkezésre állási Scatter ábrázolási élményben, vagy a keresési ablak bármely rendelkezésre állási hibája alapján, és tekintse meg, hogy miért jelentettük be a hibát. A teszt eredménye, valamint a korrelált kiszolgálóoldali telemetria (ha engedélyezve van) segít megérteni, miért nem sikerült a teszt. Az átmeneti problémák gyakori okai a hálózati vagy a kapcsolatok problémái.

   * A teszt időkorlátja? 2 perc múlva megszakítjuk a teszteket. Ha a ping vagy a többlépéses teszt 2 percnél hosszabb időt vesz igénybe, a rendszer hibát jelez. Érdemes lehet többre feltörni a tesztet, amely rövidebb időtartamokban is elvégezhető.

   * Az összes helyszín hibát jelzett, vagy csak néhányat? Ha csak egyes jelentett hibák történtek, a hálózati/CDN-problémák miatt előfordulhat. A piros pontokra kattintva könnyebben megismerheti, miért jelentettek hibát a hely.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nem kaptam meg e-mailt, amikor a riasztást aktiváltam, vagy megoldottam vagy mindkettőt?

A klasszikus riasztások konfigurációjában ellenőrizze, hogy az e-mailek közvetlenül szerepelnek-e a listában, vagy a terjesztési lista az értesítések fogadására van konfigurálva. Ha igen, akkor a terjesztési lista konfigurációjában ellenőrizze, hogy fogadhat-e külső e-maileket. Ellenőrizze azt is, hogy a levelezési rendszergazdája rendelkezhet-e a problémát okozó házirendekkel.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nem kaptam meg a webhook-értesítést?

Győződjön meg arról, hogy a webhook-értesítést fogadó alkalmazás elérhető, és sikeresen feldolgozza a webhook-kérelmeket. További információért tekintse meg [ezt](../alerts/alerts-log-webhook.md) a témakört.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>403 Tiltott hibát kapok, mit jelent ez?

Ez a hiba azt jelzi, hogy tűzfal-kivételeket kell hozzáadnia ahhoz, hogy a rendelkezésre állási ügynökök teszteljék a célként megadott URL-címet. Az engedélyezett ügynökök IP-címeinek teljes listájáért lásd az [IP-kivételt ismertető cikket](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Az időszakos teszt meghiúsult egy protokollmegsértési hibával?

A „protokollmegsértés... A CR karakter után LF karakternek kell következnie” hiba a kiszolgáló (vagy a függőségek) problémáját jelzi. Ez akkor történik, amikor hibás formátumú fejlécek vannak beállítva a válaszban. Ezt a terheléselosztók vagy a CDN-ek okozhatják. Konkrétan előfordulhat, hogy egyes fejlécek nem használják a CRLF a sor végére, ami megsérti a HTTP-specifikációt, ezért a .NET webkérési szinten nem végez ellenőrzést. Vizsgálja meg, hogy milyen válaszokat tartalmaz a helyszíni fejlécek.

> [!NOTE]
> Előfordulhat, hogy az URL-cím nem sikerül a HTTP-fejlécek nyugodt érvényesítését biztosító böngészőkön. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nem látok kapcsolódó kiszolgálóoldali telemetria a tesztelési hibák diagnosztizálásához? *

Ha a kiszolgálóoldali alkalmazásához be van állítva az Application Insights, akkor ezt okozhatja az, hogy [mintavételezés](./sampling.md) van folyamatban. Válasszon másik rendelkezésre állási eredményt.

### <a name="can-i-call-code-from-my-web-test"></a>Meghívhatok egy kódot a webes tesztből?

Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?

A két kifejezés hasonló értelmű, felcserélhető. A „rendelkezésre állási teszt” általánosabb fogalom, amely az URL-ping tesztek mellett a többlépéses webes teszteket is magában foglalja.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.

   Két lehetséges megoldás létezik:

   * Konfigurálhatja úgy a tűzfalat, hogy az engedélyezze a [webes tesztügynökök IP-címeiről](./ip-addresses.md) érkező bejövő kéréseket.
   * Saját kód megírásával rendszeresen ellenőrizheti a belső kiszolgálót. Futtassa a kódot a tűzfal mögötti tesztkiszolgáló háttérfolyamataként. A tesztelési folyamat az eredményeket a Core SDK-csomag [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API-jával küldheti el az Application Insightsba. Ehhez szükség van arra, hogy a tesztkiszolgáló kimenő hozzáféréssel rendelkezzen az Application Insights betöltési végpontjához, de ez jóval kisebb biztonsági kockázatot jelent a bejövő kérések engedélyezéséhez képest. Az eredmények megjelennek a rendelkezésre állási webes tesztek paneleken, bár a felhasználói élmény kis mértékben le lesz egyszerűsítve a portálon keresztül létrehozott tesztek esetében. Az egyéni rendelkezésre állási tesztek az elemzés, a keresés és a mérőszámok rendelkezésre állási eredményeiként is megjelennek.

### <a name="uploading-a-multi-step-web-test-fails"></a>A többlépéses teszt feltöltése sikertelen

Ennek valamilyen oka lehet:
   * A méretkorlát: 300 KB.
   * A hurkok nem támogatottak.
   * A más webes tesztekre mutató hivatkozások nem támogatottak.
   * Az adatforrások nem támogatottak.

### <a name="my-multi-step-test-doesnt-complete"></a>A többlépéses teszt nem fejeződik be

Egy teszt legfeljebb 100 kérelemből állhat. Emellett a teszt leáll, ha két percnél hosszabb ideig fut.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hogyan futtathatok tesztet ügyféltanúsítványokkal?

Ez jelenleg nem támogatott.

## <a name="who-receives-the-classic-alert-notifications"></a>Kik kapják meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket, így biztosítva, hogy csak a kívánt címzettek kapják meg az értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../alerts/alerts-classic.overview.md)közötti különbségről és az új riasztások élményéről, tekintse meg a [riasztások áttekintése című cikket](../alerts/alerts-overview.md). A riasztások értesítésének vezérléséhez az új riasztások használatakor használjon [műveleti csoportokat](../alerts/action-groups.md).

* A klasszikus riasztási értesítések esetében javasoljuk, hogy adott címzetteket használjon.

* A nem Y helyekről érkező hibákkal kapcsolatos riasztások esetén a **csoportos/csoportos** jelölőnégyzetes beállítás, ha engedélyezve van, a a rendszergazdai/társ-rendszergazdai szerepkörökkel rendelkező felhasználóknak küldi a felhasználókat.  Lényegében az _előfizetés_ _összes_ rendszergazdája értesítést kap.

* Ha engedélyezve van a rendelkezésre állási metrikákkal kapcsolatos riasztások, akkor a **csoportos vagy csoportos** jelölőnégyzet be van jelölve, ha engedélyezve van, az előfizetésben tulajdonos, közreműködő vagy olvasó szerepkörrel rendelkező felhasználók számára küldi el a rendszer. _Minden_ olyan felhasználó, aki hozzáféréssel rendelkezik az előfizetéshez, a Application Insights erőforrás hatókörben van, és értesítést fog kapni. 

> [!NOTE]
> Ha jelenleg a **tömeges/csoportos** jelölőnégyzetet használja, és letiltja, akkor nem fogja tudni visszaállítani a változást.

Ha a felhasználókat a szerepköreik alapján kell értesítenie, használja az új riasztási élmény/közel valós idejű riasztásokat. A [műveleti csoportokkal](../alerts/action-groups.md)e-mailes értesítéseket állíthat be a felhasználók számára a közreműködő/tulajdonos/olvasó szerepkörök bármelyikével (egyetlen lehetőségként nem kombinálva).

## <a name="next-steps"></a>Következő lépések

* [Többlépéses webes tesztelés](availability-multistep.md)
* [URL-ping tesztek](monitor-web-app-availability.md)