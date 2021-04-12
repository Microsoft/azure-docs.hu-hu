---
title: A megölelő olvasó cookie-szabályzatának beállítása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan állíthatja be a cookie-szabályzatot a teljes olvasóhoz.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 8b0a1f4a948aa6fec565130acb5267476a1d4401
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048646"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>A cookie-szabályzat beállítása a magától megjelenő olvasóhoz

A lebilincselő olvasó alapértelmezés szerint letiltja a cookie-használatot. Ha engedélyezi a cookie-használatot, a felhasználóbarát olvasó cookie-kat használhat a felhasználói beállítások fenntartásához és a funkciók használatának nyomon követéséhez. Ha engedélyezi a cookie-használatot a megölelő olvasóban, vegye figyelembe az EU cookie-megfelelőségi szabályzatának követelményeit. A gazda alkalmazás feladata a szükséges felhasználói jóváhagyások beszerzése az EU cookie-megfelelőségi szabályzatának megfelelően.

A cookie-szabályzat a magával ragadó olvasó [beállításain](../reference.md#options)keresztül állítható be.

## <a name="enable-cookie-usage"></a>Cookie-használat engedélyezése

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie-használat letiltása

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) rövid útmutatót, amelyből megtudhatja, hogy mit tehet a magától az olvasói SDK-val a Node.js használatával
* Tekintse meg az [Android-oktatóanyagot](../how-to-launch-immersive-reader.md) , amelyből megtudhatja, hogy mi a teendő a Java vagy a Kotlin for Android használatával.
* Tekintse meg az [iOS-oktatóanyagot](../how-to-launch-immersive-reader.md) , amelyből megtudhatja, hogy mit tehet a gyors iOS-es verzióban
* Tekintse meg a [Python-oktatóanyagot](../how-to-launch-immersive-reader.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](../reference.md)