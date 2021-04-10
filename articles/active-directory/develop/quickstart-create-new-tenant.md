---
title: 'Rövid útmutató: Azure Active Directory bérlő létrehozása'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Active Directory bérlőt olyan alkalmazások fejlesztéséhez, amelyek a hitelesítéshez és engedélyezéshez használják a Microsoft Identity platformot.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100535161"
---
# <a name="quickstart-set-up-a-tenant"></a>Gyorsútmutató: Bérlő beállítása

Ha a Microsoft Identity platformot használó alkalmazásokat szeretné létrehozni az identitás-és hozzáférés-kezeléshez, hozzá kell férnie egy Azure Active Directory (Azure AD) *bérlőhöz*. Ez az Azure AD-bérlőben regisztrálhatja és kezelheti az alkalmazásait, konfigurálhatja Microsoft 365 és más webes API-khoz tartozó adataihoz való hozzáférést, és engedélyezheti a feltételes hozzáféréshez hasonló szolgáltatásokat.

A bérlő egy szervezetet jelöl. Ez az Azure AD dedikált példánya, amelyet a szervezet vagy az alkalmazás fejlesztője a Microsofttal való kapcsolat elején kap. Ez a kapcsolat az Azure-ra, Microsoft Intunera vagy Microsoft 365re való feliratkozással kezdődhet, például:.

Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. Saját képviselettel rendelkezik a munkahelyi és iskolai identitások, fogyasztói identitások (ha Azure AD B2C bérlő) és az alkalmazások regisztrációja. A bérlőn belüli alkalmazás-regisztráció lehetővé teszi, hogy csak a bérlőn vagy az összes bérlőn belüli fiókok hitelesítő adatai legyenek.

## <a name="prerequisites"></a>Előfeltételek

Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>A környezet típusának meghatározása

Kétféle környezetet hozhat létre. A környezet kizárólag azon felhasználók típusaitól függ, amelyeket az alkalmazás hitelesíteni fog. 

Ez a rövid útmutató a felépíteni kívánt alkalmazás típusának két forgatókönyvét tárgyalja:

* Munkahelyi és iskolai (Azure AD-) fiókok vagy Microsoft-fiókok (például Outlook.com és Live.com)
* Közösségi és helyi (Azure AD B2C) fiókok

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Munkahelyi és iskolai fiókok, vagy személyes Microsoft-fiókok

A munkahelyi és iskolai fiókok, illetve a személyes Microsoft-fiókok környezetének létrehozásához használhat meglévő Azure AD-bérlőt, vagy létrehozhat egy újat.
### <a name="use-an-existing-azure-ad-tenant"></a>Meglévő Azure AD-bérlő használata

Számos fejlesztő rendelkezik olyan szolgáltatásokkal vagy előfizetésekkel, amelyek az Azure AD-bérlőhöz vannak kötve, például Microsoft 365 vagy Azure-előfizetésekhez.

A bérlő ellenõrzése:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>. Használja az alkalmazás felügyeletéhez használni kívánt fiókot.
1. Győződjön meg a jobb felső sarokban. Ha Bérlővel rendelkezik, automatikusan bejelentkezik. A bérlő neve közvetlenül a fiók neve alatt jelenik meg.
   * Vigye a kurzort a fiókja nevére, hogy megtekintse a nevét, e-mail-címét, könyvtárát vagy bérlői AZONOSÍTÓját (GUID) és tartományát.
   * Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik.

> [!TIP]
> A bérlő AZONOSÍTÓjának megkereséséhez a következőket teheti:
> * A címtár vagy a bérlő AZONOSÍTÓjának lekéréséhez vigye a kurzort a fiók nevére.
> * Keresse meg és válassza ki **Azure Active Directory**  >  **Tulajdonságok**  >  **bérlői azonosítóját** a Azure Portalban.

Ha nem rendelkezik a fiókjához társított Bérlővel, akkor a fiók neve alatt egy GUID-t fog látni. Nem fog tudni műveleteket végezni, például az alkalmazások regisztrálását, amíg nem hoz létre Azure AD-bérlőt.

### <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Ha még nem rendelkezik Azure AD-Bérlővel, vagy ha új fejlesztést szeretne létrehozni, tekintse meg az [új bérlő létrehozása az Azure ad-ben](../fundamentals/active-directory-access-create-new-tenant.md)című témakört. Vagy használja a Azure Portal [könyvtár létrehozási élményét](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) . 

Az új bérlő létrehozásához a következő információkat kell megadnia:

- **Szervezet neve**
- **Kezdeti tartomány** – ez a tartomány a *. onmicrosoft.com része. A tartományt később is testreszabhatja.
- **Ország vagy régió**

> [!NOTE]
> A bérlő elnevezése esetén alfanumerikus karaktereket használjon. A speciális karakterek nem engedélyezettek. A név nem lehet hosszabb 256 karakternél.

## <a name="social-and-local-accounts"></a>Közösségi és helyi fiókok

A közösségi és helyi fiókokat bejelentkező alkalmazások létrehozásának megkezdéséhez hozzon létre egy Azure AD B2C bérlőt. Első lépésként tekintse meg [Azure ad B2C bérlő létrehozása](../../active-directory-b2c/tutorial-create-tenant.md)című témakört.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egy alkalmazás regisztrálása](quickstart-register-app.md) a Microsoft Identity platformmal való integrációhoz.
