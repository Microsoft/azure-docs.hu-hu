---
title: Bejelentkezés munkahelyi vagy iskolai fiókkal való hitelesítéssel – Azure AD
description: Ismerje meg, hogyan jelentkezik be munkahelyi vagy iskolai fiókjába a különböző kétfaktoros ellenőrzési módszerekkel.
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
ms.openlocfilehash: b373288eb5cd47f99bdbb25f961e1330a708d7d1
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739187"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Jelentkezzen be munkahelyi vagy iskolai fiókjába a kétfaktoros ellenőrzési módszerrel

> [!NOTE]
> A cikk célja, hogy végigvezesszen egy tipikus bejelentkezési élményen. A bejelentkezéssel vagy a problémák elhárításával kapcsolatos segítségért tekintse meg az [Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md)problémáit bemutató témakört.

## <a name="what-will-your-sign-in-experience-be"></a>Milyen lesz a bejelentkezési élmény?
A bejelentkezési élmény attól függ, hogy mit használ második tényezőként: telefonhívást, hitelesítési alkalmazást vagy szöveges üzenetet. Válassza ki azt a lehetőséget, amely a legjobban leírja, mit csinál:

| Hogyan jelentkezik be? |
| --- |
| [Telefonhívással a mobiltelefonomra vagy az irodai telefonomra](#signing-in-with-a-phone-call) |
| [Sms-sel a mobiltelefonomra](#signing-in-with-a-text-message)
| [Az alkalmazás értesítései Microsoft Authenticator használatával](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| A Microsoft Authenticator alkalmazásból származó ellenőrzőkódokkal |
| [Alternatív módszerrel, mert jelenleg nem tudom használni az előnyben részesített módszert](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Bejelentkezés telefonhívással
Az alábbi információk ismertetik a kétlépéses ellenőrzési élményt a mobiltelefonjára vagy irodai telefonjára való hívással.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 felhasználónevével és jelszavával.  
2. A Microsoft felhívja.  
3. Válaszoljon a telefonra, és nyomja le a # billentyűt.  

## <a name="signing-in-with-a-text-message"></a>Bejelentkezés szöveges üzenettel
Az alábbi információk a mobiltelefonra küldött szöveges üzenetekkel kapcsolatos kétlépéses ellenőrzési élményt ismertetik:

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 felhasználónevével és jelszavával.
2. A Microsoft egy számkódot tartalmazó szöveges üzenetet küld Önnek.
3. Írja be a kódot a bejelentkezési oldalon megadott mezőbe.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator alkalmazással
Az alábbi információk azt ismertetik, hogyan felhasználásával Microsoft Authenticator kétlépéses ellenőrzéshez a Microsoft Authenticator alkalmazás. Az alkalmazás kétféleképpen használható. Leküldéses értesítéseket fogadhat az eszközén, vagy megnyithatja az alkalmazást az ellenőrző kód leküldéséhez.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator értesítéssel
1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 felhasználónevével és jelszavával.
2. A Microsoft értesítést küld Microsoft Authenticator eszközén található alkalmazásnak.

   ![A Microsoft értesítést küld](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Nyissa meg az értesítést a telefonján, és válassza a **Verify (Ellenőrzés)** kulcsot. Ha a vállalatának PIN-kódra van szüksége, itt adhatja meg.
4. Most már be kell, hogy legyen jelentkezve.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Bejelentkezés ellenőrző kóddal a Microsoft Authenticator alkalmazással

Ha a Microsoft Authenticator alkalmazással kap ellenőrzőkódokat, akkor az alkalmazás megnyitásakor egy számot fog látni a fiók neve alatt. Ez a szám 30 másodpercenként változik, hogy ne használja kétszer ugyanazt a számot. Amikor a rendszer ellenőrző kódot kér, nyissa meg az alkalmazást, és használja az aktuálisan megjelenített számot.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 felhasználónevével és jelszavával.
2. A Microsoft ellenőrző kódot kér.

   ![Adja meg az ellenőrző kódot](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Nyissa meg Microsoft Authenticator alkalmazást a telefonján, és írja be a kódot abban a mezőben, amelybe bejelentkezik.

## <a name="signing-in-with-an-alternate-method"></a>Bejelentkezés másik módszerrel
Előfordulhat, hogy nem a beállított telefon vagy eszköz van beállítva előnyben részesített ellenőrzési módszerként. Ezért javasoljuk, hogy állítson be biztonsági mentési módszereket a fiókjához. A következő szakasz azt mutatja be, hogyan lehet alternatív módszerrel bejelentkezni, ha az elsődleges módszer nem érhető el.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például Microsoft 365 felhasználónevével és jelszavával.
2. Válassza **a Másik ellenőrzési lehetőség használata lehetőséget.** A beállítások alapján különböző ellenőrzési lehetőségek állnak rendelkezésre.
3. Válasszon egy másik módszert, és jelentkezzen be.

   ![Alternatív módszer használata](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Következő lépések
- Ha problémája merül fel a kétlépéses ellenőrzéssel való bejelentkezés során, további információt a Probléma az [Azure AD Multi-Factor Authentication szolgáltatásbancímű](multi-factor-authentication-end-user-troubleshoot.md)oldalon található.

- Ismerje meg, hogyan [kezelheti a kétlépéses ellenőrzési beállításokat.](multi-factor-authentication-end-user-manage-settings.md)

- Ismerje meg, hogyan használhatja a Microsoft Authenticator [alkalmazást,](user-help-auth-app-download-install.md) hogy szöveges és telefonhívások helyett értesítéseket használva bejelentkezhet.
