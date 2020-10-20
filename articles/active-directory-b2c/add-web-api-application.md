---
title: Webes API-alkalmazás hozzáadása – Azure Active Directory B2C | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá webes API-alkalmazásokat a Active Directory B2C bérlőhöz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 5473730484240f2c813c9ae279e1516ed93a45c5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92214957"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Webes API-alkalmazás hozzáadása a Azure Active Directory B2C-bérlőhöz

 Regisztrálja webes API-erőforrásait a bérlőben, hogy elfogadják és válaszoljanak a hozzáférési jogkivonatot futtató ügyfélalkalmazások által benyújtott kérelmekre. Ez a cikk bemutatja, hogyan regisztrálhat egy webes API-t Azure Active Directory B2C (Azure AD B2C).

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapi1*.
1. Az **átirányítási URI**területen válassza a **web**lehetőséget, majd adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Éles alkalmazásokban az átirányítási URI-t a végponthoz hasonlóan állíthatja be `https://localhost:5000` . A fejlesztés vagy a tesztelés során beállíthatja, hogy a egy `https://jwt.ms` Microsoft tulajdonú webalkalmazás, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.
1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** a webes API kódjában való használathoz.

Ha olyan alkalmazással rendelkezik, amely megvalósítja az implicit engedélyezési folyamatot, például egy [JavaScript-alapú egyoldalas alkalmazást (Spa)](tutorial-register-spa.md), akkor a következő lépéseket követve engedélyezheti a folyamatot:

1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Az **implicit engedélyezés**területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Válassza a **Mentés** lehetőséget.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adjon nevet az alkalmazásnak. Például: *webapi1*.
6. A **Web App/web API belefoglalása** és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
7. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Az éles alkalmazásban beállíthatja a válasz URL-címét egy értékre (például `https://localhost:44332` ). Tesztelési célból állítsa be a válasz URL-címét a következőre: `https://jwt.ms` .
8. Az **alkalmazás-azonosító URI-ja**mezőben adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Létrehozás** lehetőségre.
10. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.

* * *

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Például az [oktatóanyagban: alkalmazás regisztrálása Azure Active Directory B2Cban](tutorial-register-applications.md), a *webapp1* nevű webalkalmazás regisztrálva van a Azure ad B2Cban. Ezt az alkalmazást használhatja a webes API meghívásához.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az alkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik az alkalmazást. Az alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.
