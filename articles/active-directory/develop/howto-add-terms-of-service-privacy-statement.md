---
title: A szolgáltatási feltételek és az alkalmazások adatvédelmi nyilatkozata | Azure
description: Megtudhatja, hogyan konfigurálhatja az Azure AD használatára regisztrált alkalmazások használati feltételeit és adatvédelmi nyilatkozatát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 311dfd976610c392909a0ec3d91fecaa4d733539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104195"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Útmutató: alkalmazások használati feltételeinek és adatvédelmi nyilatkozatának konfigurálása

Az Azure Active Directory (Azure AD) és a Microsoft-fiókokkal integrált alkalmazásokat létrehozó és kezelő fejlesztőknek az alkalmazás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat kell tartalmazniuk. A használati feltételek és az adatvédelmi nyilatkozat a felhasználói beleegyező felületen keresztül történik. Segítenek a felhasználóknak tudni, hogy megbíznak az alkalmazásban. A használati feltételek és az adatvédelmi nyilatkozat különösen kritikus fontosságú a felhasználók számára elérhető több-bérlős alkalmazások esetében – a több könyvtár által használt alkalmazások vagy bármely Microsoft-fiók számára.

Ön felelős azért, hogy létrehozza az alkalmazás használati feltételeit és adatvédelmi nyilatkozatát, valamint hogy az URL-címeket megadja a dokumentumoknak. Azon több-bérlős alkalmazások esetében, amelyek nem tudják megadni ezeket a hivatkozásokat, az alkalmazás felhasználói beleegyezési élménye riasztást jelenít meg, amely miatt a felhasználók nem tudnak hozzájárulni az alkalmazáshoz.

> [!NOTE]
> * Az egybérlős alkalmazások nem fognak riasztást megjeleníteni.
> * Ha a két hivatkozás egyike vagy mindkettő hiányzik, az alkalmazás riasztást jelenít meg.

## <a name="user-consent-experience"></a>Felhasználói beleegyezett élmény

Az alábbi példák azt mutatják be, hogy a felhasználói beleegyezett a szolgáltatási feltételek és az adatvédelmi nyilatkozat konfigurálásakor, és ha nincsenek konfigurálva.

![Képernyőképek az adatvédelmi nyilatkozat és a megadott szolgáltatási feltételek nélkül](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>A használati feltételekre és az adatvédelmi nyilatkozatra mutató hivatkozások formázása

Mielőtt felveszi a hivatkozásokat az alkalmazás használati feltételeihez és az adatvédelmi nyilatkozat dokumentumaihoz, győződjön meg arról, hogy az URL-címek követik ezeket az irányelveket.

| Irányelv     | Leírás                           |
|---------------|---------------------------------------|
| Formátum        | Érvényes URL-cím                             |
| Érvényes sémák | HTTP és HTTPS<br/>Javasoljuk, hogy HTTPS |
| Maximális hossz    | 2048 karakter                       |

Példák: `https://myapp.com/terms-of-service` és `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>A szolgáltatási feltételekre és az adatvédelmi nyilatkozatra mutató hivatkozások hozzáadása

Ha a szolgáltatási feltételek és az adatvédelmi nyilatkozat elkészült, a következő módszerek egyikével adhatja hozzá a dokumentumokra mutató hivatkozásokat az alkalmazásban:

* [A Azure Portal](#azure-portal)
* [Az alkalmazás-objektum JSON használata](#app-object-json)
* [A Microsoft Graph API használata](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Az Azure Portal használata
Kövesse az alábbi lépéseket a Azure Portal.

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba</a> , és válassza ki a megfelelő Azure ad-bérlőt (nem B2C).
2. Navigáljon a **Alkalmazásregisztrációk** szakaszra, és válassza ki az alkalmazást.
3. A **kezelés** területen válassza a **branding (védjegyezés**) lehetőséget.
4. Adja meg a szolgáltatási URL-cím és az **adatvédelmi nyilatkozat URL-** mezőinek **feltételeit** .
5. Kattintson a **Mentés** gombra.

    ![Az alkalmazás tulajdonságai tartalmazzák a szolgáltatási és adatvédelmi nyilatkozat URL-címeit](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Az alkalmazás-objektum JSON használata

Ha közvetlenül az alkalmazás-objektum JSON-fájlját szeretné módosítani, használja a Azure Portal vagy az alkalmazás regisztrációs portáljának jegyzékfájl-szerkesztőjét, hogy tartalmazza az alkalmazás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat is.

1. Navigáljon az **alkalmazás regisztrációi** szakaszára, és válassza ki az alkalmazást.
2. Nyissa meg a **jegyzék** ablaktáblát.
3. CTRL + F, keressen rá a "informationalUrls" kifejezésre. Adja meg az adatokat.
4. Mentse a módosításokat.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>A Microsoft Graph API használata

Az összes alkalmazás programozott frissítéséhez a Microsoft Graph API-val frissítheti az összes alkalmazást, hogy tartalmazza a szolgáltatási feltételekre és az adatvédelmi nyilatkozatra mutató hivatkozásokat.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Ügyeljen arra, hogy ne írja felül a következő mezőkhöz hozzárendelt előre megadott értékeket: `supportUrl` , `marketingUrl` és `logoUrl`
> * A Microsoft Graph API csak akkor működik, ha Azure AD-fiókkal jelentkezik be. A személyes Microsoft-fiókok nem támogatottak.
