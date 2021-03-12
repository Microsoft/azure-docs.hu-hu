---
title: Felhasználói beállítások tárolása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan tárolhatja a felhasználó beállításait.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617692"
---
# <a name="how-to-store-user-preferences"></a>Felhasználói beállítások tárolása

Ez a cikk bemutatja, hogyan tárolhatja a felhasználó felhasználói FELÜLETi beállításait (a **felhasználói beállításoknak** megfelelően) a [-Preferences](./reference.md#options) és a [-onPreferencesChanged](./reference.md#options) teljes olvasó SDK-beállításokkal.

Ha a [CookiePolicy](./reference.md#cookiepolicy-options) SDK beállítás *engedélyezve* értékre van állítva, a felhasználóbarát olvasó alkalmazás tárolja a cookie-k **felhasználói beállításait** (a szöveg méretét, a téma színét, a betűtípust stb.), amelyek egy adott böngészőhöz és eszközhöz tartoznak. Minden alkalommal, amikor a felhasználó ugyanazzal a böngészővel és eszközön indítja el az olvasót, megnyílik a felhasználó beállításai az adott eszköz utolsó munkamenetében. Ha azonban a felhasználó egy másik böngészőben vagy eszközön nyitja meg az olvasót, a beállítások kezdetben a magával ragadó olvasó alapértelmezett beállításaival lesznek konfigurálva, és a felhasználónak újra be kell állítania a beállításait, és így minden egyes használt eszközön. A `-preferences` és a teljes `-onPreferencesChanged` OLVASÓi SDK-k lehetővé teszik, hogy az alkalmazások különböző böngészőkben és eszközökön barangolják a felhasználók beállításait, így a felhasználó egységes felhasználói felülettel rendelkezik, ahol az alkalmazást használják.

Először is, a `-onPreferencesChanged` visszahívási SDK beállítás megadásával, amikor elindítja a felhasználóbarát olvasó alkalmazást, a magától megjelenő olvasó egy `-preferences` sztringet küld vissza a gazda alkalmazásnak, amikor a felhasználó megváltoztatja a beállításait az olvasói munkamenet során. Ezután a gazda alkalmazás feladata a felhasználói beállítások tárolása a saját rendszerében. Ha ugyanez a felhasználó ismét elindítja az olvasót, a gazda alkalmazás lekérheti a felhasználó beállításait a tárolóból, és karakterlánc-SDK-ként adhatja meg azokat a felhasználóbarát `-preferences` olvasó alkalmazás indításakor, hogy a felhasználó beállításait visszaállítsa.

Ez a funkció alternatív módszerként is használható a **felhasználói beállítások** tárolásához abban az esetben, ha a cookie-k használata nem kívánatos vagy nem valósítható meg.

> [!CAUTION]
> **Fontos** Ne próbálja meg programozott módon megváltoztatni a `-preferences` felhasználóbarát olvasó alkalmazásba érkező és a felé irányuló karakterlánc értékeit, mivel ez váratlan viselkedést eredményezhet, ami az ügyfelek számára csökkentett teljesítményű felhasználói élményt eredményez. A gazdagép alkalmazásai soha nem rendelhetnek egyéni értéket a karakterlánchoz, vagy kezelhetik azokat `-preferences` . A `-preferences` karakterlánc beállítás használatakor csak a `-onPreferencesChanged` visszahívási beállításban visszaadott pontos értéket használja.

## <a name="how-to-enable-storing-user-preferences"></a>Felhasználói beállítások tárolásának engedélyezése

a magával ragadó olvasó SDK [launchAsync](./reference.md#launchasync) `options` paraméter tartalmazza a `-onPreferencesChanged` visszahívást. Ezt a függvényt bármikor meghívja a rendszer, amikor a felhasználó megváltoztatja a beállításait. A `value` paraméter egy karakterláncot tartalmaz, amely a felhasználó aktuális beállításait jelöli. Ezt a karakterláncot ezután tárolja a rendszer az adott felhasználó számára a gazda alkalmazásban.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Felhasználói beállítások betöltése a felhasználóbarát olvasóba

A beállítás használatával adja át a felhasználó beállításait a magára az olvasónak `-preferences` . A felhasználó preferenciáinak tárolására és betöltésére szolgáló triviális példa a következő:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)