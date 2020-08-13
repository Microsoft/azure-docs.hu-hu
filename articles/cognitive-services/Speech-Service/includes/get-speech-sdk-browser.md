---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: ab5a106d077713c1332bfb1ba4608d9410086c9f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186384"
---
:::row:::
    :::column span="3":::
        A JavaScripthez készült Speech SDK NPM-csomagként érhető el: a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> és a hozzá tartozó GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-adattár kognitív-Services-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bár a JavaScripthez készült Speech SDK NPM-csomagként érhető el, így az ügyfél-webböngészők és a Node.js is felhasználhatják azt – vegye figyelembe az egyes környezetek különböző építészeti következményeit. Például a dokumentum- <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">objektummodell (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nem érhető el kiszolgálóoldali alkalmazásokhoz, ugyanúgy, mint a <a href="https://nodejs.org/api/fs.html" target="_blank">fájlrendszer <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nem érhető el az ügyféloldali alkalmazások számára.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

A JavaScripthez készült Speech SDK telepítéséhez futtassa az alábbi `npm install` parancsot az alábbi paranccsal.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-parancsfájl címkéje

Azt is megteheti, hogy közvetlenül tartalmaz egy `<script>` címkét a HTML- `<head>` elemben, amely a <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM szindikátusra <span class="docon docon-navigate-external x-hidden-focus"></span> </a>támaszkodik.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

További információ: <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">webböngésző SPEECH SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a>– rövid útmutató.
