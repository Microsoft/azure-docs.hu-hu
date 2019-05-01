---
title: Nézet közötti váltás és a szerkesztési mód a jelentések a Power BI-Munkaterületcsoportok |} A Microsoft Docs
description: Ismerje meg, hogyan nézet közötti váltás és a szerkesztési mód a jelentések a Power BI-Munkaterületcsoportok belül.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: cab5d067387fcffe4f62b94e06960afe47d5f00c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702691"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Nézet közötti váltás és a szerkesztési mód a jelentések a Power BI-Munkaterületcsoportok

Ismerje meg, hogyan nézet közötti váltás és a szerkesztési mód a jelentések a Power BI-Munkaterületcsoportok belül.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="creating-an-access-token"></a>Hozzáférési jogkivonat létrehozása

Szeretne létrehozni, amely megtekintéséhez és a egy jelentés szerkesztése is lehetővé teszi hozzáférési jogkivonatot. Szerkesztése és a egy jelentés mentése kell a **Report.ReadWrite** jogkivonat-engedélyt. További információkért lásd: [történő hitelesítést és az engedélyezés a Power BI-Munkaterületcsoportok](app-token-flow.md).

> [!NOTE]
> Ez lehetővé teszi, hogy szerkesztheti, és mentse a módosításokat a meglévő jelentés. Ha szeretné is, a függvény az igazoló **Mentés másként**, meg kell adnia a további engedélyeket. További információkért lásd: [hatókörök](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>A beágyazási konfiguráció

Meg kell adnia a engedélyeket és a egy viewMode megtekintve láthatja a Mentés gombra a szerkesztési módban. További információkért lásd: [beágyazási konfiguráció részletei](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Ha például a JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Ez azt jelzi, hogy a jelentés beágyazása a megtekintési üzemmódba alapján **viewMode** beállítása **modellek. ViewMode.View**.

## <a name="view-mode"></a>Megjelenítési mód

A következő JavaScript használatával válthat a megtekintési üzemmódba, ha a szerkesztési módból.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Szerkesztőmód

A következő JavaScript segítségével váltson szerkesztőmódra, ha a megtekintési módban.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Lásd még

[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git Repo](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)
