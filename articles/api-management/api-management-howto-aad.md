---
title: Fejlesztői fiókok engedélyezése Azure Active Directory használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan engedélyezheti a felhasználókat a API Management Azure Active Directory használatával.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2021
ms.author: apimpm
ms.openlocfilehash: 743a7e7d34457405aa4be42b196dc994506c6587
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035802"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory használatával az Azure API Managementben

Ez a cikk bemutatja, hogyan engedélyezheti a hozzáférést a fejlesztői portálhoz Azure Active Directory (Azure AD) felhasználói számára. Az útmutató azt is bemutatja, hogyan kezelheti az Azure AD-felhasználók csoportjait olyan külső csoportok hozzáadásával, amelyek tartalmazzák a felhasználókat.

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Azure API Management-példány importálása és közzététele. További információ: [Importálás és közzététel](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Fejlesztői fiókok engedélyezése az Azure AD használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ezt: ![Nyíl ikon.](./media/api-management-howto-aad/arrow.png).
3. Írja be az **API** kifejezést a keresőmezőbe.
4. Válassza ki **API Management szolgáltatásokat**.
5. Válassza ki az API Management-szolgáltatáspéldányát.
6. A **fejlesztői portál** területen válassza az **identitások** lehetőséget.
7. Válassza a felül a **+ Hozzáadás** lehetőséget.

    A jobb oldalon megjelenik az **identitás-szolgáltató hozzáadása** panel.
8. A **szolgáltató típusa** területen válassza a **Azure Active Directory** lehetőséget.

    A panelen megjelenő egyéb szükséges információk megadását lehetővé tevő vezérlők. A vezérlőelemek közé tartozik az **ügyfél-azonosító** és az **ügyfél titka**. (Ezen vezérlőkről a cikk későbbi részében talál információt.)
9. Jegyezze fel az **átirányítási URL-cím** tartalmát.
    

    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Identitás-szolgáltató hozzáadása Azure Portal":::
    > [!NOTE]
    > Két átirányítási URL-cím létezik:<br/>
    > **Átirányítási URL** – a API Management legújabb fejlesztői portálra mutat.<br/>
    > **Átirányítási URL-cím (elavult portál)** – a API Management elavult fejlesztői portálra mutat.
    >
    > Javasoljuk, hogy használja a fejlesztői portál legújabb átirányítási URL-címét.
   
10. Nyisson meg egy másik lapot a böngészőben. 
11. A [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) gombra kattintva regisztrálhat egy alkalmazást a Active Directoryban.
12. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
13. Válassza az **új regisztráció** lehetőséget. Az **alkalmazás regisztrálása** lapon állítsa be az értékeket az alábbiak szerint:
    
    * Adjon **nevet** egy értelmes névnek. például: *Developer-Portal*
    * **Ebben a szervezeti könyvtárban csak** a **támogatott fióktípus** beállítása a fiókokhoz. 
    * Állítsa be az **átirányítási URI** -t a 9. lépésben kapott értékre. 
    * Válassza a **regisztráció** lehetőséget. 

14.  Az alkalmazás regisztrálása után másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** lapról. 
15. Térjen vissza a API Management-példányra. Az **identitás-szolgáltató hozzáadása** ablakban illessze be az **alkalmazás (ügyfél) azonosító** értékét az **ügyfél-azonosító** mezőbe.
16. Váltson vissza az Azure AD-konfigurációra, majd válassza a **tanúsítványok & a titkok** elemet a **kezelés** alatt. Válassza az **új ügyfél titka** gombot. Adjon meg egy értéket a **Leírás** mezőben, válassza a **lejárat** lehetőséget, majd válassza a **Hozzáadás** elemet. Másolja az ügyfél titkos értékét az oldal elhagyása előtt. A következő lépésben szüksége lesz ezekre. 
17. A **kezelés** területen válassza a **hitelesítés** lehetőséget, majd válassza az **azonosító jogkivonatok** lehetőséget az **implicit engedélyezés** területen.
18. Lépjen vissza a API Management-példányra, illessze be a titkos kulcsot az **ügyfél titkos** mezőjébe.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a kulcs lejárta előtt frissíti az **ügyfél titkos** kulcsát. 
    >  
    >

19. Az **identitás-szolgáltató hozzáadása** ablak az **engedélyezett bérlők** szövegmezőt is tartalmazza. Itt adja meg azon Azure AD-példányok tartományait, amelyekhez hozzáférést szeretne biztosítani az API Management Service-példány API-jai számára. Több tartományt is elkülönítheti a sortörésekkel, szóközökkel vagy vesszővel.

    > [!NOTE]
    > Az **engedélyezett bérlők** szakaszban több tartományt is megadhat. Ahhoz, hogy egy felhasználó egy másik tartományból jelentkezzen be, mint az az eredeti tartomány, ahol az alkalmazás regisztrálva van, a különböző tartomány globális rendszergazdájának engedélyt kell adnia az alkalmazásnak a címtáradatok eléréséhez. Az engedély megadásához a globális rendszergazdának a következőnek kell lennie: a. Ugrás a `https://<URL of your developer portal>/aadadminconsent` következőre: (például https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Írja be annak az Azure AD-bérlőnek a tartománynevét, amelyhez hozzáférést szeretne biztosítani.
    > c. Válassza a **Küldés** lehetőséget. 

20.  A kívánt konfiguráció megadása után válassza a **Hozzáadás** lehetőséget.

A módosítások mentése után a megadott Azure AD-példányban lévő felhasználók bejelentkezhetnek a fejlesztői portálra a [Bejelentkezés a fejlesztői portálra Azure ad-fiókkal](#log_in_to_dev_portal)című témakör lépéseit követve.

## <a name="add-an-external-azure-ad-group"></a>Külső Azure AD-csoport hozzáadása

Miután engedélyezte az Azure AD-bérlő felhasználóinak hozzáférését, hozzáadhat Azure AD-csoportokat API Managementhoz. Ennek eredményeképpen a termék láthatóságát az Azure AD-csoportok használatával szabályozhatja.

Ha külső Azure AD-csoportot szeretne hozzáadni a APIM-hez, először el kell végeznie az előző szakaszt. Emellett a regisztrált alkalmazásnak hozzáférést kell biztosítania a Microsoft Graph API-hoz a `Directory.Read.All` következő lépések végrehajtásával: 

1. Térjen vissza az alkalmazás regisztrálásához, amely az előző szakaszban lett létrehozva.
2. Válassza az **API-engedélyek** lehetőséget, majd kattintson **az + engedély hozzáadása** lehetőségre. 
3. A **kérelem API-engedélyek** ablaktáblán válassza a **Microsoft API** -k fület, görgessen le, majd válassza ki a **Azure Active Directory gráf** csempét. Válassza az **alkalmazás engedélyei** lehetőséget, keresse meg a **címtár** kifejezést, majd válassza ki a **könyvtárat. Read. All** engedély. 
4. Kattintson a panel alján található **engedélyek hozzáadása** elemre, majd kattintson a **rendszergazdai jóváhagyás megadása a (z) {tenantname} számára** lehetőségre, hogy hozzáférést biztosítson a címtár összes felhasználója számára. 

Most hozzáadhat külső Azure AD-csoportokat a API Management példány **csoportok** lapjáról.

1. Válassza ki a **Csoportok** lapot.
2. Kattintson a **HRE hozzáadása** gombra.
    !["HRE csoport hozzáadása" gomb](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Válassza ki a hozzáadni kívánt csoportot.
4. Kattintson a **kiválasztás** gombra.

Külső Azure AD-csoport hozzáadása után megtekintheti és konfigurálhatja a tulajdonságait. Válassza ki a csoport nevét a **csoportok** lapon. Innen szerkesztheti a csoport **nevét** és **leírását** .
 
A konfigurált Azure AD-példány felhasználói már bejelentkezhetnek a fejlesztői portálra. Megtekinthetik és előfizethetnek bármely olyan csoportra, amelyben láthatók.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Fejlesztői portál – Azure AD-fiók hitelesítésének hozzáadása

A fejlesztői portálon jelentkezzen be a HRE-be a **Bejelentkezés gombra: OAuth** widget. A widget már szerepel a fejlesztői portál alapértelmezett tartalmának bejelentkezési oldalán.

Bár a rendszer automatikusan létrehoz egy új fiókot, amikor egy új felhasználó bejelentkezik a HRE-be, érdemes lehet ugyanezt a widgetet hozzáadni a regisztrációs laphoz.

A **regisztrációs űrlap: a OAuth** widget a OAuth való regisztrációhoz használt űrlapot jelöli.

> [!IMPORTANT]
> A HRE módosításainak érvénybe léptetéséhez újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Örökölt fejlesztői portál – bejelentkezés az Azure AD-vel

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Bejelentkezés a fejlesztői portálra egy Azure AD-fiókkal, amelyet az előző részekben konfigurált:

1. Nyisson meg egy új böngészőablakot a bejelentkezési URL-cím használatával a Active Directory alkalmazás konfigurációjában, és válassza a **Azure Active Directory** lehetőséget.

   ![Bejelentkezési oldal][api-management-dev-portal-signin]

1. Adja meg az Azure AD egyik felhasználójának hitelesítő adatait, majd válassza a **Bejelentkezés** lehetőséget.

   ![Bejelentkezés felhasználónévvel és jelszóval][api-management-aad-signin]

1. Ha további információra van szüksége, előfordulhat, hogy a rendszer regisztrációs űrlapra kéri. Fejezze be a regisztrációs űrlapot, és **válassza a regisztráció lehetőséget**.

   !["Regisztráció" gomb a regisztrációs űrlapon][api-management-complete-registration]

A felhasználó most bejelentkezett a fejlesztői portálra a API Management Service-példányához.

![A fejlesztői portál regisztrációjának befejezése után][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
