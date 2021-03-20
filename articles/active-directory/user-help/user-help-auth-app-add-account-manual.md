---
title: Fiók manuális hozzáadása az alkalmazáshoz – Azure Active Directory | Microsoft Docs
description: Fiókok manuális hozzáadása a Microsoft Authenticator alkalmazáshoz kétfaktoros ellenőrzéshez.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 3838234f9f65fcf91f8de33e3d7196aa13f2b903
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83741718"
---
# <a name="manually-add-an-account-to-the-app"></a>Fiók manuális hozzáadása az alkalmazáshoz

Ha a kamera nem tudja rögzíteni a QR-kódot, manuálisan is felveheti a fiók adatait a Microsoft Authenticator alkalmazásba kétfaktoros ellenőrzéshez. Ez munkahelyi vagy iskolai fiókokhoz, illetve nem Microsoft-fiókokhoz is működik.

A fiókokhoz megadott kódok nem tesznek különbséget a kis-és nagybetűk között, és nincs szükség szóközökre a Microsoft Authenticator alkalmazásba való felvételkor.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

1. A számítógépen jegyezze fel a **kód** és az **URL-cím** információit a **Mobile App konfigurálása** lapon. Tartsa megnyitva ezt a lapot, így megtekintheti a kódot és az URL-címet.

    ![A QR-kódot biztosító képernyő](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók** lehetőséget.

3. Válassza ki **vagy adja meg manuálisan a kódot**.

    ![QR-kód beolvasására szolgáló képernyő](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Adja meg a **kódot** és az **URL-címet** az 1. lépésben, majd válassza a **Befejezés** gombot.

    ![Kód és URL-cím megadására szolgáló képernyő](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Az alkalmazás **fiókok** képernyőjén megjelenik a fiók neve és egy hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

## <a name="add-your-google-account"></a>Google-fiók hozzáadása

1. A számítógépen válassza a **nem lehet beolvasni** lehetőséget a **hitelesítő beállítása** lapon a QR-kóddal.

    A **Vonalkód beolvasása lap nem** jelenik meg a titkos kóddal. Tartsa nyitva ezt a lapot, hogy megtekintse a titkos kódot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** elemet a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, válassza a **másik fiók (Google, Facebook stb.)** lehetőséget, majd válassza ki **vagy írja be a kódot manuálisan**.

3. Adja meg a **fiók nevét** (például Google), és írja be a **titkos kulcsot** az 1. lépésben, majd válassza a **Befejezés** gombot.

4. A **hitelesítő beállítása** lapon adja meg a Google-fiókjához az alkalmazásban megadott hat számjegyű ellenőrző kódot, majd válassza az **ellenőrzés** lehetőséget.

    Az alkalmazás **fiókok** képernyőjén megjelenik a fiók neve és egy hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

    >[!NOTE]
    >További információ a kétfaktoros ellenőrzésről és a Google-fiókról: [a kétlépéses ellenőrzés bekapcsolása](https://support.google.com/accounts/answer/185839) és [További tudnivalók a kétlépéses ellenőrzésről](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Facebook-fiók hozzáadása

1. A **külső fél hitelesítő beállítása** lapon, amely tartalmazza a QR-kódot, valamint egy kódot, amely az alkalmazásba való belépésre van kiírva. Tartsa megnyitva ezt a lapot, hogy láthassa a kódot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** elemet a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, válassza a **másik fiók (Google, Facebook stb.)** lehetőséget, majd válassza ki **vagy írja be a kódot manuálisan**.

3. Adja meg a **fiók nevét** (például Facebook), és írja be a **titkos kulcsot** az 1. lépésben, majd válassza a **Befejezés** gombot.

4. A számítógép **Kéttényezős hitelesítő** lapján írja be a Facebook-fiókjához az alkalmazásban megadott hat számjegyű ellenőrző kódot, majd válassza az **ellenőrzés** lehetőséget.

    Az alkalmazás **fiókok** képernyőjén megjelenik a fiók neve és egy hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

    >[!NOTE]
    >A kétfaktoros ellenőrzéssel és a Facebook-fiókkal kapcsolatos további információkért lásd: [Mi az a kétfaktoros hitelesítés és hogyan működik?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Amazon-fiók hozzáadása

A kétfaktoros ellenőrzés bekapcsolásával hozzáadhatja az Amazon-fiókját, majd hozzáadhatja a fiókot az alkalmazáshoz.

1. A számítógépén válassza a **nem tudja beolvasni a vonalkódot** lehetőséget a QR-kóddal rendelkező **kódok fogadása** lapon.

    A **vonalkódos üzenet nem vizsgálható** meg a titkos kóddal. Tartsa nyitva ezt az üzenetet, hogy megtekintse a titkos kódot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** elemet a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, válassza a **másik fiók (Google, Facebook stb.)** lehetőséget, majd válassza ki **vagy írja be a kódot manuálisan**.

3. Adja meg a **fiók nevét** (például Amazon), és írja be az 1. lépésben szereplő **titkos kulcsot** , majd kattintson a **Befejezés gombra**.

4. Fejezze be a regisztrációs folyamat hátralévő részét, beleértve a biztonsági mentési ellenőrzési módszer hozzáadását, például egy szöveges üzenetet, majd válassza a **kód küldése** lehetőséget.

5. A számítógép **biztonsági mentése ellenőrzési módszer hozzáadása** lapján írja be az Amazon-fiókjához tartozó biztonsági mentési ellenőrzési módszer által megadott hat számjegyű ellenőrző kódot, majd válassza a **kód ellenőrzése és folytatás** lehetőséget.

6. A majdnem kész lapon döntse el, hogy megbízható eszközt kíván-e készíteni a számítógépről, majd válassza a **beolvasás lehetőséget. Two-Step ellenőrzés bekapcsolása**.

    Megjelenik a **speciális biztonsági beállítások** lap, amely a frissített kétfaktoros ellenőrzés részleteit jeleníti meg.

    >[!NOTE]
    >A kétfaktoros ellenőrzéssel és az Amazon-fiókkal kapcsolatos további információkért tekintse meg a [Two-Step ellenőrzése](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) és a [Bejelentkezés Two-Step ellenőrzéssel](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)című témakört.    

## <a name="next-steps"></a>Következő lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a Microsoft Authenticator alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Ha nem sikerül beszereznie a személyes Microsoft-fiók ellenőrzési kódját, tekintse meg az **ellenőrző kód problémáinak elhárítása** című szakaszt a [Microsoft-fiók biztonsági adatok & ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikkben.

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
