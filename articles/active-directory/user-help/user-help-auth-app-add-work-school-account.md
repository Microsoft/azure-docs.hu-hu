---
title: Munkahelyi vagy iskolai fiók felvétele a Microsoft Authenticator alkalmazásba – Azure AD
description: Vegye fel a munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazásba, és ellenőrizze az identitását a kétfaktoros ellenőrzés használata során.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359112"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz

Ha a szervezet kétfaktoros ellenőrzést használ, beállíthatja a munkahelyi vagy iskolai fiókját, hogy az Microsoft Authenticator alkalmazást az egyik ellenőrzési módszerként használja.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

A következő módokon adhatja hozzá munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazáshoz:

- Jelentkezzen be munkahelyi vagy iskolai fiókjával hitelesítő adatok (előzetes verzió)
- QR-kód bevizsgálása

### <a name="sign-in-with-your-credentials"></a>Bejelentkezés hitelesítő adatokkal

>[!Note]
>Ezt a szolgáltatást csak azok a felhasználók tudják használni, akiknek a rendszergazdái engedélyezték a telefonos bejelentkezést a hitelesítő alkalmazással.

Fiók hozzáadásához jelentkezzen be a munkahelyi vagy iskolai fiókjába a hitelesítő adataival:

1. Nyissa meg a Microsoft Authenticator alkalmazást, és válassza ki a **+** gombot, és koppintson a **munkahelyi vagy iskolai fiók hozzáadása** lehetőségre. Válassza a **Bejelentkezés** lehetőséget.

1. Adja meg a munkahelyi vagy iskolai fiókjának hitelesítő adatait. Ha van egy ideiglenes hozzáférési pass (KOPPINTson), használhatja a bejelentkezéshez. Ezen a ponton lehetséges, hogy a következő feltételek egyikével blokkolva lesz:

   - Ha nem rendelkezik elegendő hitelesítési módszerekkel a fiókjában egy erős hitelesítési jogkivonat beszerzéséhez, akkor nem folytathatja a fiók hozzáadását.

   - Ha az üzenet jelenik meg `You might be signing in from a location that is restricted by your admin` , a rendszer letiltja a rendszergazdát, és feloldja a [biztonsági adatok](https://mysignins.microsoft.com/security-info)blokkolását.

   - Ha a rendszergazda a hitelesítő alkalmazással nem tiltja le a telefonos bejelentkezést, akkor az eszköz regisztrálásával megkezdheti a jelszó nélküli telefonos bejelentkezés és az Azure Multi-Factor Authentication (MFA) beállítását.

1. Ezen a ponton a szervezet által megadott QR-kód bevizsgálása után egy helyszíni multi-Factor Authentication-fiók állítható be az alkalmazásban. Ezt csak akkor kell megtennie, ha a szervezet helyszíni MFA-kiszolgálót használ.

1. Az eszközön koppintson a fiókra, és ellenőrizze, hogy a fiókja megfelelő-e, és hogy van-e hozzárendelve hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

## <a name="sign-in-with-a-qr-code"></a>Bejelentkezés QR-kóddal

Ha QR-kód beolvasásával szeretne fiókot hozzáadni, tegye a következőket:

1. A számítógépen lépjen a **további biztonsági ellenőrzés** lapra.

   >[!Note]
   >Ha nem látja a **további biztonsági ellenőrzés** oldalt, lehetséges, hogy a rendszergazda bekapcsolta a biztonsági adatok (előzetes verzió) szolgáltatást. Ha ez a helyzet, kövesse a [biztonsági adatok beállítása a hitelesítő alkalmazás használatára](security-info-setup-auth-app.md) című szakasz utasításait. Ha ez nem így van, segítségért forduljon a szervezet ügyfélszolgálatához. A biztonsági információkkal kapcsolatos további információkért lásd: [biztonsági adatok beállítása bejelentkezési kérésből](security-info-setup-signin.md).

1. Jelölje be a hitelesítő alkalmazás melletti jelölőnégyzetet, majd válassza a **Konfigurálás** lehetőséget. Megjelenik a **Mobile App konfigurálása** oldal.

   ![QR-kódot biztosító képernyő](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Nyissa meg a Microsoft Authenticator alkalmazást, és válassza a plusz ikont ![ az iOS-vagy Android-eszközökön, majd válassza a ](media/user-help-auth-app-add-work-school-account/plus-icon.png) **fiók hozzáadása** lehetőséget, majd válassza a **munkahelyi vagy iskolai fiók lehetőséget,** majd a **QR-kód** beírása elemet.
   Ha nincs beállítva fiók a hitelesítő alkalmazásban, akkor egy nagy kék gomb jelenik meg, amely a **fiók hozzáadása** lehetőséget fogja látni.

Ha a rendszer nem kéri a QR-kód beolvasni kívánt kamerájának használatát, a telefon beállításaiban győződjön meg arról, hogy a hitelesítő alkalmazás hozzáfér a telefon kamerájával.

## <a name="next-steps"></a>Következő lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
