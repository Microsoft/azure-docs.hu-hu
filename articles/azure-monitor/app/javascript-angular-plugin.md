---
title: Szögletes beépülő modul a Application Insights JavaScript SDK-hoz
description: Szögletes beépülő modul telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737018"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Szögletes beépülő modul a Application Insights JavaScript SDK-hoz

A Application Insights JavaScript SDK szögletes beépülő modulja a következőket teszi lehetővé:

- Az útválasztó változásainak követése

> [!WARNING]
> A szögletes beépülő modul nem ECMAScript 3 (ES3) kompatibilis.

## <a name="getting-started"></a>Első lépések

NPM-csomag telepítése:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Alapvető használat

Application Insights példányának beállítása az alkalmazás belépési összetevőjében:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A JavaScript SDK-val kapcsolatos további tudnivalókért tekintse meg a [Application Insights JavaScript SDK dokumentációját](javascript.md) .
- [Szögletes beépülő modul a GitHubon](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
