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
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332372"
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
* Tekintse meg az [Android-oktatóanyagot](../tutorial-android.md) , amelyből megtudhatja, hogy mi a teendő a Java vagy a Kotlin for Android használatával.
* Tekintse meg az [iOS-oktatóanyagot](../tutorial-ios.md) , amelyből megtudhatja, hogy mit tehet a gyors iOS-es verzióban
* Tekintse meg a [Python-oktatóanyagot](../tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](../reference.md)