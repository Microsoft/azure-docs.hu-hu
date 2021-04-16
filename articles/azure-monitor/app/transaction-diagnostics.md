---
title: Azure Application Insights Transaction Diagnostics | Microsoft Docs
description: Application Insights teljes tranzakciódiagnosztika
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 60365079c295e154ff0a38277c9ccdec35157e6e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481395"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Egyesített összetevők közötti tranzakciódiagnosztika

Az egységesített diagnosztikai élmény automatikusan egyetlen nézetben korrelál az összes felügyelt Application Insights kiszolgálóoldali telemetriára. Nem számít, ha több, különálló eszközkulcsokkal rendelkező erőforrással rendelkezik. Application Insights észleli a mögöttes kapcsolatot, és lehetővé teszi a tranzakció lassulásához vagy meghibásodásához kapcsolódó alkalmazás-összetevő, függőség vagy kivétel egyszerű diagnosztizálését.

## <a name="what-is-a-component"></a>Mi az az összetevő?

Az összetevők az elosztott/mikroszolgáltatás-alkalmazás egymástól függetlenül üzembe helyezhető részei. A fejlesztők és az üzemeltetési csapatok kódszintű láthatósággal vagy hozzáféréssel rendelkezik az ezen alkalmazás-összetevők által létrehozott telemetriához.

* Az összetevők eltérnek az olyan "megfigyelt" külső függőségektől, mint az SQL, az EventHub stb., amelyekhez a csapat vagy szervezet nem férhet hozzá (kód vagy telemetria).
* Az összetevők bármilyen számú kiszolgáló-, szerepkör-/tárolópéldányon futnak.
* Az összetevők külön Application Insights eszközkulcsok (akkor is, ha az előfizetések eltérőek) vagy különböző szerepkörök, amelyek egyetlen eszközkulcsnak Application Insights jelentést. Az új felhasználói élmény az összes összetevő részleteit megjeleníti, függetlenül azok beállítási mikéntjétől.

> [!NOTE]
> * **Hiányoznak a kapcsolódó elemhivatkozások?** Az összes kapcsolódó telemetria [](#cross-component-transaction-chart) a [](#all-telemetry-with-this-operation-id) bal oldal felső és alsó részén található. 

## <a name="transaction-diagnostics-experience"></a>Tranzakciódiagnosztikai élmény
Ez a nézet négy fő részből áll: az eredmények listájából, az összetevők közötti tranzakciódiagramból, a művelettel kapcsolatos összes telemetria idősorozat-listájából, valamint a bal oldalon kiválasztott telemetriai elemek részletek paneljére.

![Fő részek](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Összetevőközi tranzakciós diagram

Ez a diagram egy idővonalat biztosít vízszintes sávokkal a kérések és az összetevők közötti függőségek időtartamára. Az összegyűjtött kivételek az idővonalon is meg vannak jelölve.

* A diagram felső sora a belépési pontot, a tranzakcióban az első összetevőnek hívott bejövő kérést jelöli. Az időtartam a tranzakció befejezéséhez szükséges teljes idő.
* A külső függőségek hívásai egyszerű, nem összecsukható sorok, amelyek ikonjai a függőség típusát jelképezik.
* A más összetevőkhöz való hívások összecsukható sorok. Minden sor egy adott, az összetevőnél meghívott műveletnek felel meg.
* Alapértelmezés szerint a kiválasztott kérés, függőség vagy kivétel a jobb oldalon jelenik meg.
* Válassza ki bármelyik sort, hogy a [részleteket a jobb oldalon lássa.](#details-of-the-selected-telemetry) 

> [!NOTE]
> A más összetevőkhöz irányuló hívások két sorral vannak: az egyik sor a hívó összetevő kimenő hívását (függőségét), a másik pedig a nevű összetevőnél lévő bejövő kérésnek felel meg. Az időtartamsávok kezdő ikonja és eltérő stílusa segít megkülönböztetni őket.

## <a name="all-telemetry-with-this-operation-id"></a>Az ezzel a műveletazonosítóval kapcsolatos összes telemetria

Ez a szakasz az ehhez a tranzakcióhoz kapcsolódó összes telemetria idősorozatában jeleníti meg az egyennézetes listanézetet. Emellett a tranzakciós diagramon nem megjelenő egyéni eseményeket és nyomkövetéseket is megjeleníti. Ezt a listát szűrheti egy adott összetevő/hívás által létrehozott telemetriához. A listában bármelyik telemetriaelemet kiválaszthatja, hogy a megfelelő részleteket [a jobb oldalon lássa.](#details-of-the-selected-telemetry)

![Az összes telemetria időütemezése](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>A kiválasztott telemetria részletei

Ez az összecsukható panel a tranzakciódiagramon vagy a listán kiválasztott elemek részleteit jeleníti meg. A "Show all" (Az összes megjelenítése) az összes összegyűjtött standard attribútumot felsorolja. Az egyéni attribútumok külön vannak felsorolva a standard készlet alatt. Kattintson a "..." a verem nyomkövetési ablaka alatt, hogy kimásolódási lehetőség megjelenik. A "Profiler-nyomkövetések megnyitása" vagy a "Hibakeresési pillanatkép megnyitása" a megfelelő részletes panelen jeleníti meg a kódszintű diagnosztikát.

![Kivétel részletei](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Keresési eredmények

Ez az összecsukható panel a szűrési feltételeknek megfelelő többi eredményt jeleníti meg. Kattintson bármelyik eredményre a fent felsorolt 3 szakasz megfelelő részleteinek frissítéséhez. Olyan mintákat próbálunk megtalálni, amelyek valószínűleg minden összetevőből elérhetők, még akkor is, ha bármelyikben mintavételezés van érvényben. Ezek "javasolt" mintákként jelennek meg.

![Keresési eredmények](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profilkészítő és pillanatkép-hibakereső

[Application Insights profilkészítő vagy](./profiler.md) [pillanatkép-hibakereső segítséget](snapshot-debugger.md) nyújt a teljesítménnyel és a hibákkal kapcsolatos problémák kódszintű diagnosztikához. Ezzel a felhasználói élmével egyetlen kattintással láthatja bármelyik összetevő profilkészítői nyomkövetését és pillanatképét.

Ha a Profiler nem működik, lépjen kapcsolatba a **serviceprofilerhelp microsoft.com \@**

Ha nem tud Snapshot Debugger, lépjen kapcsolatba a **snapshothelp \@ microsoft.com**

![Profilkészítő-integráció](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>GYIK

*Egyetlen összetevőt látok a diagramon, a többi pedig csak külső függőségként jelenik meg, anélkül, hogy részletezni lenne, mi történt az összetevőkben.*

Lehetséges okok:

* A többi összetevőt is Application Insights?
* A legújabb stabil SDK-t Application Insights használják?
* Ha ezek az összetevők különálló Application Insights-erőforrások, [](resources-roles-access-control.md) van-e szükség hozzáférésre. Ha rendelkezik hozzáféréssel, és az összetevők a legújabb Application Insights-s SZOFTVERDK-okkal vannak beszerzve, a jobb felső visszajelzési csatornán keresztül tud róla.

*Ismétlődő sorokat látok a függőségek számára. Ez várható?*

Jelenleg a kimenő függőségi hívást mutatjuk a bejövő kéréstől elkülönítve. A két hívás általában azonosnak tűnik, és csak az időtartam értéke különbözik a hálózati körbeút miatt. Az időtartamsávok kezdő ikonja és eltérő stílusa segít megkülönböztetni őket. Nem egyértelmű az adatok bemutatása? Visszajelzést küldhet nekünk!

*Mi a helyzet a különböző összetevőpéldányok órajel-eltűnésével?*

Az idővonalak a tranzakciós diagram óraelrendjére módosulnak. A pontos időbélyegeket a részletek panelen vagy az Elemzés használatával láthatja.

*Miért hiányzik az új felhasználói élmény a kapcsolódó elemek legtöbb lekérdezése miatt?*

Ez az elvárt működés. Az összes kapcsolódó elem már elérhető a bal oldalon (felső és alsó szakaszok). Az új felület két kapcsolódó elemet tartalmaz, amelyekre a bal oldal nem terjed ki: minden telemetria öt perccel az esemény előtt és után, valamint a felhasználói idővonal.

*A vártnál több eseményt látok a tranzakciódiagnosztikai élményben az Application Insights JavaScript SDK használatakor. Van mód arra, hogy tranzakciónként kevesebb eseményt látunk?*

A tranzakciódiagnosztikai élmény egyetlen műveletben jeleníti meg [az](correlation.md#data-model-for-telemetry-correlation) összes telemetriát, amely rendelkezik [műveletazonosítóval.](data-model-context.md#operation-id) Alapértelmezés szerint a Application Insights SDK for JavaScript minden egyedi oldalnézethez létrehoz egy új műveletet. Egyoldalas alkalmazásban (SPA) csak egy oldalnézeti esemény jön létre, és a rendszer egyetlen műveleti azonosítót használ az összes létrehozott telemetriához, ami azt eredményezheti, hogy számos esemény áll kapcsolatban ugyanazokkal a művelettel. Ezekben a forgatókönyvekben az Automatikus útvonalkövetés használatával automatikusan hozhat létre új műveleteket az egyoldalas alkalmazáson belüli navigációhoz. Az [enableAutoRouteTrackinget](javascript.md#single-page-applications) be kell kapcsolnia, hogy az URL-útvonal minden egyes frissüléskor lapnézetet generál (logikai oldalnézet esetén). Ha manuálisan szeretné frissíteni a műveletazonosítót, ezt a parancs hívását kell `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` elvégeznie. A PageView-események manuális aktiválása a műveletazonosítót is alaphelyzetbe állítja.
