---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 99be483f67bf5e3f9b27c63a2318df8761c16eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434527"
---
:::row:::
    :::column span="3":::
        A JavaScripthez készült Speech SDK NPM-csomagként érhető el: a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech-SDK </a> és a hozzá tartozó GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-adattár kognitív-Services-Speech-SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bár a JavaScripthez készült Speech SDK NPM-csomagként érhető el, így a Node.js és az ügyfél webböngészői is felhasználhatják azt – vegye figyelembe az egyes környezetek különböző építészeti következményeit. Például a dokumentum- <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">objektummodell (DOM) </a> nem érhető el kiszolgálóoldali alkalmazásokhoz, ugyanúgy, mint a <a href="https://nodejs.org/api/fs.html" target="_blank">fájlrendszer </a> nem érhető el az ügyféloldali alkalmazások számára.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

A JavaScripthez készült Speech SDK telepítéséhez futtassa az alábbi `npm install` parancsot az alábbi paranccsal.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

További információ: <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js SPEECH SDK </a>gyors üzembe helyezése.
