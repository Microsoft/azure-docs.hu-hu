---
title: Az Application Insights JavaScript SDK beépülő moduljának reagálása
description: Az reagáló beépülő modul telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 4970cacb0995678bdad87f14ba971b8fb88ffa09
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593638"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Az Application Insights JavaScript SDK beépülő moduljának reagálása

A Application Insights JavaScript SDK beépülő moduljának reagálása lehetővé teszi a következőket:

- Útvonal-változások nyomon követése
- Az összetevők használati statisztikáinak megválaszolása

## <a name="getting-started"></a>Első lépések

NPM-csomag telepítése:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Alapvető használat

Application Insightshoz való kapcsolódás inicializálása:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Az összetevő becsomagolása a magasabb rendű összetevő-függvénnyel a Application Insights engedélyezéséhez:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Konfiguráció

| Name    | Alapértelmezett | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| előzmények | null    | Az útválasztó előzményeinek megválaszolása. További információ: [reakciós útválasztó csomag dokumentációja](https://reactrouter.com/web/api/history). Ha meg szeretné tudni, hogyan érheti el az előzmények objektumot az összetevőkön kívül, tekintse meg a [reakciós útválasztó – gyakori kérdések](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Az összetevők használatának nyomon követése

A magasabb rendű összetevő-használat nyomon követéséhez alkalmazza a különböző reakciós összetevők használatát `withAITracking` .

Az eseményen keresztül méri a `ComponentDidMount` rendezvény idejét `ComponentWillUnmount` . Ahhoz azonban, hogy ez pontosabb legyen, ki kell vonnia azt az időpontot, amikor a felhasználó tétlen volt `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Ha meg szeretné tekinteni ezt a mérőszámot a Azure Portalben, navigáljon a Application Insights erőforráshoz, válassza a "metrikák" fület, és konfigurálja az üres diagramokat az egyéni metrika nevének megjelenítéséhez "az összetevő bevonásának ideje (másodpercben)" beállításnál válassza ki az összesítést (Sum, AVG stb.), és alkalmazza a felosztást "összetevő neve" értékre.

![Képernyőkép a diagramról, amely az egyéni metrika "az összetevő beosztásának időpontját (másodpercben)" felosztását jeleníti meg az "összetevő neve" alapján.](./media/javascript-react-plugin/chart.png)

Emellett egyéni lekérdezéseket is futtathat az Application Insights-adat felosztásához, amely szerint a jelentés és a vizualizációk előállítása a követelmények szerint történik. A Azure Portal navigáljon a Application Insights erőforráshoz az Áttekintés lap felső menüjéből válassza az "elemzés" lehetőséget, és futtassa a lekérdezést.

![Képernyőkép az egyéni metrika lekérdezési eredményeiről.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Akár 10 percet is igénybe vehet, amíg az új egyéni metrikák megjelennek az Azure Portalon.

## <a name="using-react-hooks"></a>Reagáló hookok használata

A reakciós [hookok](https://reactjs.org/docs/hooks-reference.html) az állapot-és életciklus-kezelési módszerek a reagáló alkalmazásokban anélkül, hogy az osztályon alapuló reagáló összetevőkre kellene támaszkodni. A Application Insights reagáló beépülő modul számos Hook-integrációt biztosít, amelyek hasonló módon működnek, mint a magasabb rendű összetevők megközelítése.

### <a name="using-react-context"></a>Reagálási környezet használata

A Application Insights reakciós Kampói úgy lettek kialakítva, hogy az a [kontextust](https://reactjs.org/docs/context.html) is használják, mint az adott aspektust. A környezet használatához inicializálja Application Insights a fentieknek megfelelően, majd importálja a környezeti objektumot:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Ez a környezeti szolgáltató a Application Insights elérhetővé teszi az `useContext` összes gyermek összetevőn belül.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

A `useTrackMetric` Hook replikálja a `withAITracking` magasabb rendű összetevő funkcióit anélkül, hogy további összetevőket adna hozzá az összetevő-struktúrához. A Hook két argumentumot fogad, először a Application Insights példányt (amely a hooktól szerezhető be `useAppInsightsContext` ), valamint az összetevő azonosítóját a nyomon követéshez (például a nevét).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Ugyanúgy működik, mint a magasabb rendű összetevő, de az életciklus-életciklus helyett reagálni fog az életciklus eseményeire. A Hookot explicit módon meg kell adni a felhasználói eseményekhez, ha bizonyos interakciók futtatására van szükség.

### `useTrackEvent`

A `useTrackEvent` Hook segítségével nyomon követheti az alkalmazás által nyomon követett egyéni eseményeket, például egy kattintással vagy más API-hívással. Két argumentumot vesz igénybe, az első a Application Insights-példány (amely a `useAppInsightsContext` horogtól szerezhető be), valamint az esemény neve.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Ha a horog használatban van, egy adattartalom is megadható, amely további adatokat ad hozzá az eseményhez, amikor Application Insights tárolják.

## <a name="react-error-boundaries"></a>Hiba határainak megválaszolása

A [hibák határai](https://reactjs.org/docs/error-boundaries.html) lehetővé teszik a kivételek zökkenőmentes kezelését, ha egy reagáló alkalmazáson belül történik, és ha ez a hiba akkor valószínű, hogy a kivételt naplózni kell. A Application Insights reagáló beépülő modulja egy hiba határ összetevőt biztosít, amely automatikusan naplózza a hibát, amikor a művelet bekövetkezik.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

A `AppInsightsErrorBoundary` két kelléket kell átadnia, az `ReactPlugin` alkalmazáshoz létrehozott példányt és egy, a hiba bekövetkezésekor megjelenítendő összetevőt. Ha kezeletlen hiba történik, a `trackException` rendszer meghívja a hiba határára vonatkozó információkat, és `onError` megjeleníti az összetevőt.

## <a name="sample-app"></a>Minta alkalmazás

Tekintse meg a [Application Insights reagáló bemutatót](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Következő lépések

- A JavaScript SDK-val kapcsolatos további tudnivalókért tekintse meg a [Application Insights JavaScript SDK dokumentációját](javascript.md).
- A Kusto lekérdezési nyelvének megismeréséhez és a Log Analytics adatainak lekérdezéséhez tekintse meg a [naplózási lekérdezés áttekintését](../../azure-monitor/logs/log-query-overview.md).
