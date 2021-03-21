---
title: Az Azure Web App Extension kibocsátási megjegyzései – Application Insights
description: Az Azure Web Apps-bővítményre vonatkozó megjegyzések kiadása a Application Insights-vel való futtatókörnyezet-rendszerindításhoz.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 07ba61f630b849a377f1c7ba881f95518eb73606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042606"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Az Azure webalkalmazás-bővítmény kibocsátási megjegyzései Application Insights

Ez a cikk az Azure Web Apps-bővítmény kiadási megjegyzéseit tartalmazza a futtatókörnyezethez Application Insights. Ez csak az előre telepített bővítmények esetében alkalmazható.

[További információ](azure-web-apps.md) a Application Insights Azure webalkalmazás-bővítményéről.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- A bővítmény aktuális verziójának megkeresése
    - Nyissa meg a következőt: `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. További információkért tekintse meg [a bővítmény/ügynök alapú figyelés részletes hibaelhárítási útmutatóját](./azure-web-apps.md?tabs=net#troubleshooting) .

- Mi a teendő, ha privát bővítményeket használok?
    - Távolítsa el a Private site Extensions szolgáltatást, mert már nem támogatott.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2838"></a>2.8.38

- JAVA-bővítmény: frissítve a [Java Agent 3.0.2 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) a 2.5.1-ből.
- Node.js bővítmény: frissített AI SDK- [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) a 1.8.7-ből.
- .NET Core: eltávolított támogatott verziók (2,0, 2,2, 3,0). A támogatott verziók a következők: 2,1 és 3,1.

### <a name="2837"></a>2.8.37

- AppSvc Windows-bővítmény: a .net Core a System.Diagnostics.DiagnosticSource.dll bármely verziójával működik.

### <a name="2836"></a>2.8.36

- AppSvc Windows-bővítmény: enabled Inter-op az AI SDK-val a .NET Core-ban.

### <a name="2835"></a>2.8.35

- AppSvc Windows-bővítmény: a .NET Core 3,1-támogatás hozzáadva.

### <a name="2833"></a>2.8.33

- .NET, .NET Core, Java és Node.js ügynökök és a Windows-bővítmény: a szuverén felhők támogatása. A kapcsolati karakterláncok a szuverén felhőkbe való adatküldéshez használhatók.

### <a name="2831"></a>2.8.31

- ASP.NET Core ügynök: a frissített Application Insights SDK-referenciák egyikével kapcsolatos kijavított probléma (lásd: 2.8.26 kapcsolatos ismert problémák). Ha a nem megfelelő verziója `System.Diagnostics.DiagnosticSource.dll` már be van töltve a futtatókörnyezettel, a kód nélküli bővítmény most nem fogja összeomlani az alkalmazást, és kikapcsolja a biztonsági mentést. A probléma által érintett ügyfelek esetében javasoljuk, hogy távolítsa el a `System.Diagnostics.DiagnosticSource.dll` mappát a bin mappából, vagy használja a bővítmény régebbi verzióját a "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24" beállítással, ellenkező esetben az alkalmazás figyelése nincs engedélyezve.

### <a name="2826"></a>2.8.26

- ASP.NET Core ügynök: a frissített Application Insights SDK-val kapcsolatos kijavított probléma. Az ügynök nem próbálkozik a betöltéssel, `AiHostingStartup` Ha a ApplicationInsights.dll már szerepel a bin mappában. Ez a szerelvényen keresztül feloldja a reflexióval kapcsolatos problémákat \<AiHostingStartup\> . GetTypes().
- Ismert problémák: a kivétel akkor `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` fordulhat elő, ha a DLL egy másik verziója be `DiagnosticSource` van töltve. Ez például akkor fordulhat elő, ha `System.Diagnostics.DiagnosticSource.dll` a közzétételi mappában szerepel. A megoldásként használja a bővítmény előző verzióját az Alkalmazásbeállítások az App Servicesben való beállításával: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- A 2.8.21 újracsomagolt verziója.

### <a name="2823"></a>2.8.23

- ASP.NET Core 3,0 kód-figyelési támogatás hozzáadva.
- Frissítve ASP.NET Core SDK- [t a](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) 2,1-es, 2,2-as és 3,0-es futtatókörnyezeti verzióra. A .NET Core 2,0-ot célzó alkalmazások továbbra is használják az SDK 2.1.1-es számát.

### <a name="2814"></a>2.8.14

- Frissített ASP.NET Core SDK-verziót a 2.3.0-ről a legújabb verzióra (2.6.1) a .NET Core 2,1, 2,2 rendszerre irányuló alkalmazásokhoz. A .NET Core 2,0-ot célzó alkalmazások továbbra is használják az SDK 2.1.1-es számát.

### <a name="2812"></a>2.8.12

- ASP.NET Core 2,2 alkalmazások támogatása.
- Javítva lett egy hiba a ASP.NET Core bővítményben, ami az SDK injektálását okozza, még akkor is, ha az alkalmazás már fel van szerelve az SDK-val. Az 2,1-es és a 2,2-es alkalmazások esetében az alkalmazás mappájában lévő ApplicationInsights.dll jelenléte mostantól a bővítmény visszaállítását eredményezi. Az 2,0-es alkalmazások esetében a bővítmény csak akkor áll vissza, ha a ApplicationInsights `UseApplicationInsights()` hívással van engedélyezve.

- A ASP.NET Core-alkalmazások hiányos HTML-válaszának állandó javítása. Ez a javítás mostantól kibővült a .NET Core 2,2-alkalmazások működéséhez.

- A ASP.NET Core-alkalmazások JavaScript-befecskendezésének kikapcsolására szolgáló támogatás hozzáadva ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). A ASP.NET Core esetében a JavaScript-injektálás alapértelmezés szerint "opt-out" módban van, kivéve, ha explicit módon ki van kapcsolva. (Az alapértelmezett beállítás a jelenlegi viselkedés megőrzése érdekében történik.)

- Rögzített ASP.NET Core bővítményi hiba, amely akkor is okozta a befecskendezést, ha a rendszerállapotkulcsot nem volt jelen.
- Kijavítva egy olyan hibát az SDK-verzió előtagjának logikájában, amely a telemetria-ben helytelen SDK-verziót okozott.

- A ASP.NET Core-alkalmazások SDK-verziójának előtagja a telemetria gyűjtési módjának azonosításához.
- Rögzített SCM-ApplicationInsights lap az előre telepített bővítmény verziójának megfelelő megjelenítéséhez.

### <a name="2810"></a>2.8.10

- Javítsa ki az ASP.NET Core-alkalmazások hiányos HTML-válaszát.

## <a name="next-steps"></a>Következő lépések

- Az Azure App Services figyelésének konfigurálásával kapcsolatos további információkért tekintse meg az [Azure app Service dokumentációját](azure-web-apps.md) . 
