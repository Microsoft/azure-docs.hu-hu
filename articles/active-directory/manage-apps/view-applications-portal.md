---
title: 'Rövid útmutató: Azon alkalmazások listájának megtekintése, amelyek a Azure Active Directory (Azure AD) bérlőt használják az identitáskezeléshez'
description: Ebben a rövid útmutatóban a Azure Portal segítségével megtekintheti azon alkalmazások listáját, amelyek regisztrálva vannak az Azure Active Directory-bérlő (Azure AD) identitáskezeléshez való használatára.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: c4a03e85798d4406068bd5923dbc753d8e5d31b6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376443"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Rövid útmutató: Azon alkalmazások listájának megtekintése, amelyek a Azure Active Directory (Azure AD) bérlőt használják az identitáskezeléshez

Az Azure AD használatának első lépések az identitás- és hozzáférés-kezelési (IAM)-rendszerként a szervezet által használt alkalmazásokhoz. Ebben a rövid útmutatóban megtekintheti az alkalmazásokat, más néven alkalmazásokat, amelyek már be vannak állítva az Azure AD-bérlő identitásszolgáltatóként (IdP) való használatára.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőben regisztrált alkalmazások megtekintéséhez a következőre van szükség:

- Egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

>[!IMPORTANT]
>Javasoljuk, hogy ne éles környezetben tesztelje a rövid útmutató lépéseit.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Keresse meg a bérlőben található alkalmazások listáját

Az Azure AD-bérlőben regisztrált alkalmazások a  vállalati alkalmazások szakaszban hetők meg a Azure Portal.

A bérlőben regisztrált alkalmazások megtekintése:

1. Jelentkezzen be a [Azure Portal.](https://portal.azure.com)
2. A bal oldali navigációs panelen válassza **a** Azure Active Directory.
3. A **Azure Active Directory** válassza a Vállalati **alkalmazások lehetőséget.**
4. Az Alkalmazás **típusa legördülő** menüben válassza a **Minden alkalmazás** lehetőséget, majd az Alkalmaz **lehetőséget.** Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
5. További alkalmazások megtekintéséhez válassza **a** lista alján található További betöltése lehetőséget. Ha a bérlőben számos alkalmazás található, egyszerűbb lehet rákeresni egy adott alkalmazásra ahelyett, hogy végiggörgeti a listát. Egy adott alkalmazás keresését a rövid útmutató egy későbbi, későbbi, erre vonatkozó útmutatója is be fog fedni.

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Válassza ki a kívánt beállításokat.

1. Az alkalmazásokat megtekintheti **alkalmazástípus,** **alkalmazásállapot** és alkalmazás **láthatósága szerint.**
2. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:
    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.
3. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.
4. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **Rejtett beállítás** azokat az alkalmazásokat jeleníti meg, amelyek a bérlőben vannak, de a felhasználók számára nem láthatók.
5. A kívánt beállítások kiválasztása után válassza az **Alkalmaz lehetőséget.**

## <a name="search-for-an-application"></a>Alkalmazás keresése

Adott alkalmazás keresése:

1. Az Alkalmazás **típusa menüben** válassza a **Minden alkalmazás** lehetőséget, majd válassza az **Alkalmaz lehetőséget.**
2. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás hozzá lett adva az Azure AD-bérlőhöz, az megjelenik a keresési eredmények között. Ez a példa azt mutatja, hogy a GitHub nem lett hozzáadva a bérlői alkalmazásokhoz.
    ![A példa azt mutatja, hogy egy alkalmazás nem lett hozzáadva a bérlőhöz](media/view-applications-portal/search-for-tenant-application.png)
3. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.
    ![Példa az összes olyan alkalmazásra, amely a Sales (Értékesítés) kezdetből indul](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: Az alkalmazáskezelés [automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban nem hoz létre új erőforrásokat, így semmit sem kell megtisztítani.

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan használhatja az Azure AD-t identitásszolgáltatóként egy alkalmazáshoz.
> [!div class="nextstepaction"]
> [Alkalmazás felvétele](add-application-portal.md)