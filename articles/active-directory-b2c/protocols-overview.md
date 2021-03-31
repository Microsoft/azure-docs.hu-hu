---
title: Hitelesítési protokollok a Azure Active Directory B2Cban | Microsoft Docs
description: Alkalmazások közvetlen létrehozása a Azure Active Directory B2C által támogatott protokollok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183905"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: hitelesítési protokollok
A Azure Active Directory B2C (Azure AD B2C) a két iparági szabványnak megfelelő protokollok támogatásával biztosítja a szolgáltatásként nyújtott identitást az alkalmazások számára: az OpenID Connect és a OAuth 2,0. A szolgáltatás szabványoknak megfelelő, de ezeknek a protokolloknak két implementációja is finom különbségeket tartalmazhat.

Az útmutatóban szereplő információk akkor hasznosak, ha a kódot közvetlenül a HTTP-kérések küldésével és felügyeletével írja le, nem pedig nyílt forráskódú kódtár használatával. Javasoljuk, hogy olvassa el ezt a lapot, mielőtt beleveti az egyes protokollok részletes adatait. Ha azonban már ismeri a Azure AD B2Ct, egyből megnyithatja [a protokoll-referenciákat](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Az alapok
Minden Azure AD B2Ct használó alkalmazást regisztrálni kell a B2C-címtárban a [Azure Portalban](https://portal.azure.com). Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz:

* **Application ID** (Alkalmazásazonosító), amely egyedileg azonosítja az alkalmazást.
* **Átirányítási URI** -vagy **alkalmazáscsomag-azonosító** , amely az alkalmazásra való közvetlen válaszadásra használható.
* Néhány más forgatókönyv-specifikus érték. További információért olvassa el az [alkalmazás regisztrálását](tutorial-register-applications.md)ismertető témakört.

Az alkalmazás regisztrálása után a Azure Active Directory (Azure AD) szolgáltatással kommunikál a végpontra irányuló kérelmek küldésével:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Szinte minden OAuth és OpenID Connect-folyamathoz négy fél vesz részt az Exchange-ben:

![A négy OAuth 2,0 szerepkört ábrázoló diagram](./media/protocols-overview/protocols_roles.png)

* Az **engedélyezési kiszolgáló** az Azure ad-végpont. Biztonságos módon kezeli a felhasználói adatokhoz és a hozzáféréshez kapcsolódó bármilyen információt. A folyamat résztvevői közötti megbízhatósági kapcsolatokat is kezeli. Feladata a felhasználó személyazonosságának ellenőrzése, az erőforrásokhoz való hozzáférés engedélyezése és visszavonása, valamint a jogkivonatok kiállítása. Más néven identitás-szolgáltató.

* Az **erőforrás tulajdonosa** általában a végfelhasználó. Ez az a fél, amely az adott adatforrást birtokolja, és lehetővé teszi, hogy harmadik felek hozzáférjenek ehhez az adatforráshoz vagy erőforráshoz.

* Az **OAuth-ügyfél** az alkalmazás. Az alkalmazás azonosítója azonosítja. Ez általában az a fél, amely a végfelhasználók számára együttműködik. Emellett az engedélyezési kiszolgáló jogkivonatait is kéri. Az erőforrás tulajdonosának engedélyt kell adni az ügyfélnek az erőforrás eléréséhez.

* Az **erőforrás-kiszolgáló** , ahol az erőforrás vagy az adat található. Megbízik az engedélyezési kiszolgálón a OAuth-ügyfél biztonságos hitelesítéséhez és engedélyezéséhez. Emellett a tulajdonos hozzáférési jogkivonatait is használja, hogy az erőforrásokhoz való hozzáférés megadható legyen.

## <a name="policies-and-user-flows"></a>Házirendek és felhasználói folyamatok
Azure AD B2C szabályzatok vitathatatlanul a szolgáltatás legfontosabb funkciói. A Azure AD B2C kiterjeszti a standard OAuth 2,0 és az OpenID Connect protokollokat a szabályzatok bevezetésével. Ezek lehetővé teszik, hogy a Azure AD B2C sokkal több, mint egyszerű hitelesítést és engedélyezést végezzen.

A leggyakoribb identitási feladatok beállításához a Azure AD B2C-portál a **felhasználói folyamatok** nevű előre definiált, konfigurálható szabályzatokat tartalmazza. A felhasználói folyamatok teljes mértékben leírják a fogyasztói identitások élményét, beleértve a regisztrációt, a bejelentkezést és a profil szerkesztését. A felhasználói folyamatok egy felügyeleti felületen definiálhatók. A HTTP-hitelesítési kérelmek speciális lekérdezési paramétere segítségével hajthatók végre.

A szabályzatok és a felhasználói folyamatok nem szabványos funkciók a OAuth 2,0 és az OpenID Connect szolgáltatáshoz, ezért érdemes megismernie a szükséges időt. További információ: [Azure ad B2C felhasználói folyamatokra vonatkozó útmutató](user-flow-overview.md).

## <a name="tokens"></a>Tokenek
A OAuth 2,0 és az OpenID Connect Azure AD B2C megvalósítása a tulajdonosi jogkivonatok széles körű használatát teszi lehetővé, beleértve a JSON webes tokenként (JWTs) jelölt tulajdonosi jogkivonatokat is. A tulajdonosi jogkivonat egy olyan egyszerű biztonsági jogkivonat, amely a "tulajdonos" hozzáférést biztosít egy védett erőforráshoz.

A tulajdonos bármely olyan fél, aki be tudja mutatni a jogkivonatot. Az Azure AD-nek először hitelesítenie kell egy felet, mielőtt fogadni tudja a tulajdonosi jogkivonatot. Ha azonban a szükséges lépéseket nem a jogkivonat biztonságossá tételéhez használja az átvitel és a tárolás során, akkor azt egy nem szándékolt fél meghallgathatja és felhasználhatja.

Egyes biztonsági jogkivonatok olyan beépített mechanizmusokkal rendelkeznek, amelyek megakadályozzák a jogosulatlan felek használatát, de a tulajdonosi jogkivonatok nem rendelkeznek ezzel a mechanizmussal. Ezeket biztonságos csatornán kell szállítani, például egy Transport Layer Security (HTTPS) protokollon keresztül.

Ha egy tulajdonosi jogkivonatot egy biztonságos csatornán kívül továbbítanak, egy rosszindulatú fél a támadók számára egy ember általi támadással szerezheti be a jogkivonatot, és felhasználhatja a védett erőforráshoz való jogosulatlan hozzáféréshez. Ugyanazok a biztonsági alapelvek érvényesek, amikor a tulajdonosi jogkivonatok tárolása vagy gyorsítótárazása későbbi használatra történik. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja és tárolja a tulajdonosi jogkivonatokat.

További tulajdonosi jogkivonatok biztonsági megfontolásokért lásd: [RFC 6750, 5. szakasz](https://tools.ietf.org/html/rfc6750).

További információ a Azure AD B2C használt jogkivonatok különböző típusairól [Az Azure ad-jogkivonat dokumentációjában](tokens-overview.md)találhatók.

## <a name="protocols"></a>Protokollok
Ha készen áll a kérelmek áttekintésére, az alábbi oktatóanyagok közül választhat. Mindegyik egy adott hitelesítési forgatókönyvnek felel meg. Ha segítségre van szüksége az Ön számára legmegfelelőbb folyamat meghatározásához, tekintse meg [a Azure ad B2C használatával felépíthető alkalmazások típusait](application-types.md).

* [Mobil-és natív alkalmazások készítése a OAuth 2,0 használatával](authorization-code-flow.md)
* [Webalkalmazások létrehozása az OpenID Connect használatával](openid-connect.md)
* [Egylapos alkalmazások készítése a OAuth 2,0 implicit folyamat használatával](implicit-flow-single-page-application.md)

