---
title: Személyes Microsoft-fiók hozzáadása a Microsoft Authenticator alkalmazáshoz – Azure AD
description: Vegyen fel személyes Microsoft-fiókokat, például a Outlook.com vagy az Xbox LIVE-t a Microsoft Authenticator alkalmazásba, hogy ellenőrizze az identitását a kétfaktoros ellenőrzés használata során.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83741632"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Személyes Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz

Vegye fel a személyes Microsoft-fiókjait, például a Outlook.com és az Xbox LIVE-fiókokat a standard kétfaktoros ellenőrzési folyamat és a jelszó nélküli telefonos bejelentkezés Microsoft Authenticator alkalmazásba.

- **Standard kétfaktoros ellenőrzési módszer.** Írja be a felhasználónevét és jelszavát arra az eszközre, amelyhez bejelentkezik, majd válassza ki, hogy a Microsoft Authenticator alkalmazás küldjön-e értesítést, vagy ha szeretné, hogy a társított ellenőrző kódot a Microsoft Authenticator alkalmazás **fiókok** képernyőjéről másolja.

- **Jelszó nélküli bejelentkezési módszer.** Írja be a felhasználónevét arra az eszközre, amelyhez be szeretné jelentkezni a személyes Microsoft-fiók használatával, majd a mobileszköz használatával ellenőrizze, hogy az ujjlenyomatát, arcát vagy PIN-kódját használja-e. Ennél a módszernél nem kell megadnia a jelszavát.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

Személyes Microsoft-fiók a kétfaktoros ellenőrzés bekapcsolásával, majd a fióknak az alkalmazáshoz való hozzáadásával adhat hozzá. A kétfaktoros ellenőrzés bekapcsolásával csak jelszó nélküli telefonos bejelentkezést használhat a fiókjához, de javasoljuk, hogy kapcsolja be a kétfaktoros ellenőrzést a fiók biztonsága érdekében.

## <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen lépjen a [Biztonság alapjai](https://account.microsoft.com/security) lapra, és jelentkezzen be a személyes Microsoft-fiók használatával. Például: alain@outlook.com.

2. A **Biztonság alapjai** lap alján válassza a **további biztonsági beállítások** hivatkozást.

    ![Biztonsági alapismeretek oldal a "további biztonsági beállítások" hivatkozás kiemelve](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Lépjen a **kétlépéses ellenőrzés** szakaszra, és válassza **a funkció bekapcsolását**. Kikapcsolhatja azt is, ha már nem szeretné használni a személyes fiókjával.

## <a name="add-your-microsoft-account-to-the-app"></a>Microsoft-fiók hozzáadása az alkalmazáshoz

1. Nyissa meg a Microsoft Authenticator alkalmazást a mobileszközön.

1. Az Androidon válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikon jobb felső sarkában.

    ![Android-fiókok kiválasztási oldalai](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    IOS rendszeren válassza a plusz ikont a jobb felső sarokban.

    ![a fiók kiválasztási élményének iOS-verziója](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. A **fiók hozzáadása** lapon válassza a **személyes fiók** lehetőséget.

1. A fiók hozzáadásához válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget. A QR-kód használható, ha elérhető, de bármikor felveheti a fiókját, ha bejelentkezik a felhasználónevével és jelszavával.

    ![Válasszon ki egy Microsoft-fiók vagy olvassa be a QR-kódot, ha elérhető](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Jelentkezzen be a személyes fiókjába a megfelelő e-mail-cím (például) használatával, alain@outlook.com majd válassza a **tovább** lehetőséget.

    >[!Note]
    >Ha nincs személyes Microsoft-fiók, [létrehozhat egyet](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Adja meg a jelszót, majd válassza a **Bejelentkezés** lehetőséget. A rendszer hozzáadja a személyes fiókját a Microsoft Authenticator alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Ha nem sikerül beszereznie a személyes Microsoft-fiók ellenőrzési kódját, tekintse meg az **ellenőrző kód problémáinak elhárítása** című szakaszt a [Microsoft-fiók biztonsági adatok & ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikkben.

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
