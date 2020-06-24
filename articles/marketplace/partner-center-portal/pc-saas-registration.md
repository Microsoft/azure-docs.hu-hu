---
title: SaaS-alkalmazás regisztrálása – Azure Marketplace
description: Ismerje meg, hogyan regisztrálhat egy SaaS-alkalmazást a Azure Portal használatával, és hogyan fogadhat egy Azure Active Directory biztonsági jogkivonatot.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: dsindona
ms.openlocfilehash: 0201ea7b207b7d4c0eaa56de1ee062ea405f0bbb
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119239"
---
# <a name="register-a-saas-application"></a>SaaS-alkalmazás regisztrálása

Ez a cikk azt ismerteti, hogyan regisztrálhat egy SaaS-alkalmazást a Microsoft [Azure Portal](https://portal.azure.com/) használatával, és hogyan kérheti le a közzétevő hozzáférési tokenjét (Azure Active Directory hozzáférési tokent). A közzétevő ezt a tokent fogja használni az SaaS-alkalmazás hitelesítéséhez a SaaS-teljesítési API-k meghívásával.  A teljesítési API-k a OAuth 2,0 ügyfél hitelesítő adatait használják Azure Active Directory (1.0-s) végpontokon történő adatforgalom biztosítására a szolgáltatások közötti hozzáférési jogkivonat kéréséhez.

Az Azure Marketplace nem kényszeríti az SaaS-szolgáltatás által a végfelhasználók számára használt hitelesítési módszer korlátozásait. Az alábbi folyamat csak az SaaS szolgáltatás Azure piactéren történő hitelesítéséhez szükséges.

További információ az Azure AD-ről (Active Directory): [Mi az a hitelesítés](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)?

## <a name="register-an-azure-ad-secured-app"></a>Azure AD-védelemmel ellátott alkalmazás regisztrálása

Az Azure AD képességeit felhasználó alkalmazásokat először regisztrálni kell egy Azure AD-bérlőben. Ez a regisztrációs folyamat magában foglalja az Azure AD néhány részletét az alkalmazásról. Ha az Azure Portal használatával szeretne regisztrálni egy új alkalmazást, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ha a fiókja többhöz biztosít hozzáférést, kattintson a fiókra a jobb felső sarokban, és állítsa be a portál-munkamenetet a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásra, majd a **Alkalmazásregisztrációk**elemre, majd az **új alkalmazás regisztrálása**elemre.

    ![SaaS AD-alkalmazások regisztrációi](./media/saas-offer-app-registration-v1.png)

4. A létrehozás lapon adja meg az alkalmazás \' regisztrációs adatait:
    -   **Név**: adjon meg egy értelmes alkalmazásnév-nevet
    -   **Alkalmazás típusa**:  
        
        Válassza a **webalkalmazás/API** lehetőséget a biztonságos kiszolgálóra telepített [ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) és [erőforrás-/API-alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) számára. Ezzel a beállítással OAuth a bizalmas [webes ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) és a nyilvános [felhasználói ügynökön alapuló ügyfelek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Egyazon alkalmazás az ügyfelet és az erőforrást/API-t is elérhetővé teheti.

        A webalkalmazások konkrét példái között tekintse meg az [Azure ad fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/)első [lépések](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) szakaszában elérhető gyors útmutatókat.

5. Ha elkészült, kattintson a **regisztráció**gombra.  Az Azure AD egy egyedi *alkalmazás-azonosítót* rendel az új alkalmazáshoz. Javasoljuk, hogy regisztráljon egy olyan alkalmazást, amely csak az API-t és egyetlen bérlőt is elér.

6. Az ügyfél titkos kulcsának létrehozásához navigáljon a **tanúsítványok & titkok lapra** , és kattintson az **+ új ügyfél titka**elemre.  Ügyeljen arra, hogy a titkos értéket a kódban használt értékre másolja.

Az **Azure ad-alkalmazás azonosítója** a KÖZZÉTEVŐ-azonosítóhoz van társítva, ezért győződjön meg arról, hogy ugyanazt az *alkalmazás-azonosítót* használja az összes ajánlatban.

>[!Note]
>Ha egy közzétevő két különböző fiókkal rendelkezik a partner Centerben, két különböző Azure AD-alkalmazás-azonosítót kell használnia.  A fiókpartner minden fiókpartner-fiókjának egyedi Azure AD-alkalmazás-azonosítót kell használnia az ezen a fiókon keresztül közzétett SaaS-ajánlatok esetében.

## <a name="how-to-get-the-publishers-authorization-token"></a>A közzétevő engedélyezési jogkivonatának beszerzése

Az alkalmazás regisztrálását követően programozott módon kérheti le a közzétevő engedélyezési jogkivonatát (Azure AD-hozzáférési jogkivonat az Azure AD v1-végpont használatával). A közzétevőnek ezt a jogkivonatot kell használnia a különböző SaaS-teljesítési API-k meghívásakor. Ez a jogkivonat csak egy órára érvényes. 

További információ ezekről a jogkivonatokról: [Azure Active Directory hozzáférési tokenek](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).  Vegye figyelembe, hogy a folyamat a v1 végponti tokent használja.

### <a name="get-the-token-with-an-http-post"></a>A token lekérése HTTP-BEJEGYZÉSsel

#### <a name="http-method"></a>HTTP-metódus

Közzététel<br>

##### <a name="request-url"></a>*URL-cím kérése* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI-paraméter*

|  Paraméter neve    |  Kötelező         |  Leírás |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True (Igaz)      |  A regisztrált HRE-alkalmazás bérlői azonosítója. |

##### <a name="request-header"></a>*Kérelem fejléce*

|  Fejléc neve       |  Kötelező         |  Leírás |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True (Igaz)      |  A kérelemhez társított tartalomtípus. Az alapértelmezett érték `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Kérelem törzse*

|  Tulajdonság neve     |  Kötelező         |  Leírás |
|  ---------------   |  ---------------  | ------------ |
|  `grant-type`      |  True (Igaz)      |  Adja meg a típust. A `"client_credentials"` címet használja. |
|  `client_id`       |  True (Igaz)      |  Az Azure AD-alkalmazáshoz társított ügyfél/alkalmazás-azonosító. |
|  `client_secret`   |  True (Igaz)      |  Az Azure AD-alkalmazáshoz társított titkos kulcs. |
|  `resource`        |  True (Igaz)      |  A célként megadott erőforrás, amelyre a tokent kérték. Akkor használja, `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` Ha a Marketplace SaaS API mindig a cél erőforrás ebben az esetben. |

##### <a name="response"></a>*Válasz*

|  Name     |  Típus         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  A kérelem sikeres volt. |

##### <a name="tokenresponse"></a>*TokenResponse*

Példa a válaszra:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

A `"access_token"` válaszban szereplő mező értéke az, amelyet a rendszer az `<access_token>` összes SaaS-beteljesülés és a Marketplace-mérési API-k meghívásakor ad át engedélyezési paraméterként.

## <a name="next-steps"></a>További lépések

Az Azure AD-védelemmel ellátott alkalmazás mostantól használhatja a [SaaS-teljesítési API 2-es verzióját](./pc-saas-fulfillment-api-v2.md).
