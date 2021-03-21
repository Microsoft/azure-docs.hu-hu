---
title: Bejelentkezés munkahelyi vagy iskolai fiókkal történő hitelesítéssel – Azure AD
description: Ismerje meg, hogyan jelentkezhet be a munkahelyi vagy iskolai fiókjába a különböző kétfaktoros ellenőrzési módszerek használatával.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94834166"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Jelentkezzen be a munkahelyi vagy iskolai fiókjába a kétfaktoros ellenőrzési módszer használatával

> [!NOTE]
> Ennek a cikknek a célja egy tipikus bejelentkezési felület bejárása. Ha segítségre van szüksége a bejelentkezéshez vagy a problémák elhárításához, tekintse meg az [Azure AD multi-Factor Authenticationával](multi-factor-authentication-end-user-troubleshoot.md)kapcsolatos problémákat.

## <a name="what-will-your-sign-in-experience-be"></a>Mi lesz a bejelentkezési élmény?
A bejelentkezési élmény attól függően különbözik, hogy mit választ a második tényezőként való használatra: telefonhívás, hitelesítési alkalmazás vagy szövegek. Válassza ki azt a lehetőséget, amely a legjobban leírja, hogy mit csinál:

| Hogyan jelentkezhet be? |
| --- |
| [A mobil-vagy irodai telefon hívásával](#signing-in-with-a-phone-call) |
| [Egy szöveggel a mobil telefonom](#signing-in-with-a-text-message)
| [A Microsoft Authenticator alkalmazás értesítéseivel](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Ellenőrző kódokkal a Microsoft Authenticator alkalmazásból |
| [Alternatív módszer esetén, mert most nem tudom használni az előnyben részesített módszert](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Bejelentkezés telefonos hívással
A következő információk ismertetik a kétlépéses ellenőrzési élményt a mobil vagy irodai telefon hívásával.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 a felhasználónevével és jelszavával.  
2. A Microsoft meghívja Önt.  
3. Válaszolja meg a telefont, és nyomja meg a # billentyűt.  

## <a name="signing-in-with-a-text-message"></a>Bejelentkezés szöveges üzenettel
A következő információk ismertetik a mobiltelefonra küldött szöveges üzenetekkel kapcsolatos kétlépéses ellenőrzési élményt:

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 a felhasználónevével és jelszavával.
2. A Microsoft szöveges üzenetet küld Önnek, amely egy számsorozatot tartalmaz.
3. Írja be a kódot a bejelentkezési oldalon megadott mezőbe.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator alkalmazással
Az alábbi információk az Microsoft Authenticator alkalmazás kétlépéses ellenőrzésekhez való használatának élményét ismertetik. Az alkalmazás két különböző módon használható. Leküldéses értesítéseket fogadhat az eszközén, vagy megnyithatja az alkalmazást egy ellenőrző kód beszerzéséhez.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Bejelentkezés értesítéssel a Microsoft Authenticator alkalmazásból
1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 a felhasználónevével és jelszavával.
2. A Microsoft értesítést küld a Microsoft Authenticator alkalmazásnak az eszközön.

   ![A Microsoft értesítést küld](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Nyissa meg az értesítést a telefonján, és válassza az **ellenőrzés** kulcsot. Ha a vállalata PIN-kódot igényel, adja meg itt.
4. Most be kell jelentkeznie.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Bejelentkezés ellenőrző kóddal a Microsoft Authenticator alkalmazással

Ha az Microsoft Authenticator alkalmazást használja az ellenőrző kódok beolvasásához, akkor az alkalmazás megnyitásakor a fiók neve alatt egy szám jelenik meg. Ez a szám 30 másodpercenként változik, hogy ne használja ugyanazt a számot kétszer. Amikor a rendszer megkérdezi az ellenőrző kódot, nyissa meg az alkalmazást, és használja az aktuálisan megjelenő számot.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 a felhasználónevével és jelszavával.
2. A Microsoft egy ellenőrző kódot kér.

   ![Adja meg az ellenőrző kódot](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást a telefonján, és írja be a kódot abban a mezőben, ahová bejelentkezik.

## <a name="signing-in-with-an-alternate-method"></a>Bejelentkezés másik módszerrel
Előfordulhat, hogy nem rendelkezik az előnyben részesített ellenőrzési módszerként beállított telefonnal vagy eszközzel. Ezért javasoljuk, hogy állítsa be a fiókja biztonsági mentési módszereit. A következő szakasz bemutatja, hogyan jelentkezhet be másik módszerrel, ha az elsődleges metódus nem érhető el.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 a felhasználónevével és jelszavával.
2. Válassza **a másik ellenőrzési lehetőség használata lehetőséget**. A beállítástól függően különböző ellenőrzési lehetőségek jelennek meg.
3. Válasszon másik módszert, és jelentkezzen be.

   ![Alternatív módszer használata](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Következő lépések
- Ha problémák merülnek fel a kétlépéses ellenőrzéssel való bejelentkezés során, további információkat kaphat az [Azure AD multi-Factor Authenticationával](multi-factor-authentication-end-user-troubleshoot.md)kapcsolatban.

- Ismerje meg, hogyan [kezelheti a kétlépéses ellenőrzési beállításokat](multi-factor-authentication-end-user-manage-settings.md).

- Megtudhatja, hogyan kezdheti el [a Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) , hogy a szövegek és telefonhívások helyett értesítéseket tudjon használni a bejelentkezéshez.
