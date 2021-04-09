---
title: Az Azure AD-alkalmazás korlátozása felhasználók készletére | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan korlátozhatja az Azure AD-ban regisztrált alkalmazásaihoz való hozzáférést egy kiválasztott felhasználói csoportba.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199596"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Útmutató: az Azure AD-alkalmazás korlátozása egy Azure AD-bérlőben lévő felhasználók köré

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők.

Hasonlóképpen, a [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazások esetében az Azure ad-bérlő minden olyan felhasználója hozzáférhet az alkalmazáshoz, amikor sikeresen hitelesítik magukat a megfelelő bérlőn.

A bérlői rendszergazdák és a fejlesztők gyakran rendelkeznek olyan követelményekkel, amelyekben az alkalmazásnak egy adott felhasználói csoportra kell korlátozódnia. A fejlesztők ugyanúgy használhatják a népszerű engedélyezési mintákat, mint az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC), de ez a megközelítés jelentős mennyiségű munkát igényel a fejlesztő részéről.

A bérlői rendszergazdák és fejlesztők az Azure AD ezen beépített funkciójának használatával korlátozhatják az alkalmazásokat a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára is.

## <a name="supported-app-configurations"></a>Támogatott alkalmazások konfigurációi

Egy adott alkalmazás a következő típusú alkalmazásokkal rendelkező felhasználók vagy biztonsági csoportok meghatározott csoportjára való korlátozására használható:

- Az SAML-alapú hitelesítéssel összevont egyszeri bejelentkezésre konfigurált alkalmazások.
- Az Azure AD előzetes hitelesítését használó alkalmazásproxy-alkalmazások.
- A közvetlenül az Azure AD alkalmazás platformján létrehozott alkalmazások, amelyek a OAuth 2.0/OpenID Connect hitelesítést használják, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba.

     > [!NOTE]
     > Ez a funkció csak a Web App/web API-hoz és a vállalati alkalmazásokhoz érhető el. A [natívként](./quickstart-register-app.md) regisztrált alkalmazások nem korlátozhatók a bérlőben lévő felhasználók vagy biztonsági csoportok csoportjára.

## <a name="update-the-app-to-enable-user-assignment"></a>Az alkalmazás frissítése a felhasználó-hozzárendelés engedélyezéséhez

Az engedélyezett felhasználói hozzárendeléssel kétféleképpen hozhatók létre alkalmazások. Egy **globális rendszergazdai** szerepkörre van szükség, a második nem.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Vállalati alkalmazások (globális rendszergazdai szerepkört igényel)

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> **globális rendszergazdaként**.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez felhasználót vagy biztonsági csoportot szeretne hozzárendelni a listából. 
    Egy adott alkalmazás megkereséséhez használja az ablak tetején található szűrőket.
1. Az alkalmazás **Áttekintés** lapjának **kezelés** területén válassza a **Tulajdonságok** elemet.
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen** értékre. Ha ezt a beállítást az **Igen** értékre állítja, akkor a bérlőben lévő felhasználókat először hozzá kell rendelni ehhez az alkalmazáshoz, vagy nem lesznek képesek bejelentkezni az alkalmazásba.
1. Kattintson a **Mentés** gombra.

### <a name="app-registration"></a>Alkalmazásregisztráció

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Hozza létre vagy válassza ki a kezelni kívánt alkalmazást. Az alkalmazás **tulajdonosának** kell lennie.
1. Az alkalmazás **áttekintése** lapon válassza ki a **felügyelt alkalmazást a helyi címtár** hivatkozásra az **alapvető** erőforrások szakaszban.
1. A **Kezelés** területen válassza a **Tulajdonságok** elemet.
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen** értékre. Ha ezt a beállítást az **Igen** értékre állítja, akkor a bérlőben lévő felhasználókat először hozzá kell rendelni ehhez az alkalmazáshoz, vagy nem lesznek képesek bejelentkezni az alkalmazásba.
1. Kattintson a **Mentés** gombra.

## <a name="assign-users-and-groups-to-the-app"></a>Felhasználók és csoportok társítása az alkalmazáshoz

Ha úgy konfigurálta az alkalmazást, hogy engedélyezze a felhasználó-hozzárendelést, akkor a felhasználók és csoportok az alkalmazáshoz rendelhetők.

1. A **kezelés** területen válassza ki a **felhasználók és csoportok**  >  **hozzáadása felhasználót/csoportot** .
1. Válassza ki a **felhasználók** választót. 

     A felhasználók és a biztonsági csoportok listája a szövegmezővel együtt jelenik meg egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ezen a képernyőn egyszerre több felhasználót és csoportot is kijelölhet.

1. Ha elkészült a felhasználók és a csoportok kiválasztásával, válassza a **kiválasztás** lehetőséget.
1. Választható Ha az alkalmazásban definiált alkalmazás-szerepköröket használ, a kiválasztott felhasználók és csoportok hozzárendeléséhez a **szerepkör kiválasztása** lehetőséget használhatja. 
1. Válassza a **hozzárendelés** lehetőséget, hogy elvégezze a felhasználók és csoportok hozzárendelését az alkalmazáshoz. 
1. Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a frissített **felhasználók és csoportok** listában.

## <a name="more-information"></a>További információ

- [Útmutató: alkalmazások szerepköreinek hozzáadása az alkalmazáshoz](./howto-add-app-roles-in-azure-ad-apps.md)
- [Engedélyezés hozzáadása az alkalmazás szerepköreinek & a szerepkörök jogcímeinek használata egy ASP.NET Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Biztonsági csoportok és alkalmazás-szerepkörök használata az alkalmazásokban (videó)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, a Group jogcímek és az alkalmazás szerepköreivel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-alkalmazás jegyzékfájlja](./reference-app-manifest.md)
