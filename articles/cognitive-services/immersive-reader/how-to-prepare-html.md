---
title: HTML-tartalom előkészítése a lebilincselő olvasó számára
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan indíthatja el az olvasót HTML, JavaScript, Python, Android vagy iOS használatával. A magával ragadó olvasó bevált technikákat használ a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megértésének javítására.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620067"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>HTML-tartalom előkészítése a lebilincselő olvasó számára

Ebből a cikkből megtudhatja, hogyan alakíthatja ki a HTML-kódot, és hogyan kérheti le a tartalmat, hogy az a teljes olvasó számára is használható legyen.

## <a name="prepare-the-html-content"></a>A HTML-tartalom előkészítése

Helyezze el a megjelenő tartalmat a tároló elemen belül a magára az olvasóba. Ügyeljen arra, hogy a Container elem egyedi legyen `id` . Az egyszerű HTML-elemek támogatását a magától megtekintő olvasó nyújtja, további információért lásd a [referenciát](reference.md#html-support) .

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>HTML-tartalom beolvasása a JavaScriptben

A `id` tároló elem használatával lekérheti a HTML-tartalmat a JavaScript-kódjában.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>A részletes olvasó elindítása a HTML-tartalommal

A híváskor `ImmersiveReader.launchAsync` az adathalmaz tulajdonságát állítsa be a `mimeType` `text/html` HTML megjelenítésének engedélyezéséhez.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](reference.md)