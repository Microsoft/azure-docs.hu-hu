---
title: Fejlesztői fiókok engedélyezése Azure Active Directory B2C használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan engedélyezheti a felhasználókat a API Management Azure Active Directory B2C használatával.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013546"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory B2C használatával az Azure API Managementben

## <a name="overview"></a>Áttekintés

A Azure Active Directory B2C egy felhőalapú identitáskezelési megoldás a fogyasztóknak szánt webes és mobil alkalmazásokhoz. A segítségével kezelheti a fejlesztői portálhoz való hozzáférést. Ez az útmutató bemutatja, hogy a API Management szolgáltatásban milyen konfiguráció szükséges a Azure Active Directory B2C integrálásához. A fejlesztői portál klasszikus Azure Active Directory használatával történő hozzáférésének engedélyezéséről a következő témakörben talál további információt: [fejlesztői fiókok engedélyezése Azure Active Directory használatával].

> [!NOTE]
> Az útmutató lépéseinek elvégzéséhez először Azure Active Directory B2C Bérlővel kell létrehoznia alkalmazást a alkalmazásban. Emellett készen kell lennie a regisztrációra és a bejelentkezési szabályzatokra. További információ: [Azure Active Directory B2C Overview (áttekintés]).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Fejlesztői fiókok engedélyezése Azure Active Directory B2C használatával

1. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a API Management-példányt.

   > [!NOTE]
   > Ha még nem hozott létre API Management Service-példányt, tekintse meg a [API Management Service-példány létrehozása][Create an API Management service instance] című témakört az [Azure API Management oktatóanyag első lépéseiben][Get started with Azure API Management].

1. Az **identitások** területen. Kattintson a felül található **+ Hozzáadás** gombra.

   A jobb oldalon megjelenik az **identitás-szolgáltató hozzáadása** panel. Válassza a **Azure Active Directory B2C** lehetőséget.
    
   ![AAD B2C hozzáadása identitás-szolgáltatóként][api-management-howto-add-b2c-identity-provider]

1. Másolja az **átirányítási URL-címet**.

   ![AAD B2C identitás-szolgáltató átirányítási URL-címe][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Egy új lapon nyissa meg Azure Active Directory B2C-bérlőjét a Azure Portal, és nyissa meg az **alkalmazások** panelt.

   ![Új 1. alkalmazás regisztrálása][api-management-howto-aad-b2c-portal-menu]

1. Új Azure Active Directory B2C alkalmazás létrehozásához kattintson a **Hozzáadás** gombra.

   ![Új alkalmazás regisztrálása 2][api-management-howto-aad-b2c-add-button]

1. Az **új alkalmazás** panelen adja meg az alkalmazás nevét. Válassza az **Igen** lehetőséget a **webalkalmazás/webes API** területen, majd válassza az **Igen** lehetőséget az **implicit folyamat engedélyezése** lehetőségnél. Ezután illessze be a 3. lépésben a **Válasz URL-cím** szövegmezőbe másolt **átirányítási URL-címet** .

   ![Új alkalmazás regisztrálása 3][api-management-howto-aad-b2c-app-details]

1. Ha az új fejlesztői portált (nem a régi fejlesztői portált) használja, adja meg a **megadott nevet**, a **vezetéknevet** és a **felhasználó objektumazonosítót** az alkalmazás jogcímeiben.

    ![Alkalmazásjogcímek](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Kattintson a **Létrehozás** gombra. Az alkalmazás létrehozásakor megjelenik az **alkalmazások** panelen. A részletek megtekintéséhez kattintson az alkalmazás nevére.

   ![Új alkalmazás regisztrálása 4][api-management-howto-aad-b2c-app-created]

1. A **Tulajdonságok** panelen másolja a vágólapra az **alkalmazás azonosítóját** .

   ![1. alkalmazás azonosítója][api-management-howto-aad-b2c-app-id]

1. Váltson vissza az API Management **identitás-szolgáltató hozzáadása** panelre, és illessze be az azonosítót az **ügyfél-azonosító** szövegmezőbe.
    
1.  Váltson vissza a B2C-alkalmazás regisztrálására, kattintson a **kulcsok** gombra, majd kattintson a **kulcs generálása** elemre. Kattintson a **Save (Mentés** ) gombra a konfiguráció mentéséhez és az **alkalmazás kulcsának** megjelenítéséhez. Másolja a kulcsot a vágólapra.

    ![1. alkalmazás kulcsa][api-management-howto-aad-b2c-app-key]

1.  Váltson vissza az API Management **identitás-szolgáltató hozzáadása** panelre, és illessze be a kulcsot az **ügyfél titka** szövegmezőbe.
    
1.  Adja meg az Azure Active Directory B2C-bérlő tartománynevét a **bejelentkezési-bérlőben**.

1.  A **szolgáltató mezőben megadhatja a használni** kívánt Azure ad B2C bejelentkezési URL-címet. Állítsa az értéket **<your_b2c_tenant_name>. b2clogin.com** értékre.

1. Határozza meg a **regisztrációs házirendet** és a **bejelentkezési** szabályzatot a B2C-bérlői házirendekben. Megadhatja a **profil szerkesztési házirendjét** és a jelszó- **visszaállítási szabályzatot** is.

1. A kívánt konfiguráció megadása után kattintson a **Mentés** gombra.

    A módosítások mentése után a fejlesztők új fiókokat hozhatnak létre, és bejelentkezhetnek a fejlesztői portálra Azure Active Directory B2C használatával.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Fejlesztői portál – Azure AD B2C fiók hitelesítésének hozzáadása

A fejlesztői portálon jelentkezzen be AAD B2C a bejelentkezés **gombra: OAuth** widget. A widget már szerepel a fejlesztői portál alapértelmezett tartalmának bejelentkezési oldalán.

Bár a rendszer automatikusan létrehoz egy új fiókot, amikor egy új felhasználó bejelentkezik AAD B2Cba, érdemes lehet ugyanezt a widgetet hozzáadni a regisztrációs laphoz.

A **regisztrációs űrlap: a OAuth** widget a OAuth való regisztrációhoz használt űrlapot jelöli.

> [!IMPORTANT]
> A HRE módosításainak érvénybe léptetéséhez újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Örökölt fejlesztői portál – regisztrálás a Azure AD B2C használatával

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Ha Azure Active Directory B2C használatával szeretne regisztrálni egy fejlesztői fiókra, nyisson meg egy új böngészőablakot, és nyissa meg a fejlesztői portált. Kattintson a **Sign up (regisztráció** ) gombra.

   ![Fejlesztői portál 1][api-management-howto-aad-b2c-dev-portal]

2. Válassza a **Azure Active Directory B2C**-regisztrációt.

   ![Fejlesztői portál 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. A rendszer átirányítja az előző szakaszban konfigurált regisztrációs szabályzatra. Az e-mail-cím vagy az egyik meglévő közösségi fiók használatával regisztrálhat.

   > [!NOTE]
   > Ha Azure Active Directory B2C az egyetlen lehetőség, amely engedélyezve van a közzétevő portál **identitások** lapján, a rendszer közvetlenül átirányítja a regisztrációs szabályzatba.

   ![Fejlesztői portál][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Ha a regisztráció befejeződött, a rendszer átirányítja a fejlesztői portálra. Most bejelentkezett a API Management Service-példány fejlesztői portálján.

    ![A regisztráció befejeződött][api-management-registration-complete]

## <a name="next-steps"></a>Következő lépések

*  [Azure Active Directory B2C áttekintése]
*  [Azure Active Directory B2C: bővíthető házirend-keretrendszer]
*  [Microsoft-fiók használata identitás-szolgáltatóként a Azure Active Directory B2C]
*  [Google-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]
*  [LinkedIn-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]
*  [Facebook-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C áttekintése]: ../active-directory-b2c/overview.md
[Fejlesztői fiókok engedélyezése Azure Active Directory használatával]: ./api-management-howto-aad.md
[Azure Active Directory B2C: bővíthető házirend-keretrendszer]: ../active-directory-b2c/user-flow-overview.md
[Microsoft-fiók használata identitás-szolgáltatóként a Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Google-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Facebook-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[LinkedIn-fiók használata identitás-szolgáltatóként Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
