---
title: Az Azure Functions monitorozása
description: Ismerje meg, hogyan használható az Azure Application Insights és a Azure Functions a függvények végrehajtásának figyelésére.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550759"
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

A [Azure functions](functions-overview.md) az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) beépített integrációját kínálja a függvények figyelésére. Ez a cikk áttekintést nyújt az Azure által az Azure Functions figyelésére biztosított figyelési lehetőségekről.

Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. A teljesítménnyel kapcsolatos rendellenességek automatikus észlelése és hatékony elemzési eszközök használata révén könnyebben diagnosztizálhatja a problémákat, és jobban megismerheti a függvények használatát. Ezek az eszközök úgy vannak kialakítva, hogy segítsenek a függvények teljesítményének és használhatóságának folyamatos fejlesztésében. Application Insights is használhatja a helyi funkció alkalmazás-projekt fejlesztése során. További információ: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

Mivel a Application Insights a rendszerállapot Azure Functionsbe van építve, érvényes kialakítási kulcsra van szükség ahhoz, hogy a Function alkalmazást egy Application Insights erőforráshoz lehessen kapcsolni. A rendszer a kialakítási kulcsot hozzáadja az alkalmazás beállításaihoz, amikor létrehozza a Function app-erőforrást az Azure-ban. Ha a Function alkalmazás még nem rendelkezik ezzel a kulccsal, [manuálisan is beállíthatja](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Díjszabás és korlátozások Application Insights

Kipróbálhatja a Application Insights-integrációt a Azure Functions ingyenes szolgáltatással, amely napi korlátot biztosít az adatfeldolgozáshoz.

Ha a fejlesztés során engedélyezi az alkalmazások vizsgálatát, akkor a tesztelés során ezt a korlátot is elérheti. Az Azure portál-és e-mail-értesítéseket biztosít, ha közeledik a napi korláthoz. Ha kihagyja ezeket a riasztásokat, és lenyomja a korlátot, az új naplók nem jelennek meg Application Insights lekérdezésekben. Ügyeljen arra, hogy a szükségtelen hibaelhárítási idő elkerülhető legyen. További információ: [a díjszabás és az adatmennyiség kezelése Application Insightsban](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights tartalmaz egy [mintavételi](../azure-monitor/app/sampling.md) funkciót, amely képes arra, hogy túl sok telemetria-adatmennyiséget állítson elő a befejezett végrehajtásokon a maximális terhelés idején. A mintavétel alapértelmezés szerint engedélyezve van. Ha úgy tűnik, hogy hiányoznak az adatok, előfordulhat, hogy módosítania kell a mintavételi beállításokat, hogy az megfeleljen az adott figyelési forgatókönyvnek. További információ: a [mintavételezés konfigurálása](configure-monitoring.md#configure-sampling).

A Function app számára elérhető Application Insights szolgáltatások teljes listáját a [Azure functions által támogatott funkciók Application Insightsjában](../azure-monitor/app/azure-functions-supported-features.md)részletesen ismertetjük.

## <a name="application-insights-integration"></a>Application Insights integráció

A Function app létrehozásakor általában Application Insights példányt kell létrehoznia. Ebben az esetben az integrációhoz szükséges kialakítási kulcs már be van állítva egy *APPINSIGHTS_INSTRUMENTATIONKEY* nevű Alkalmazásbeállítás. Ha valamilyen okból kifolyólag a Function alkalmazás nem rendelkezik a kialakítási kulccsal, [engedélyeznie kell Application Insights integrációt](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Telemetria-adatok gyűjtése

Ha a Application Insights integráció engedélyezve van, a rendszer a telemetria adatait a csatlakoztatott Application Insights-példányba küldi. Ezek az adatok tartalmazzák a functions gazdagép által létrehozott naplókat, a függvények kódjából írt nyomkövetéseket és a teljesítményadatokat. 

>[!NOTE]
>A függvények és a functions-gazdagép adatain kívül adatokat is gyűjthet a [functions Scale vezérlőről](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Naplózási szintek és kategóriák

Amikor nyomkövetéseket ír az alkalmazás kódjából, egy naplózási szintet kell rendelnie a nyomkövetésekhez. A naplózási szintek lehetővé teszik a nyomkövetési adatok mennyiségének korlátozását.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

További információ a naplózási szintekről: a [naplózási szintek konfigurálása](configure-monitoring.md#configure-log-levels).

A naplózott elemek kategóriához rendelésével nagyobb mértékben szabályozhatja a telemetria meghatározott forrásaiból generált adatokat. A kategóriák megkönnyítik az elemzések az összegyűjtött adatokon keresztüli futtatását. A függvény kódjából írt nyomokat az egyes kategóriákhoz rendeli a függvény neve alapján. További információ a kategóriákról: a [Kategóriák konfigurálása](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Egyéni telemetria-adatértékek

A [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) és a [JavaScript](functions-reference-node.md#log-custom-telemetry)alkalmazásban egy Application Insights SDK használatával írhat egyéni telemetria-fájlokat.

### <a name="dependencies"></a>Függőségek

A függvények 2. x-es verziójától kezdve a futtatókörnyezet automatikusan gyűjti az adatokat a függőségekről bizonyos ügyféloldali SDK-kat használó kötések esetében. Application Insights adatokat gyűjt a következő függőségekről:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage-szolgáltatások (blob, üzenetsor és tábla)

A rendszer a HTTP-kérelmeket és az adatbázis-hívásokat `SqlClient` is rögzíti. Az Application Insights által támogatott függőségek teljes listájáért lásd: [automatikusan követett függőségek](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights az összegyűjtött függőségi adatokhoz tartozó _alkalmazás-hozzárendelést_ hoz létre. A következőkben egy példa látható egy HTTP-trigger függvény egy üzenetsor-tároló kimeneti kötéssel való hozzárendelésére.  

![Alkalmazás-hozzárendelés függőséggel](./media/functions-monitoring/app-map.png)

A függőségek a szinten vannak írva `Information` . Ha a vagy újabb értékre szűr `Warning` , nem fog megjelenni a függőségi információk. Emellett a függőségek automatikus összegyűjtése nem felhasználói hatókörben történik. A függőségi adatmennyiség rögzítéséhez győződjön meg arról, hogy a szint legalább `Information` a felhasználói hatókörön kívülre van beállítva a `Function.<YOUR_FUNCTION_NAME>.User` gazdagépen.

Az automatikus függőségi adatgyűjtés mellett a nyelvspecifikus Application Insights SDK-k egyikét is használhatja, hogy egyéni függőségi adatokat írjon a naplókba. Az egyéni függőségek írására példaként tekintse meg a következő nyelvspecifikus példák egyikét:

+ [Egyéni telemetria naplózása C#-függvényekben](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Egyéni telemetria naplózása JavaScript-függvényekben](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Naplókba való írás 

A naplókba írás és a használt API-k a Function app-projekt nyelvétől függenek.   
A függvényekből származó naplók írásához tekintse meg a fejlesztői útmutatót a nyelvhez.

+ [C# (.NET-osztály könyvtára)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Adatok elemzése

Alapértelmezés szerint a függvény alkalmazásból gyűjtött adatokat Application Insights tárolja a rendszer. A [Azure Portal](https://portal.azure.com)Application Insights a telemetria-adatvizualizációk széles körét biztosítja. A hibák naplózása és a lekérdezési események és mérőszámok részletezése is megtehető. További információ: az összegyűjtött adatok megtekintésének és lekérdezésének alapvető példái az [Azure functions telemetria elemzése](analyze-telemetry-data.md)című témakörben Application Insights. 

## <a name="streaming-logs"></a>Folyamatos átviteli naplók

Egy alkalmazás fejlesztése során gyakran érdemes megtekinteni, hogy mi történik a naplókba közel valós időben, amikor az Azure-ban fut.

Kétféle módon lehet megtekinteni a függvények végrehajtásával létrejövő naplózási adat streamjét.

* **Beépített log streaming**: a app Service platformon megtekintheti az alkalmazás naplófájljainak streamjét. Ez az adatfolyam megegyezik azzal a kimenettel, amelyet a függvények hibakeresése során észlelt a [helyi fejlesztés](functions-develop-local.md) során, és amikor a portálon a **test (teszt** ) lapot használja. Megjelenik az összes napló alapú információ. További információ: stream- [naplók](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ez a folyamatos átviteli módszer csak egyetlen példányt támogat, és nem használható a Linux rendszeren futó alkalmazással egy használati tervben.

* **Élő metrikastream**: Ha a function alkalmazás [Application Insightshoz csatlakozik](configure-monitoring.md#enable-application-insights-integration), a Azure Portal a [élő metrikastream](../azure-monitor/app/live-stream.md)használatával megtekintheti a naplózási adatokat és az egyéb mérőszámokat közel valós időben. Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó figyelési funkciót használ a használati tervben. Ez a metódus [mintavételes adathalmazt](configure-monitoring.md#configure-sampling)használ.

A naplózási streamek a Portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. A naplózási adatfolyamok engedélyezésével kapcsolatos további információkért lásd: [streaming-végrehajtási naplók engedélyezése Azure Functionsban](streaming-logs.md).

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

_Ez a funkció előzetes verzióban érhető el._ 

Application Insights lehetővé teszi a telemetria-adatexportálást a hosszú távú tárolás vagy más Analysis Services szolgáltatásba.  

Mivel a függvények a Azure Monitor is integrálva vannak, a diagnosztikai beállítások használatával különböző célhelyekre is küldhet telemetria-fájlokat, beleértve a Azure Monitor naplókat is. További információ: [Azure functions figyelése Azure monitor naplókkal](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>A vezérlő naplófájljainak méretezése

_Ez a funkció előzetes verzióban érhető el._ 

A [Azure functions skálázási vezérlő](./event-driven-scaling.md#runtime-scaling) figyeli a Azure functions gazdagép azon példányait, amelyeken az alkalmazás fut. Ez a vezérlő döntéseket hoz a példányok hozzáadásához vagy eltávolításához az aktuális teljesítmény alapján. A méretezési vezérlő kibocsátja a naplókat a Application Insightsba, hogy jobban megértse a méretezési vezérlő által a Function alkalmazásra vonatkozó döntéseket. A blob Storage-ban létrehozott naplókat egy másik szolgáltatás általi elemzés céljából is tárolhatja. 

A szolgáltatás engedélyezéséhez vegyen fel egy nevű Alkalmazásbeállítás `SCALE_CONTROLLER_LOGGING_ENABLED` -beállítást a Function app-beállításokhoz. További információ: a [méretezési vezérlő naplófájljainak konfigurálása](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Problémák bejelentése

Ha Application Insights-integrációval kapcsolatos problémát szeretne jelenteni a functions szolgáltatásban, vagy javaslatot vagy kérést szeretne készíteni, [hozzon létre egy problémát a githubban](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core naplózás](/aspnet/core/fundamentals/logging/)
