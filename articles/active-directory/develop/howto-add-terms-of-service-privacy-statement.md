---
title: Az alkalmazások és az adatvédelmi nyilatkozat feltételeit |} Az Azure
description: Ismerje meg, hogy hogyan konfigurálhat a regisztrált Azure AD használata az alkalmazások és az adatvédelmi nyilatkozat feltételeit.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482866"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Útmutató: Az alkalmazás és az adatvédelmi nyilatkozat feltételeit konfigurálása

Fejlesztők számára, akik alkalmazásokat, amelyekbe beépül az Azure Active Directory (Azure AD) és a Microsoft-fiókok kezelése tartalmaznia kell az alkalmazás használati és az adatvédelmi nyilatkozat mutató hivatkozásokat. A felhasználók számára a felhasználói jóváhagyási felületen keresztül illesztett és az adatvédelmi nyilatkozat feltételeit. Ezek segítenek a felhasználókkal, hogy nyugodtan megbízhatják az alkalmazást. A feltételeket és adatvédelmi nyilatkozata különösen létfontosságúak a felhasználók felé néző több-bérlős alkalmazások – alkalmazásokat, amelyek több címtárat használ vagy bármilyen Microsoft-fiókkal érhetők el.

Ön felelős létrehozása a használati feltételekre és az adatvédelmi nyilatkozat dokumentumok az alkalmazáshoz, és ezeket a dokumentumokat, az URL-címeket, amelyek biztosítják. A több-bérlős alkalmazások, amelyek nem adja meg ezeket a hivatkozásokat a felhasználói jóváhagyási felületen az alkalmazás egy riasztást, amely előfordulhat, hogy ismeretlen hozzájárul ahhoz, hogy az alkalmazás jelennek meg.

> [!NOTE]
> * Egybérlős alkalmazások nem jelennek meg a riasztást.
> * Ha egyik vagy mindkét a két kapcsolat hiányzik, akkor az alkalmazás riasztást jelennek meg.

## <a name="user-consent-experience"></a>Felhasználói jóváhagyás élmény

Az alábbi példák bemutatják a felhasználói élmény hozzájárulás, ha a feltételeket és adatvédelmi nyilatkozata vannak konfigurálva, és ha ezek a hivatkozások nem konfigurált.

![Képernyőképek és a egy adatvédelmi nyilatkozat és a megadott szolgáltatási feltételeit nélkül](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>A feltételeket és adatvédelmi nyilatkozata dokumentumok formázási hivatkozások

Mielőtt hivatkozásokat ad hozzá az alkalmazás használati és az adatvédelmi nyilatkozat dokumentumok, ellenőrizze az URL-címeket az alábbiakra.

| Irányelv     | Leírás                           |
|---------------|---------------------------------------|
| Formátum        | Érvényes URL-cím                             |
| Érvényes sémák | HTTP és HTTPS<br/>Azt javasoljuk, hogy HTTPS |
| Maximális hossz    | 2048 karakternél                       |

Példák: `https://myapp.com/terms-of-service` és `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hivatkozások hozzáadása a feltételeket és adatvédelmi nyilatkozata

Amikor készen áll a szolgáltatás és az adatvédelmi nyilatkozat feltételeit, adhat hozzá hivatkozásokat ezeket a dokumentumokat az alkalmazásban az alábbi módszerek egyikének használatával:

* [Az Azure Portalon keresztül](#azure-portal)
* [Az alkalmazás objektum JSON használatával](#app-object-json)
* [Az MSGraph beta REST API használatával](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Az Azure portal használatával
Kövesse az alábbi lépéseket az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg a **Alkalmazásregisztrációk** szakaszt, és válassza ki az alkalmazást.
3. Nyissa meg a **Branding** ablaktáblán.
4. Töltse ki a **feltételek az URL-címe** és **adatvédelmi nyilatkozatának URL-címe** mezőket.
5. Mentse a módosításokat.

    ![Alkalmazás tulajdonságainak tartalmaz feltételeit és adatvédelmi nyilatkozat URL-címek](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Az alkalmazás objektum JSON használatával

Ha az alkalmazás objektum JSON közvetlenül módosítani szeretné, az Azure Portalon vagy az alkalmazásregisztrációs portálon az alkalmazásjegyzék-szerkesztőben segítségével olyan hivatkozásoknak, amelyek az alkalmazás és az adatvédelmi nyilatkozat feltételeit.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Az MSGraph beta REST API használatával

Programozott módon frissíti alkalmazásait, az MSGraph beta REST API-val feltételeit és adatvédelmi nyilatkozata dokumentumok mutató hivatkozásokat tartalmazza az alkalmazások frissítéséhez használhatja.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
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
> * Ügyeljen arra, hogy nem minden hozzárendelt mezőt előre meglévő érték felülírása: `supportUrl`, `marketingUrl`, és `logoUrl`
> * Az MSGraph beta REST API-t csak akkor fog működni, ha egy Azure AD-fiókkal jelentkezik be. Személyes Microsoft-fiókok nem támogatottak.
