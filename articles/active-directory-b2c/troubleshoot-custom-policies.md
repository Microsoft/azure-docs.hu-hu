---
title: Egyéni házirendek – Azure Active Directory B2C
description: Útmutató a hibák megoldásához, amikor a Azure Active Directory B2C egyéni házirendjeivel dolgozik.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840253"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Az egyéni szabályzatok és az Identity Experience Framework Azure AD B2C hibáinak megoldása

Ha Azure Active Directory B2C (Azure AD B2C) egyéni házirendeket használ, akkor a házirend nyelvi XML-formátumában olyan kihívásokat tapasztalhat, amelyek az identitási élmény keretrendszerét használják. Az egyéni szabályzatok írásának megtanulása olyan lehet, mint az új nyelv megtanulása. Ebben a cikkben néhány olyan eszközt és tippet ismertetünk, amelyek segíthetnek a problémák felderítésében és megoldásában.

Ez a cikk a Azure AD B2C egyéni házirend-konfigurációjának hibaelhárítására koncentrál. Nem foglalkozik a függő entitás alkalmazásával vagy az azonosító könyvtárával.

## <a name="xml-editing"></a>XML-szerkesztés

Az egyéni házirendek beállításának leggyakoribb hibája az XML-fájl helytelen formázása. A megfelelő XML-szerkesztő szinte elengedhetetlen. Az XML natív módon, színkódokkal jelenik meg, előre kitölti a gyakori kifejezéseket, megőrzi az indexelt XML-elemeket, és érvényesíti az XML-sémákat.

A kedvenc szerkesztők közül kettő a [Visual Studio Code](https://code.visualstudio.com/) és a [Notepad + +](https://notepad-plus-plus.org/).

Az XML-séma ellenőrzése a hibákat az XML-fájl feltöltése előtt azonosítja. Az [alapszintű csomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)gyökérkönyvtárában szerezze be a *TrustFrameworkPolicy_0.3.0.0. xsd* XML Schema Definition fájlt. Ha szeretné megtudni, hogyan használhatja az XSD-sémafájl érvényesítését a szerkesztőben, keresse meg az *XML-eszközöket* és az *XML-érvényesítést* , vagy a szerkesztő dokumentációjában hasonló módon.

Hasznos lehet az XML-szabályok áttekintése. Azure AD B2C elutasítja az észlelt XML-formázási hibákat. Előfordulhat, hogy a helytelenül formázott XML-üzenetek félrevezető hibaüzeneteket okozhatnak.

## <a name="upload-policies-and-policy-validation"></a>Szabályzatok és házirend-érvényesítés feltöltése

Az XML-házirend fájljának érvényesítése automatikusan történik a feltöltéskor. A legtöbb hiba miatt a feltöltés sikertelen lesz. Az érvényesítés magában foglalja a feltöltött házirend-fájlt. Emellett magában foglalja a fájlok feltöltendő fájljának láncát is (a függő entitások házirendjének fájlját, a kiterjesztések fájlját és az alapfájlt).

Gyakori érvényesítési hibák a következők:

> Hiba kódrészlet: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Lehet, hogy helytelenül van beállítva a ClaimType értéke, vagy nem létezik a sémában.
* A ClaimType értékeket meg kell adni a házirendben szereplő fájlok legalább egyikében.
    Például: `<ClaimType Id="issuerUserId">`
* Ha a ClaimType definiálva van a bővítmények fájlban, de az alapfájl Kivonatjogcím is használja, akkor az alap fájl feltöltése hibát eredményez.

> Hiba kódrészlet: `...makes a reference to a ClaimsTransformation with id...`

* A hiba oka ugyanaz lehet, mint a ClaimType hiba esetén.

> Hiba kódrészlet: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Győződjön meg arról, hogy a és az elemek TenantId értéke `<TrustFrameworkPolicy\>` `<BasePolicy\>` megegyezik a cél Azure ad B2C Bérlővel.

## <a name="troubleshoot-the-runtime"></a>A futtatókörnyezet hibáinak megoldása

* A **Futtatás most** paranccsal `https://jwt.ms` tesztelheti a házirendeket a webes vagy a mobil alkalmazástól függetlenül. Ez a webhely úgy viselkedik, mint egy függő entitás alkalmazása. Megjeleníti a Azure AD B2C szabályzat által generált JSON webes jogkivonat (JWT) tartalmát.

    A jogkivonat-ellenőrzéshez átirányítható tesztelési alkalmazás létrehozása `https://jwt.ms` :

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Az ügyféloldali böngésző és a Azure AD B2C közötti üzenetek cseréjének nyomon követéséhez használja a [hegedűst](https://www.telerik.com/fiddler). Ezzel a megoldással megtekintheti, hogy a felhasználói utazás miért sikertelen a folyamat lépésein.

* **Fejlesztési módban** a [Application Insights](troubleshoot-with-application-insights.md) segítségével nyomon követheti az Identity Experience Framework felhasználói utazásának tevékenységeit. **Fejlesztési módban** megfigyelheti a jogcímek cseréjét az identitási élmény keretrendszere és a technikai profilok által meghatározott különböző jogcímek, például az Identitáskezelés, az API-alapú szolgáltatások, a Azure ad B2C felhasználói könyvtár és más szolgáltatások (például az Azure ad multi-Factor Authentication) között.

## <a name="recommended-practices"></a>Ajánlott eljárások

**A forgatókönyvek több verzióját is megtarthatja. Csoportosítsa őket egy projektben az alkalmazással.** Az alap, a bővítmények és a függő entitások fájljai közvetlenül függenek egymástól. Mentse őket csoportként. Az új funkciók a szabályzatokhoz való hozzáadásakor külön működő verziókat tartanak fenn. A saját fájlrendszerében lévő munkaverziókat az alkalmazás kódjával együtt kell használni. Előfordulhat, hogy alkalmazásai számos különböző függő entitásra vonatkozó házirendet hívhatnak a bérlőben. Előfordulhat, hogy a Azure AD B2C szabályzatok által várt jogcímektől függenek.

**Műszaki profilok fejlesztése és tesztelése ismert felhasználói útvonalakkal.** A műszaki profilok beállításához a kipróbált Starter Pack-szabályzatokat használhatja. Ezeket külön tesztelje, mielőtt beépíti őket a saját felhasználói útjába.

**Kipróbált technikai profilokkal fejlesztheti és tesztelheti a felhasználói utazásokat.** A felhasználói útvonalak előkészítési lépéseinek növekményes módosítása. Fokozatosan hozza létre a kívánt forgatókönyveket.

## <a name="next-steps"></a>Következő lépések

Töltse le az [Active-Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip archívumot a githubon. Az adattár klónozása is megtehető:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
