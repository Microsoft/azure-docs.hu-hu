---
title: 'Rövid útmutató: a Azure Active Directory (Azure AD) bérlőt használó alkalmazások listájának megtekintése'
description: Ebben a rövid útmutatóban a Azure Portal segítségével megtekintheti azoknak az alkalmazásoknak a listáját, amelyek regisztrálva vannak a Azure Active Directory (Azure AD) bérlő az Identitáskezelés során való használatára.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: 4bf0353148a5f8474270b314a85d55c3cfd753ab
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257561"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Rövid útmutató: a Azure Active Directory (Azure AD) bérlőt használó alkalmazások listájának megtekintése

Az Azure AD használatának első lépései a szervezet által használt alkalmazások identitás-és hozzáférés-kezelési (IAM) rendszereként. Ebben a rövid útmutatóban megtekintheti az alkalmazásokat, más néven alkalmazásokat, amelyek már úgy vannak beállítva, hogy az Azure AD-bérlőt használják identitás-szolgáltatóként (identitásszolgáltató).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőben regisztrált alkalmazások megtekintéséhez a következőkre lesz szüksége:

- Egy Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.

## <a name="find-the-list-of-applications-in-your-tenant"></a>A bérlőben található alkalmazások listájának megkeresése

Az Azure AD-bérlőben regisztrált alkalmazások a Azure Portal **vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlőben regisztrált alkalmazások megtekintése:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
3. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások** lehetőséget.
4. Az **alkalmazás típusa** legördülő menüben válassza a **minden alkalmazás** lehetőséget, majd kattintson az **alkalmaz** gombra. Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
5. További alkalmazások megtekintéséhez válassza a lista alján található **továbbiak betöltés** lehetőséget. Ha a bérlő számos alkalmazást tartalmaz, könnyebben kereshet egy adott alkalmazást a lista görgetése helyett. Az adott alkalmazásra vonatkozó keresés a rövid útmutató későbbi részében szerepel.

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Válassza ki a beállításokat a keresett elemek alapján.

1. Az alkalmazásokat az **alkalmazás típusa**, az **alkalmazás állapota** és az **alkalmazás láthatósága** alapján tekintheti meg.
2. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:
    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.
3. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.
4. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **rejtett** beállítás megjeleníti a bérlőben található alkalmazásokat, de a felhasználók számára nem láthatók.
5. A kívánt beállítások kiválasztása után válassza az **alkalmaz** lehetőséget.

## <a name="search-for-an-application"></a>Alkalmazás keresése

Egy adott alkalmazás megkeresése:

1. Az **alkalmazás típusa** menüben válassza a **minden alkalmazás** lehetőséget, majd kattintson az **alkalmaz** gombra.
2. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás hozzá lett adva az Azure AD-bérlőhöz, az megjelenik a keresési eredmények között. Ez a példa azt mutatja, hogy a GitHub még nem lett hozzáadva a bérlői alkalmazásokhoz.
    ![Példa egy olyan alkalmazást mutat be, amely még nem lett hozzáadva a bérlőhöz](media/view-applications-portal/search-for-tenant-application.png)
3. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.
    ![Példa az összes értékesítést elindító alkalmazásra](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban nem hozott létre új erőforrásokat, ezért nem kell megtisztítani.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan használhatja az Azure AD-t az alkalmazások identitás-szolgáltatójának használatával.
> [!div class="nextstepaction"]
> [Alkalmazás felvétele](add-application-portal.md)