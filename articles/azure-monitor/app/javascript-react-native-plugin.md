---
title: Natív reagálású beépülő modul a Application Insights JavaScript SDK-hoz
description: A natív reagálás beépülő modul telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593599"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Natív reagálású beépülő modul a Application Insights JavaScript SDK-hoz

A natív reagálásra szolgáló beépülő modul Application Insights JavaScript SDK-hoz gyűjti az eszköz adatait, alapértelmezés szerint ez a beépülő modul automatikusan gyűjti a következőket:

- **Egyedi eszköz-azonosító** (más néven telepítési azonosító)
- **Eszköz modell neve** (például iPhone X, Samsung Galaxy fold, Huawei P30 Pro stb.)
- **Eszköz típusa** (például kézibeszélő, Tablet stb.)

## <a name="requirements"></a>Követelmények

A (>= 2.0.0) verziót kell használnia `@microsoft/applicationinsights-web` . Ez a beépülő modul csak reagáló natív alkalmazásokban működik. [A kiállítási keretrendszer használatával](https://docs.expo.io/)nem fog működni az alkalmazásokkal, ezért nem fog működni a Create reaktív alkalmazás létrehozásával.

## <a name="getting-started"></a>Első lépések

Telepítse és csatolja az [reagáló natív eszköz-információs](https://www.npmjs.com/package/react-native-device-info) csomagot. A csomag naprakészen tartása a `react-native-device-info` legújabb eszközök nevének az alkalmazással való összegyűjtéséhez.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>A beépülő modul inicializálása

A beépülő modul használatához létre kell hoznia a beépülő modult, és hozzá kell adnia a `extension` meglévő Application Insights-példányához.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Következő lépések

- A JavaScript SDK-val kapcsolatos további tudnivalókért tekintse meg a [Application Insights JavaScript SDK dokumentációját](javascript.md).
- A Kusto lekérdezési nyelvének megismeréséhez és a Log Analytics adatainak lekérdezéséhez tekintse meg a [naplózási lekérdezés áttekintését](../../azure-monitor/logs/log-query-overview.md).
