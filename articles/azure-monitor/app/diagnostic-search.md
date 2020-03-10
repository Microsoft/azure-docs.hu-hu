---
title: Keresés használata az Azure Application Insightsban | Microsoft Docs
description: A webalkalmazás által eljuttatott nyers telemetria keresése és szűrése.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368311"
---
# <a name="using-search-in-application-insights"></a>Keresés használata a Application Insightsban

A keresés a [Application Insights](../../azure-monitor/app/app-insights-overview.md) egyik funkciója, amellyel megkeresheti és megismerheti az egyes telemetria-elemeket, például a lapok nézeteit, a kivételeket vagy a webes kérelmeket. És megtekintheti a kódolt naplókat és eseményeket.

(Az [adatelemzéshez használja az](../../azure-monitor/log-query/get-started-portal.md)összetettebb lekérdezéseket.)

## <a name="where-do-you-see-search"></a>Hol látja a keresést?

### <a name="in-the-azure-portal"></a>A Azure Portal

A diagnosztikai keresést az alkalmazás Application Insights áttekintés lapjáról nyithatja meg (a felső sávon található), vagy a bal oldali vizsgálat alatt.

![Keresés lap](./media/diagnostic-search/view-custom-events.png)

Nyissa meg az Event types (eseménytípus) legördülő menüt, ahol megtekintheti a telemetria-kérelmeket, a lapok nézeteit, az Ön által kódolt egyéni eseményeket és így tovább. Az eredmények listájának tetején található egy összegző diagram, amely az események számát mutatja az idő múlásával.

Az új események beszerzéséhez kattintson a legördülő menüből vagy a frissítés gombra.

### <a name="in-visual-studio"></a>Visual Studióban

A Visual Studióban van egy Application Insights keresési ablak is. Ez a leghasznosabb a hibakeresést végző alkalmazás által létrehozott telemetria események megjelenítéséhez. A közzétett alkalmazásból összegyűjtött eseményeket azonban a Azure Portal is megjelenítheti.

A Search (keresés) ablak megnyitása a Visual Studióban:

![A Visual Studio nyílt Application Insights keresés](./media/diagnostic-search/32.png)

A keresési ablakban a webes portálhoz hasonló funkciók szerepelnek:

![Visual Studio Application Insights keresési ablak](./media/diagnostic-search/34.png)

A nyomkövetési művelet lap a kérelem vagy az oldal nézet megnyitásakor érhető el. A "művelet" olyan események sorozatából áll, amelyek egyetlen kérelemhez vagy az oldal nézethez vannak társítva. Előfordulhat például, hogy a függőségi hívások, a kivételek, a nyomkövetési naplók és az egyéni események egyetlen művelet részét képezik. A művelet nyomon követése lap grafikusan mutatja az események időzítését és időtartamát a kérelem vagy az oldal nézethez képest.

## <a name="inspect-individual-items"></a>Egyedi elemek vizsgálata

Válassza ki bármelyik telemetria elemet a legfontosabb mezők és a kapcsolódó elemek megjelenítéséhez.

![Egyéni függőségi kérelem képernyőképe](./media/diagnostic-search/telemetry-item.png)

Ez elindítja a végpontok közötti tranzakció részletes nézetét.

## <a name="filter-event-types"></a>Események szűrése

Nyissa meg az Event types (eseménytípus) legördülő menüt, és válassza ki a megtekinteni kívánt esemény típusait. (Ha később szeretné visszaállítani a szűrőket, kattintson az Alaphelyzetbe állítás elemre.)

Az események típusai a következők:

* **Nyomkövetési** - [diagnosztikai naplók](../../azure-monitor/app/asp-net-trace-logs.md) , beleértve a TrackTrace, a log4Net, a NLog és a System. Diagnostics. Trace hívásokat.
* **Kérelem** – a kiszolgálói alkalmazás által fogadott HTTP-kérelmek, beleértve a lapokat, a parancsfájlokat, a képeket, a stíluslapokat és az adatfájlokat. Ezek az események a kérések és válaszok áttekintő diagramjainak létrehozására használhatók.
* Az **oldal nézete - ** [a webes ügyfél által elküldett telemetria](../../azure-monitor/app/javascript.md), amelyet a rendszer az oldalmegtekintési jelentések létrehozásához használ.
* **Egyéni esemény** – ha a TrackEvent () hívásait beszúrta a [használat figyeléséhez](../../azure-monitor/app/api-custom-events-metrics.md), itt kereshet.
* **Kivétel** – nem kezelt [Kivételek a kiszolgálón](../../azure-monitor/app/asp-net-exceptions.md), és a TrackException () használatával bejelentkezett.
* A **függőség** - [a kiszolgálói alkalmazástól](../../azure-monitor/app/asp-net-dependencies.md) más szolgáltatásokra, például REST API-kra vagy adatbázisokra irányuló hívásokat, valamint az [ügyfél kódjából](../../azure-monitor/app/javascript.md)származó Ajax-hívásokat.
* **Rendelkezésre állás** – a [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md)eredményei.

## <a name="filter-on-property-values"></a>Tulajdonság értékeinek szűrése

Az eseményeket szűrheti a tulajdonságaik értékein. Az elérhető tulajdonságok a kiválasztott eseményektől függenek. Kattintson a szűrő ikonra ![Szűrő ikon](./media/diagnostic-search/filter-icon.png) az indításhoz.

Ha úgy dönt, hogy egy adott tulajdonság egyik értéke sem ugyanaz, mint az összes érték kiválasztásával. Kikapcsolja az adott tulajdonság szűrését.

Figyelje meg, hogy a szűrési értékektől jobbra található számok azt mutatják, hogy az aktuális szűrt készletben hány előfordulás szerepel.

## <a name="find-events-with-the-same-property"></a>Azonos tulajdonságú események keresése

Ha az összes elemet ugyanazzal a tulajdonsággal szeretné megkeresni, írja be azt a keresősávba, vagy kattintson a jelölőnégyzetre a szűrő lapon a tulajdonságok megtekintésekor.

![Kattintson a tulajdonságok jelölőnégyzetére a szűrő lapon.](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Keresés az adatszolgáltatásban

> [!NOTE]
> Összetettebb lekérdezések írásához nyissa meg a [**naplók (Analitika)** ](../../azure-monitor/log-query/get-started-portal.md) elemet a Keresés panel tetején.
>

A kifejezéseket bármelyik tulajdonság értékében megkeresheti. Ez akkor hasznos, ha olyan [Egyéni eseményeket](../../azure-monitor/app/api-custom-events-metrics.md) írt be, amelyekben tulajdonságértékek szerepelnek.

Előfordulhat, hogy meg szeretné határozni az időtartományt, mivel a rövidebb hatótávolságú keresések gyorsabbak.

![Diagnosztikai keresés megnyitása](./media/diagnostic-search/search-property.png)

A teljes szavakat, nem alsztringeket is megkeresheti. Használjon idézőjeleket speciális karakterek befoglalásához.

| Sztring | *Nem* található | Található |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Egyesült Államok|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Az alábbi keresési kifejezéseket használhatja:

| Példa lekérdezésre | Hatás |
| --- | --- |
| `apple` |Minden olyan esemény megkeresése az időtartományban, amelynek mezői tartalmazzák az "Apple" szót |
| `apple AND banana` <br/>`apple banana` |Mindkét szót tartalmazó események megkeresése. A "és a" kifejezés használata nem "és". <br/>Rövid alak. |
| `apple OR banana` |Olyan események keresése, amelyek bármelyik szót tartalmaznak. Használja a "vagy a", nem "vagy" lehetőséget. |
| `apple NOT banana` |Olyan eseményeket kereshet, amelyek egyetlen szót tartalmaznak, a másikat azonban nem. |

## <a name="sampling"></a>Mintavételezés

Ha az alkalmazás nagy mennyiségű telemetria hoz létre (és a ASP.NET SDK 2.0.0-beta3 vagy újabb verzióját használja), az adaptív mintavételi modul automatikusan csökkenti a portálra küldött kötetet, és csak az események egy reprezentatív hányadát küldi el. Azonban az ugyanahhoz a kéréshez kapcsolódó események ki vannak választva vagy ki lesznek választva egy csoportként, így a kapcsolódó események között lehet navigálni.

[Ismerkedés a mintavételezéssel](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Munkaelem létrehozása

A GitHubon vagy az Azure DevOps-ban is létrehozhat egy hibát bármely telemetria-elem részleteivel.

A végpontok közötti tranzakció részletes nézetéhez kattintson bármelyik telemetria elemre, majd válassza a **munkaelem létrehozása**lehetőséget.

![Kattintson az új munkaelem lehetőségre, szerkessze a mezőket, majd kattintson az OK gombra.](./media/diagnostic-search/work-item.png)

Első alkalommal meg kell adnia egy, az Azure DevOps-szervezetre és-projektre mutató hivatkozást.

(A hivatkozást a munkaelemek lapon is megadhatja.)

## <a name="send-more-telemetry-to-application-insights"></a>További telemetria küldése Application Insights

Az Application Insights SDK által eljuttatott beépített telemetria mellett a következőket teheti:

* Rögzítse a log nyomkövetést a kedvenc naplózási keretrendszerében a [.net](../../azure-monitor/app/asp-net-trace-logs.md) -ben vagy a [Java](../../azure-monitor/app/java-trace-logs.md)-ban. Ez azt jelenti, hogy megkeresheti a napló-nyomkövetéseket, és összekapcsolhatja őket az oldalletöltések, a kivételek és a más események használatával.
* [Kód írása](../../azure-monitor/app/api-custom-events-metrics.md) egyéni események, oldalmegtekintések és kivételek küldéséhez.

[Megtudhatja, hogyan küldhet naplókat és egyéni telemetria Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>Q & A

### <a name="limits"></a>Mennyi adat van megőrzött?

Lásd a [korlátok összegzését](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hogyan tekinthetem meg az adatpostát a kiszolgálói kérelmekben?

Az adatposta automatikus naplózása nem történik meg, de a [TrackTrace vagy a log hívásokat](../../azure-monitor/app/asp-net-trace-logs.md)is használhatja. Helyezze a POST (üzenetek) paramétert az üzenetbe. Az üzenetre ugyanúgy nem szűrheti a szűrést, mint a tulajdonságok szűrésére, de a méretkorlát már nem használható.

## <a name="add"></a>Következő lépések

* [Összetett lekérdezések írása az elemzésekben](../../azure-monitor/log-query/get-started-portal.md)
* [Naplók és egyéni telemetria küldése Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Rendelkezésre állási és válaszadási tesztek beállítása](../../azure-monitor/app/monitor-web-app-availability.md)
* [Hibaelhárítás](../../azure-monitor/app/troubleshoot-faq.md)
