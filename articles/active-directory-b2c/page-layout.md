---
title: Oldalelrendezési verziók
titleSuffix: Azure AD B2C
description: Az egyéni házirendek felhasználói felületének testreszabása az oldalelrendezés korábbi verzióiban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8469e05b82a651760829761ca57af3bdb1b256a9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443406"
---
# <a name="page-layout-versions"></a>Oldalelrendezési verziók

A lapelrendezés-csomagok rendszeres időközönként frissülnek, hogy tartalmazzanak javításokat és javításokat a lapok elemeiben. A következő változási napló meghatározza az egyes verziókban bevezetett módosításokat.

## <a name="jquery-version"></a>jQuery-verzió

Azure AD B2C az oldal elrendezése a [jQuery Library](https://jquery.com/)következő verzióját használja:

|A lapelrendezés verziója  |jQuery-verzió  |
|---------|---------|
|2.1.4 | 3.5.1 |
|1.2.0 | 3.4.1 |
|1.1.0 | 1.10.2 |

## <a name="self-asserted-page-selfasserted"></a>Önérvényesített oldal (selfasserted)

**2.1.4**
- Frissítette a jQuery verzióját a 3.5.1-es verzióra.
- Frissített HandlebarJS-verzió a 4.7.6-be.

**2.1.3**
- Biztonsági javítások.

**2.1.2**
- Kijavítottuk a honosított kódolási problémát olyan nyelveken, mint például a spanyol és a francia.

**2.1.1**

- Hozzáadott egy UXString az `heading` oldalon a `intro` címként való megjelenítéshez. Alapértelmezés szerint ez a beállítás rejtett.
- A jelszavak iCloud kulcstartóba való mentésének támogatása.
- A házirend vagy a QueryString paraméter használatának támogatása az `pageFlavor` elrendezés kiválasztásához (klasszikus, oceanBlue vagy slateGray).
- Visszaigénylések hozzáadása az önérvényesített oldalon.
- A fókusz most az első szerkeszthető mezőre kerül, amikor az oldal betöltődik.
- A fókusz most az első hiba mezőre kerül, ha több mező hibába ütközik.
- Az e-mail ellenőrző kód ellenőrzése után a fókusz most a "módosítás" gombra kerül.

**2.1.0**

- Honosítási és kisegítő lehetőségek javításai.

**2.0.0**

- A [megjelenítési vezérlők](display-controls.md) támogatása az egyéni házirendekben.

**1.2.0**

- A Felhasználónév/e-mail és jelszó mezők mostantól a `form` HTML-elemmel lehetővé teszik az Edge és az Internet Explorer (IE) számára az adatok megfelelő mentését.
- Egy konfigurálható felhasználói bemeneti ellenőrzési késleltetés lett hozzáadva a továbbfejlesztett felhasználói élményhez.
- Kisegítő lehetőségek – javítások
- Kijavítottuk a kisegítő lehetőségeket, így a Narrátor a hibaüzeneteket olvashatja. 
- A fókusz most a jelszó mezőre kerül az e-mail ellenőrzése után.
- Eltávolítva `autofocus` a jelölőnégyzet vezérlőelemből. 
- A telefonszám-ellenőrzés megjelenítési vezérlőelemének támogatása.
- Mostantól hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékhez](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` és a mobil billentyűzetek biztosítják a megfelelő javaslatokat.
- A Chrome fordításának támogatása.
- A vállalati arculat támogatása a felhasználói folyamatok oldalain.

**1.1.0**

- Törlési riasztás eltávolítva
- A hibás elemek CSS-osztálya
- A hiba logikájának megjelenítése/elrejtése Továbbfejlesztve
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Egyesített bejelentkezési regisztrációs oldal a jelszó-visszaállítási hivatkozással (unifiedssp)

> [!TIP]
> Ha a lapot honosítja több területi beállítás, vagy egy felhasználói folyamat nyelveinek támogatásához. A [honosítási azonosítók](localization-string-ids.md) című cikk felsorolja azokat a honosítási azonosítókat, amelyek a kiválasztott oldal verziójához használhatók.

**2.1.4**
- Frissítette a jQuery verzióját a 3.5.1-es verzióra.
- Frissített HandlebarJS-verzió a 4.7.6-be.

**2.1.3**
- Biztonsági javítások.
- Apróbb hibajavítások.

**2.1.2**
- Kijavítottuk a honosított kódolási problémát olyan nyelveken, mint például a spanyol és a francia.
- A "Elfelejtett jelszó" hivatkozás engedélyezése a jogcím-csereként való használatra. További információ: [önkiszolgáló jelszó-visszaállítás](add-password-reset-policy.md#self-service-password-reset-recommended).

**2.1.1**
- Hozzáadott egy UXString az `heading` oldalon a `intro` címként való megjelenítéshez. Alapértelmezés szerint ez a beállítás rejtett.
- A házirend vagy a QueryString paraméter használatának támogatása az `pageFlavor` elrendezés kiválasztásához (klasszikus, oceanBlue vagy slateGray).
- A jelszavak iCloud kulcstartóba való mentésének támogatása.
- A fókusz most az első hiba mezőre kerül, ha több mező hibába ütközik.
- A fókusz most az első szerkeszthető mezőre kerül, amikor az oldal betöltődik.
- Új hely lett hozzáadva a jogcím-szolgáltató kiválasztási hivatkozásához `bottomUnderFormClaimsProviderSelections` .
- A már nem használt UXStrings eltávolítva.

**2.1.0**

- Több regisztrációs hivatkozás támogatása.
- A felhasználói bevitel érvényesítésének támogatása a szabályzatban meghatározott predikátum-szabályoknak megfelelően.

**1.2.0**

- A Felhasználónév/e-mail és jelszó mezők mostantól a `form` HTML-elemmel lehetővé teszik az Edge és az Internet Explorer (IE) számára az adatok megfelelő mentését.
- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](customize-ui-with-html.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` és a mobil billentyűzetek biztosítják a megfelelő javaslatokat.
- A Chrome fordításának támogatása.
- A bérlő arculatának támogatása a felhasználói folyamatok oldalain.

**1.1.0**

- A bejelentkezett (KMSI) vezérlőelem hozzáadása

**1.0.0**

- Kezdeti kiadás

## <a name="mfa-page-multifactor"></a>MFA-oldal (többtényezős)

**1.2.4**
- Frissítette a jQuery verzióját a 3.5.1-es verzióra.
- Frissített HandlebarJS-verzió a 4.7.6-be.

**1.2.3**
- Elemleírás-karakterlánc felülbírálásának engedélyezése nyelvi honosítás útján.
- Biztonsági javítások.
- Apróbb hibajavítások.

**1.2.2**
- Kijavított egy problémát az ellenőrző kód automatikus kitöltésével az iOS használatakor.
- Kijavítottuk a tokennek a függő entitáshoz való átirányításával kapcsolatos problémát az Android Webview szolgáltatásban. 
- Hozzáadott egy UXString az `heading` oldalon a `intro` címként való megjelenítéshez. Alapértelmezés szerint ez a beállítás rejtett.  
- A házirend vagy a QueryString paraméter használatának támogatása az `pageFlavor` elrendezés kiválasztásához (klasszikus, oceanBlue vagy slateGray).

**1.2.1**

- Kisegítő lehetőségek javításai az alapértelmezett sablonokban

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](customize-ui-with-html.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása.
- A bérlő arculatának támogatása a felhasználói folyamatok oldalain.

**1.1.0**

- A "kód megerősítése" gomb el lett távolítva
- A kód bemeneti mezője csak hat (6) karakter bevitelét teszi elérhetővé.
- Az oldal automatikusan megkísérli ellenőrizni a 6 számjegyű kód beírásakor megadott kódot, anélkül, hogy a gombokra rá kellene kattintani
- Ha a kód nem megfelelő, a beviteli mező automatikusan törlődik
- Ha a három (3) helytelen kóddal próbálkozik, a B2C hibát küld vissza a függő entitásnak.
- Kisegítő lehetőségek – javítások
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="exception-page-globalexception"></a>Kivétel lap (globalexception)

**1.2.1**
- Frissítette a jQuery verzióját a 3.5.1-es verzióra.
- Frissített HandlebarJS-verzió a 4.7.6-be.

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](customize-ui-with-html.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.1.0**

- Kisegítő lehetőségek javítása
- Az alapértelmezett üzenet el lett távolítva, ha nincs kapcsolat a szabályzatból
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Egyéb lapok (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.1**
- Frissítette a jQuery verzióját a 3.5.1-es verzióra.
- Frissített HandlebarJS-verzió a 4.7.6-be.

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](customize-ui-with-html.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.0.0**

- Kezdeti kiadás

## <a name="next-steps"></a>Következő lépések

Az alkalmazások felhasználói felületének egyéni házirendekben való testreszabásával kapcsolatos további információkért lásd [az alkalmazás felhasználói felületének egyéni házirenddel való testreszabását](customize-ui-with-html.md)ismertető témakört.
