---
title: Azure Application Insights-alkalmazásadatok megtekintése | Microsoft Docs
description: A Application Insights Connector megoldással diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat, és megtudhatja, hogy a felhasználók hogyan használják az alkalmazást a Application Insights figyelésével.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: d0cfca44878130e870c633040afcfbdd55ba8b7b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396546"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Connector felügyeleti megoldás (elavult)

![Application Insights szimbólum](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Az [erőforrások közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md)támogatásával a Application Insights Connector felügyeleti megoldás már nem szükséges. Az Azure piactéren elavult és eltávolított, valamint a OMS-portálon, amely az Azure-beli kereskedelmi felhő esetében hivatalosan elavult, 2019. 2019. március 30-án megszűnik az Azure US government Cloud.
>
>A meglévő kapcsolatok 2019 június 30-ig továbbra is működni fognak.  A OMS-portál elavult használata esetén nincs lehetőség a meglévő kapcsolatok konfigurálására és eltávolítására a portálon. A meglévő kapcsolatok eltávolításához lásd: [az összekötő eltávolítása](#removing-the-connector-with-powershell) az alábbi PowerShell-lel a PowerShell használatával.
>
>Több alkalmazás naplózási információinak lekérdezéséhez Application Insights tekintse meg a [több Azure Monitor Application Insights erőforrás egységesítését](../log-query/unify-app-resource-data.md)ismertető témakört. További információ a OMS-portál érvénytelenítéséről: a [OMS-portál áthelyezése az Azure](../../azure-monitor/platform/oms-portal-transition.md)-ba.
>
> 

Az Applications-alapú adatelemzési összekötő megoldásával diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat, és megismerheti, hogy a felhasználók milyen műveleteket végeznek az alkalmazással, ha [Application Insights](../../azure-monitor/app/app-insights-overview.md)figyelik. Ugyanazon alkalmazás-telemetria nézetei, amelyekben a fejlesztők megtekintenek a Application Insightsban Log Analytics érhetők el. Ha azonban a Application Insights-alkalmazásait Log Analytics integrálja, az alkalmazások láthatósága egyre nagyobb mértékben megnövekszik azzal, hogy a művelet-és alkalmazásadatok egy helyen vannak. Ugyanezek a nézetek segítenek az alkalmazás-fejlesztőknek való együttműködésben. A közös nézetek segítségével csökkentheti az alkalmazás-és platformokkal kapcsolatos problémák észlelésének és megoldásának idejét.

A megoldás használatakor a következőket teheti:

- Egyetlen helyen tekintheti meg az összes Application Insights alkalmazást, még akkor is, ha azok különböző Azure-előfizetésekben találhatók
- Az infrastruktúra-adathalmazok korrelációja az alkalmazásadatok között
- Alkalmazásadatok megjelenítése a naplóbeli keresés perspektívái alapján
- Kimutatás Log Analytics adatokról a Application Insights alkalmazásba az Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Összekapcsolt források

A legtöbb más Log Analytics-megoldástól eltérően az ügynökök nem gyűjtenek adatokat az Application Insights Connector. A megoldás által használt összes adatok közvetlenül az Azure-ból származnak.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | A megoldás nem gyűjt adatokat a Windows-ügynököktől. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás nem gyűjt adatokat a Linux-ügynököktől. |
| [SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Nem | A megoldás nem gyűjt adatokat a csatlakoztatott SCOM felügyeleti csoportba tartozó ügynököktől. |
| [Azure Storage-fiók](collect-azure-metrics-logs.md) | Nem | A megoldás nem gyűjt adatokat az Azure Storage-ból. |

## <a name="prerequisites"></a>Előfeltételek

- Application Insights Connector információk eléréséhez Azure-előfizetéssel kell rendelkeznie
- Legalább egy konfigurált Application Insights erőforrással kell rendelkeznie.
- A Application Insights erőforrás tulajdonosának vagy közreműködőnek kell lennie.

## <a name="configuration"></a>Konfiguráció

1. Engedélyezze a Azure Web Apps Analytics megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) -en, vagy használja az [Solutions Gallery log Analytics-megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című témakörben leírt eljárást.
2. Keresse fel az [Azure Portalt](https://portal.azure.com). Application Insights megnyitásához válassza **az összes szolgáltatás** elemet. Ezután keressen rá Application Insights. 
3. Az **előfizetések**területen válasszon ki egy Application Insights erőforrásokkal rendelkező előfizetést, majd a **név**területen válasszon ki egy vagy több alkalmazást.
4. Kattintson a **Mentés** gombra.

Körülbelül 30 perc alatt az adatok elérhetővé válnak, és a Application Insights csempe frissül az adatokkal, például a következő képpel:

![Application Insights csempe](./media/app-insights-connector/app-insights-tile.png)

További tudnivalók:

- Application Insights alkalmazásokat csak egyetlen Log Analytics munkaterülethez csatolhat.
- Log Analyticshoz csak az [alapszintű vagy a Nagyvállalati Application Insights erőforrásokat](https://azure.microsoft.com/pricing/details/application-insights) lehet összekapcsolni. A Log Analytics ingyenes szintjét azonban használhatja.

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás nem telepít felügyeleti csomagokat a csatlakoztatott felügyeleti csoportokban.

## <a name="use-the-solution"></a>A megoldás használata

A következő szakaszok azt ismertetik, hogyan használhatók a Application Insights irányítópultján látható pengék az alkalmazások adatainak megtekintéséhez és használatához.

### <a name="view-application-insights-connector-information"></a>Application Insights Connector információk megtekintése

A **Application Insights** csempére kattintva nyissa meg a **Application Insights** irányítópultot, és tekintse meg a következő pengéket.

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash02.png)

Az irányítópulton szerepelnek a táblázatban látható pengék. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. Futtathat egy olyan naplóbeli keresést, amely az összes rekordot visszaadja, amikor az **összes** megjelenítése gombra kattint a panel alján, vagy amikor rákattint a panel fejlécére.


| **Oszlop** | **Leírás** |
| --- | --- |
| Alkalmazások – alkalmazások száma | Az alkalmazás erőforrásaiban található alkalmazások számát jeleníti meg. Az alkalmazások neveinek, valamint az alkalmazások rekordjainak számát is felsorolja. Kattintson a számra a <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> naplózásának futtatásához <br><br>  Kattintson egy alkalmazás nevére, és futtassa az alkalmazás rekordjait a gazdagépen, a telemetria típusú rekordok és az összes adat típus szerint (az elmúlt nap alapján). |
| Adatmennyiség – adatok küldésére szolgáló gazdagépek | Az adatokat küldő számítógép-gazdagépek számát jeleníti meg. Az egyes gazdagépek számítógép-gazdagépeit és rekordjainak darabszámát is felsorolja. Kattintson a számra a <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> naplózásának futtatásához <br><br> Kattintson egy számítógépnévre, és futtassa a naplót a gazdagépen, amely a gazdagépen, a rekordok szerint telemetria és az összes adat típus szerint (az elmúlt nap alapján) jeleníti meg az adatokat. |
| Rendelkezésre állás – webteszt eredményei | Megjelenít egy perecdiagram a webes tesztek eredményeihez, amely jelzi a pass vagy a Fail argumentumot. Kattintson a diagramra, és futtassa a naplóbeli keresést <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Az eredmények az összes teszt menetének és hibáinak számát mutatják. Ez az összes Web Apps az elmúlt percben forgalmas forgalmat mutatja. Az alkalmazás nevére kattintva megtekintheti a naplóbeli keresést, amely a sikertelen webes tesztek részleteit jeleníti meg. |
| Kiszolgálói kérelmek – kérelmek/óra | Megjeleníti a kiszolgálói kérelmek óránkénti sorát a különböző alkalmazásokhoz. Vigye a kurzort a diagram egyik sorára, és tekintse meg az adott időpontra vonatkozó kérelmeket fogadó első 3 alkalmazást. A kérelmeket fogadó alkalmazások és a kijelölt időszakra vonatkozó kérelmek számát is megjeleníti. <br><br>Kattintson a gráfra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely a különböző alkalmazások óránkénti kiszolgálói kéréseinek részletesebb táblázatát jeleníti meg. <br><br> Kattintson egy alkalmazásra a listában egy olyan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> naplójának kereséséhez, amely megjeleníti a kérelmek listáját, a kérelmekre vonatkozó diagramokat, valamint a kérelmek időtartamát és a kérelmekre adott válaszok listáját.   |
| Hibák – sikertelen kérelmek/óra | Megjeleníti a sikertelen alkalmazás-kérelmek óránkénti diagramját. Vigye az egérmutatót a diagram fölé, és tekintse meg az első 3 alkalmazást egy adott időpontra vonatkozó sikertelen kérelmekkel. Az egyes alkalmazásokhoz tartozó sikertelen kérelmek számát tartalmazó alkalmazások listáját is megjeleníti. Kattintson a diagramra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely a sikertelen kérelmek részletesebb táblázatát jeleníti meg. <br><br>Kattintson a lista egyik elemére a sikertelen kérelmeket, a sikertelen kérelmekre vonatkozó diagramokat és a kérelmek időtartamát, valamint a sikertelen kérelmekre vonatkozó hibakódok listáját futtató <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> naplójának kereséséhez. |
| Kivételek – kivételek óránként | Megjeleníti a kivételek óránkénti grafikonját. Vigye az egérmutatót a diagram fölé, és tekintse meg az első 3 alkalmazást, amely egy adott időpontra vonatkozó kivételeket mutat. Megjeleníti az alkalmazások listáját is, amely az egyes kivételek számát tartalmazza. Kattintson a diagramra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely részletesebb diagramot jelenít meg a kivételekről. <br><br>Kattintson a lista egyik elemére egy olyan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> naplójának kereséséhez, amely megjeleníti a kivételek listáját, a kivételek diagramját az idő és a sikertelen kérelmek esetében, valamint a kivételek listáját.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>A Application Insights perspektívájának megtekintése a naplóbeli kereséssel

Ha az irányítópult bármely elemére kattint, megjelenik egy Application Insights perspektíva a keresésben. A perspektíva kiterjesztett vizualizációt biztosít a kiválasztott telemetria-típus alapján. Így a vizualizációk tartalma megváltozik a különböző telemetria-típusoknál.

Ha az alkalmazások panelen bárhová kattint, megjelenik az alapértelmezett **alkalmazások** perspektívája.

![Application Insights alkalmazások perspektívája](./media/app-insights-connector/applications-blade-drill-search.png)

A perspektíva a kiválasztott alkalmazás áttekintését jeleníti meg.

A **rendelkezésre állás** panel egy másik nézőpont-nézetet mutat be, ahol megtekintheti a webes tesztek eredményeit és a kapcsolódó sikertelen kérelmeket.

![Application Insights rendelkezésre állási perspektíva](./media/app-insights-connector/availability-blade-drill-search.png)

Ha a **kiszolgálói kérelmek** vagy **hibák** paneleken bárhová kattint, a perspektíva összetevői változnak, hogy a kérésekhez kapcsolódó vizualizációt adjanak.

![Application Insights hibák panel](./media/app-insights-connector/server-requests-failures-drill-search.png)

Ha a **kivételek** panelen bárhová kattint, a rendszer a kivételekhez igazított vizualizációt jeleníti meg.

![Application Insights kivételek panel](./media/app-insights-connector/exceptions-blade-drill-search.png)

Függetlenül attól, hogy rákattint-e a **Application Insights Connector** -irányítópultra, a **keresési** oldalon belül minden olyan lekérdezés, amelyik Application Insights az adatmennyiséget adja vissza, megjeleníti a Application Insights perspektívát. Ha például Application Insights-adatok megtekintését szeretné megtekinteni **&#42;** , egy lekérdezés a perspektíva fület is megjeleníti a következő képhez hasonlóan:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

A perspektíva összetevői a keresési lekérdezéstől függően frissülnek. Ez azt jelenti, hogy az eredményeket bármely olyan keresési mező használatával szűrheti, amely lehetővé teszi az adatok megtekintését:

- Minden alkalmazás
- Egyetlen kiválasztott alkalmazás
- Alkalmazások csoportja

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Kimutatás egy alkalmazáshoz a Azure Portal

A Application Insights Connector-lapok úgy lettek kialakítva, hogy a *Azure Portal használatakor*a kiválasztott Application Insights alkalmazáshoz lehessen eldönteni. A megoldás magas szintű figyelési platformként használható, amely segít az alkalmazások hibakeresésében. Ha valamelyik csatlakoztatott alkalmazásban potenciális probléma jelenik meg, akkor Log Analytics kereshet, vagy közvetlenül a Application Insights alkalmazáshoz is elvégezhető.

A kimutatáshoz kattintson az egyes sorok végén megjelenő három pontra ( **...** ), majd válassza a **Megnyitás Application Insightsban**lehetőséget.

>[!NOTE]
>A **megnyitás Application Insightsban** nem érhető el a Azure Portal.

![Megnyitás Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Minta – javított adatértékek

A Application Insights *[mintavételi korrekciót](../../azure-monitor/app/sampling.md)* biztosít a telemetria-forgalom csökkentése érdekében. Ha engedélyezi a mintavételezést a Application Insights alkalmazásban, a Application Insights és a Log Analyticsban tárolt bejegyzések száma kevesebb. Míg az adatkonzisztencia megmarad a **Application Insights Connector** oldalon és perspektívákban, manuálisan kell megadnia az egyéni lekérdezésekhez tartozó mintavételes adatok helyességét.

Itt látható egy példa a naplóbeli keresési lekérdezésben a mintavételezési korrekcióra:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

A **mintavételes darabszám** mező minden bejegyzésben megtalálható, és a bejegyzés által reprezentált adatpontok számát jeleníti meg. Ha bekapcsolja a mintavételezést a Application Insights alkalmazáshoz, a mintavételi **szám** nagyobb, mint 1. Az alkalmazás által generált bejegyzések tényleges számának megszámlálásához összesítse a **mintavételes darabszám** mezőket.

A mintavételezés csak az alkalmazás által generált bejegyzések teljes számát befolyásolja. Nem kell kijavítania a mintavételezést a metrika mezőihez (például **RequestDuration** vagy **AvailabilityDuration** ), mert ezek a mezők a megjelenített bejegyzések átlagát mutatják.

## <a name="input-data"></a>Bemeneti adatok

A megoldás a következő telemetria-típusokat fogadja a csatlakoztatott Application Insights-alkalmazásokból:

- Elérhetőség
- Kivételek
- Kérelmek
- Oldalletöltések – a munkaterülete számára az oldalmegtekintések fogadásához konfigurálnia kell az alkalmazásokat az információk összegyűjtéséhez. További információ: [oldalmegtekintések](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Egyéni események – a munkaterület egyéni események fogadásához konfigurálnia kell az alkalmazásokat az információk összegyűjtéséhez. További információ: [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Az adatok a Log Analyticstól érkeznek a Application Insights, mert elérhetővé válnak.

## <a name="output-data"></a>Kimeneti adatok

Minden típusú bemeneti adathoz létrejön egy *ApplicationInsights* *típusú* rekord. A ApplicationInsights-rekordok tulajdonságai az alábbi fejezetekben láthatók:

### <a name="generic-fields"></a>Általános mezők

| Tulajdonság | Leírás |
| --- | --- |
| Type (Típus) | ApplicationInsights |
| Ügyfélip |   |
| TimeGenerated | A rekord létrehozásának időpontja |
| ApplicationId | A Application Insights alkalmazás kialakítási kulcsa |
| ApplicationName | A Application Insights alkalmazás neve |
| RoleInstance | A kiszolgáló gazdagépének azonosítója |
| DeviceType | Ügyféleszközök |
| ScreenResolution |   |
| Kontinens | Az a kontinens, ahol a kérelem származik |
| Ország/régió | Az ország/régió, ahol a kérés származik |
| Tartomány | Tartomány, állam vagy területi beállítás, ahol a kérelem származik |
| Város | A kérést kezdeményező város vagy város |
| isSynthetic | Azt jelzi, hogy a kérést egy felhasználó vagy egy automatikus metódus hozta-e létre. True = automatizált metódus vagy hamis = felhasználó által generált |
| SamplingRate | A portálra eljuttatott SDK által generált telemetria százalékos aránya. Tartomány 0,0 – 100.0. |
| SampledCount | 100/(SamplingRate). Például: 4 =&gt; 25% |
| IsAuthenticated | Igaz vagy hamis |
| OperationID | Az azonos műveleti AZONOSÍTÓval rendelkező elemek a portálon kapcsolódó elemként jelennek meg. Általában a kérelem azonosítója |
| ParentOperationID | A szülő művelet azonosítója |
| OperationName |   |
| SessionId | A kérést létrehozó munkamenet egyedi azonosítására szolgáló GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Rendelkezésre állásra vonatkozó mezők

| Tulajdonság | Leírás |
| --- | --- |
| TelemetryType | Elérhetőség |
| AvailabilityTestName | A webes teszt neve |
| AvailabilityRunLocation | Http-kérelem földrajzi forrása |
| AvailabilityResult | A webes teszt sikerességének eredményét jelzi. |
| AvailabilityMessage | A webes teszthez csatolt üzenet |
| AvailabilityCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| DataSizeMetricValue | 1,0 vagy 0,0 |
| DataSizeMetricCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| AvailabilityDuration | A webes teszt időtartamának ideje (ezredmásodpercben) |
| AvailabilityDurationCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | A webes teszt egyedi GUID azonosítója |
| AvailabilityTimestamp | A rendelkezésre állási teszt pontos időbélyege |
| AvailabilityDurationMin | A mintául szolgáló rekordok esetében ebben a mezőben a képmegjelenített adatpontok minimális webes tesztelési időtartama (ezredmásodperc) látható. |
| AvailabilityDurationMax | A mintául szolgáló rekordok esetében ebben a mezőben a megjelenített adatpontok maximális webes tesztelési időtartama (ezredmásodperc) látható. |
| AvailabilityDurationStdDev | A mintául szolgáló rekordok esetében ebben a mezőben az összes webes teszt időtartama (ezredmásodpercben) közötti szórás látható a képviselt adatpontok esetében. |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Kivétel-specifikus mezők

| Type (Típus) | ApplicationInsights |
| --- | --- |
| TelemetryType | Kivétel |
| ExceptionType | A kivétel típusa |
| ExceptionMethod | A kivételt létrehozó metódus |
| ExceptionAssembly | A szerelvény tartalmazza a keretrendszert és a verziót, valamint a nyilvános kulcs tokenjét. |
| ExceptionGroup | A kivétel típusa |
| ExceptionHandledAt | Azt a szintet jelzi, amely a kivételt kezelte |
| ExceptionCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| ExceptionMessage | A kivétel üzenete |
| ExceptionStack | A kivétel teljes vereme |
| ExceptionHasStack | Igaz, ha a kivétel veremmel rendelkezik |



### <a name="request-specific-fields"></a>Kérelem-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| Type (Típus) | ApplicationInsights |
| TelemetryType | Kérelem |
| ResponseCode | Az ügyfélnek küldött HTTP-válasz |
| RequestSuccess | Sikert vagy hibát jelez. Igaz vagy hamis. |
| Kérelemazonosító | AZONOSÍTÓ a kérelem egyedi azonosításához |
| RequestName | GET/POST + URL-cím alapja |
| RequestDuration | A kérelem időtartamának ideje (másodpercben) |
| URL-cím | A kérelem URL-címe nem tartalmazza a gazdagépet |
| Gazdagép | Webkiszolgáló-gazdagép |
| URLBase | A kérelem teljes URL-címe |
| ApplicationProtocol | Az alkalmazás által használt protokoll típusa |
| RequestCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| RequestDurationCount | 100/(mintavételi sebesség). Például: 4 =&gt; 25% |
| RequestDurationMin | A mintául szolgáló rekordok esetében ebben a mezőben a bejelölt adatpontok minimális kérésének időtartama (ezredmásodpercben) látható. |
| RequestDurationMax | A mintául szolgáló rekordok esetében ebben a mezőben a kérelmek maximális időtartama (ezredmásodpercben) látható a képviselt adatpontok esetében. |
| RequestDurationStdDev | A mintául szolgáló rekordok esetében ebben a mezőben az összes kérelem időtartama (ezredmásodpercben) közötti szórás látható a képviselt adatpontok esetében. |

## <a name="sample-log-searches"></a>Naplókeresési minták

Ez a megoldás nem rendelkezik az irányítópulton megjelenített minta napló-keresések készletével. A példákat tartalmazó naplóbeli keresési lekérdezések azonban a [Application Insights Connector információk megtekintése](#view-application-insights-connector-information) szakaszban láthatók.

## <a name="removing-the-connector-with-powershell"></a>Az összekötő eltávolítása a PowerShell-lel
A OMS-portál elavult használata esetén nincs lehetőség a meglévő kapcsolatok konfigurálására és eltávolítására a portálon. A meglévő kapcsolatokat a következő PowerShell-parancsfájl használatával távolíthatja el. A művelet végrehajtásához a munkaterület tulajdonosának vagy közreműködője, Application Insights erőforrás-olvasójának kell lennie.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Az alkalmazások listáját az alábbi PowerShell-parancsfájl segítségével kérheti le, amely egy REST API hívást hív meg. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
A szkriptnek rendelkeznie kell egy tulajdonosi hitelesítési jogkivonattal a Azure Active Directory való hitelesítéshez. A token lekérésének egyik módja a [REST API dokumentációs webhelyén](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)található cikk használata. Kattintson a **kipróbálás** gombra, és jelentkezzen be az Azure-előfizetésbe. A tulajdonosi jogkivonatot a **kérelem előzetes** verziójából másolhatja, ahogy az alábbi képen is látható.


![Tulajdonosi jogkivonat](media/app-insights-connector/bearer-token.png)


Lekérheti az alkalmazások listáját is, ha egy napló lekérdezést használ:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Következő lépések

- A Application Insights-alkalmazások részletes adatainak megtekintéséhez használja a [log Search kifejezést](../../azure-monitor/log-query/log-query-overview.md) .
