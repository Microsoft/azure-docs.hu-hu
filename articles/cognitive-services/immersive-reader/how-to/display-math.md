---
title: Matematikai megjelenítés a magától megjelenő olvasóban
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan jelenítheti meg a matematikai elemeket a teljes olvasóban.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946121"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>A matematika megjelenítése a lebilincselő olvasóban

A magával ragadó olvasó matematikai Markup Language ([MathML](https://developer.mozilla.org/docs/Web/MathML)) formában jeleníti meg a matematikai elemeket.
A MIME-típus a magától megtekinthető olvasó [chunk](../reference.md#chunk)adattömbökön állítható be. További információ: [támogatott MIME-típusok](../reference.md#supported-mime-types) .

## <a name="send-math-to-the-immersive-reader"></a>Matematikai küldése a magára olvasónak
Ha matematikai küldést szeretne küldeni a lebilincselő olvasónak, adjon meg egy MathML tartalmazó adathalmazt, és állítsa a ```application/mathml+xml```MIME-típust a következőre:;

Például ha a tartalom a következő volt:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Ezután a következő JavaScript használatával jelenítheti meg a tartalmakat.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

A lebilincselő olvasó indításakor a következőket kell látnia:

![Matematika a lebilincselő olvasóban](../media/how-tos/1-math.png)

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](../reference.md)