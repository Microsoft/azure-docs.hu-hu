---
title: Webes API-kat meghívó asztali alkalmazások regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (alkalmazás-regisztráció)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263397aa2cd09ba24fa750131b76047801869a65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798935"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó asztali alkalmazás: alkalmazás regisztrálása

Ez a cikk az asztali alkalmazások alkalmazás-regisztrálási sajátosságait ismerteti.

## <a name="supported-account-types"></a>Támogatott fióktípusok

Az asztali alkalmazásokban támogatott fióktípus attól függ, hogy milyen élményre van szüksége. A kapcsolat miatt a támogatott fióktípus a használni kívánt folyamatoktól függ.

### <a name="audience-for-interactive-token-acquisition"></a>Célközönség az interaktív jogkivonat-beszerzéshez

Ha az asztali alkalmazás interaktív hitelesítést használ, bármilyen [fióktípus](quickstart-register-app.md)használatával bejelentkezhet a felhasználókba.

### <a name="audience-for-desktop-app-silent-flows"></a>Hallgatóság az asztali alkalmazások csendes folyamataihoz

- Ha integrált Windows-hitelesítést vagy felhasználónevet és jelszót szeretne használni, az alkalmazásnak be kell jelentkeznie a saját bérlőben lévő felhasználókba, például ha Ön üzletági (LOB) fejlesztő. Vagy Azure Active Directory szervezetekben az alkalmazásnak saját bérlőben kell bejelentkeznie a felhasználókba, ha az ISV-forgatókönyv. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókjaiban.
- Ha a felhasználók olyan közösségi identitásokkal jelentkeznek be, amelyek átadják a vállalattól a kereskedelmi (B2C) hatóságot és a szabályzatot, akkor csak az interaktív és a Felhasználónév-jelszó típusú hitelesítést használhatja.

## <a name="redirect-uris"></a>Átirányítási URI-k

Az asztali alkalmazásokban használandó átirányítási URI-k a használni kívánt folyamattól függenek.

Adja meg az alkalmazás átirányítási URI-JÁT úgy, hogy az Azure Portal **Alkalmazásregisztrációkban** [konfigurálja az alkalmazás platformjának beállításait](quickstart-register-app.md#add-a-redirect-uri) .

- Interaktív hitelesítést használó alkalmazások esetén:
  - Beágyazott böngészőket használó alkalmazások: `https://login.microsoftonline.com/common/oauth2/nativeclient`
  - Rendszerböngészőket használó alkalmazások: `http://localhost`

  > [!IMPORTANT]
  > Biztonsági szempontból ajánlott a explicit módon beállítani, `https://login.microsoftonline.com/common/oauth2/nativeclient` vagy `http://localhost` az átirányítási URI-t. Egyes hitelesítési könyvtárak, például a MSAL.NET alapértelmezett értéket használnak `urn:ietf:wg:oauth:2.0:oob` , ha nincs megadva más átirányítási URI, ami nem ajánlott. Ez az alapértelmezett érték a következő jelentős kiadásban feltört változásként frissül.

- Ha macOS-hez készült natív Objective-C vagy SWIFT alkalmazást hoz létre, regisztrálja az átirányítási URI-t az alkalmazás köteg-azonosítója alapján a következő formátumban: `msauth.<your.app.bundle.id>://auth` . Cserélje le `<your.app.bundle.id>` az alkalmazást az alkalmazás Bundle-azonosítójával.
- Ha Node.js Electron-alkalmazást hoz létre, a normál webes (https://) átirányítási URI helyett egyéni protokollt használjon, hogy kezelje az engedélyezési folyamat átirányítási lépését, például: `msal://redirect` . Az egyéni fájl protokolljának neve nem nyilvánvaló, hogy kitalálja, és követnie kell a [natív alkalmazások OAuth 2.0-specifikációjának](https://tools.ietf.org/html/rfc8252#section-7.1)javaslatait.
- Ha az alkalmazás kizárólag integrált Windows-hitelesítést vagy felhasználónevet és jelszót használ, nem kell regisztrálnia az alkalmazás átirányítási URI-JÁT. Ezek a folyamatok a Microsoft Identity platform 2.0-s végpontján keresztül egy oda-vissza. Az alkalmazás nem hívható vissza semmilyen konkrét URI-ra.
- Ha meg szeretné különböztetni az [eszköz kódjának áramlását](scenario-desktop-acquire-token.md#device-code-flow), az [integrált Windows-hitelesítést](scenario-desktop-acquire-token.md#integrated-windows-authentication), valamint egy olyan bizalmas ügyfélalkalmazás [felhasználónevét és jelszavát](scenario-desktop-acquire-token.md#username-and-password) , amely a [démon-alkalmazásokban](scenario-daemon-overview.md)használt ügyfél-hitelesítő adatokat használja, egyikhez sem kell átirányítási URI-t konfigurálnia, hanem nyilvános ügyfélalkalmazásként kell beállítania. A konfiguráció elérése:

    1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>válassza ki az alkalmazást **Alkalmazásregisztrációkban**, majd válassza a **hitelesítés** lehetőséget.
    1. A **Speciális beállítások**  >  **lehetővé teszik a nyilvános ügyfél-folyamatok** számára  >  **a következő mobil-és asztali folyamatok használatát: válassza az** **Igen** lehetőséget.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Nyilvános ügyfél beállításainak engedélyezése a Azure Portal hitelesítés paneljén":::

## <a name="api-permissions"></a>API-engedélyek

Asztali alkalmazások hívás API-kat a bejelentkezett felhasználó számára. Delegált engedélyeket kell kérniük. Nem igényelhetnek alkalmazás-engedélyeket, amelyek csak Daemon- [alkalmazásokban](scenario-daemon-overview.md)vannak kezelve.

## <a name="next-steps"></a>Következő lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben, az [alkalmazás kódjának konfigurációjában](scenario-desktop-app-configuration.md).
