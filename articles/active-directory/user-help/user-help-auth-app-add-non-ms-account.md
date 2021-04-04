---
title: Nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz – Azure AD
description: Vegyen fel nem Microsoft-fiókokat, például a Google vagy a Facebook alkalmazást a Microsoft Authenticator alkalmazásba, hogy ellenőrizze az identitását a kétfaktoros ellenőrzés használatakor.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359200"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz

Ha nem Microsoft-fiókokkal rendelkezik, például a Google, a Facebook vagy a GitHub esetében, a kétfaktoros ellenőrzéshez hozzáadhatja azokat a Microsoft Authenticator alkalmazáshoz. A Microsoft Authenticator alkalmazás minden olyan alkalmazással működik, amely kétfaktoros ellenőrzést használ, és minden olyan fiókot, amely támogatja az időalapú egyszeri jelszavas (TOTP) szabványokat.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-personal-accounts"></a>Személyes fiókok hozzáadása

Az összes személyes fiók esetében általában a következőket kell tennie:

1. Jelentkezzen be a fiókjába, majd kapcsolja be a kétfaktoros ellenőrzést az eszköz vagy a számítógép használatával.

2. Vegye fel a fiókot a Microsoft Authenticator alkalmazásba. Előfordulhat, hogy a folyamat részeként a rendszer megkéri a QR-kód bevizsgálatát.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

Itt biztosítjuk a Facebook-, Google-, GitHub-és Amazon-fiókokra vonatkozó folyamatot, de a folyamat ugyanaz, mint az egyéb alkalmazások, például a Instagram és az Adobe.

## <a name="add-your-google-account"></a>Google-fiók hozzáadása

A kétfaktoros ellenőrzés bekapcsolásával adja hozzá Google-fiókját, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépén nyissa meg a következőt: https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome , válassza az első **lépések** lehetőséget, majd ellenőrizze az identitását.

2. A személyes Google-fiókra vonatkozó kétlépéses ellenőrzés bekapcsolásához kövesse az oldalon található lépéseket.

### <a name="add-your-google-account-to-the-app"></a>Google-fiók hozzáadása az alkalmazáshoz

1. A számítógépen a Google-fiók biztonsága lapon ( https://myaccount.google.com/security) lépjen a **további második lépések hozzáadása** elemre, és ellenőrizze, hogy az Ön részéről van-e szó, majd válassza a **beállítás** a **hitelesítő alkalmazásban** szakaszt.

2. A **hitelesítő alkalmazás kódjának beolvasása** lapon válassza az **Android** vagy az **iPhone** lehetőséget a telefon típusa alapján, majd kattintson a **tovább** gombra.

    Ön kap egy QR-kódot, amellyel automatikusan társíthatja a fiókját a Microsoft Authenticator alkalmazással. Ne zárjuk be ezt az ablakot.

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a jobb felső sarokban található **Testreszabás és vezérlés** ikonban a **fiók hozzáadása** lehetőséget, majd válassza az **egyéb fiók (Google, Facebook stb.)** lehetőséget.

4. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a számítógépen a **hitelesítő beállítása** lapon.

    >[!Note]
    >Ha a kamera nem működik megfelelően, a QR-kódot és az URL-címet manuálisan is megadhatja.

5. Tekintse át az eszközön a Microsoft Authenticator alkalmazás **fiókok** lapját, és győződjön meg arról, hogy a fiók adatai megfelelőek, és hogy van-e hat számjegyű ellenőrző kód.

    A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

6. Válassza a **tovább** lehetőséget a **hitelesítő beállítása** lapon a számítógépen, írja be a Google-fiókjához az alkalmazásban megadott hat számjegyű ellenőrző kódot, majd válassza az **ellenőrzés** lehetőséget.

7. A fiók ellenőrzése **megtörtént** , és a kész gombra kattintva zárhatja be a **hitelesítő beállítása** lapot.

    >[!NOTE]
    >További információ a kétfaktoros ellenőrzésről és a Google-fiókról: [a kétlépéses ellenőrzés bekapcsolása](https://support.google.com/accounts/answer/185839) és [További tudnivalók a kétlépéses ellenőrzésről](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Facebook-fiók hozzáadása

Adja hozzá a Facebook-fiókját a kéttényezős ellenőrzés bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépén nyissa meg a Facebookot, válassza a jobb felső sarokban lévő legördülő menüt, majd lépjen a **Beállítások**  >  **Biztonság és bejelentkezés** elemre.

    Megjelenik a **Biztonság és bejelentkezés** lap.

2. Ugorjon a kétfaktoros hitelesítés **használata** lehetőségre a **kétfaktoros hitelesítés** szakaszban, majd válassza a **Szerkesztés** lehetőséget.

    Megjelenik a **kétfaktoros hitelesítés** lap.

3. Válassza **a bekapcsolás** lehetőséget.

### <a name="add-your-facebook-account-to-the-app"></a>Facebook-fiók hozzáadása az alkalmazáshoz

1. A számítógép Facebook lapján lépjen a **biztonsági mentés hozzáadása** szakaszra, majd válassza a **telepítő** elemet a **hitelesítési alkalmazás** területen.

    Ön kap egy QR-kódot, amellyel automatikusan társíthatja a fiókját a Microsoft Authenticator alkalmazással. Ne zárjuk be ezt az ablakot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a jobb felső sarokban található **Testreszabás és vezérlés** ikonban a **fiók hozzáadása** lehetőséget, majd válassza az **egyéb fiók (Google, Facebook stb.)** lehetőséget.

3. Az eszköz kamerája segítségével beszkennelheti a QR-kódot a számítógép **Kéttényezős hitelesítés** lapján.

    >[!Note]
    >Ha a kamera nem működik megfelelően, a QR-kódot és az URL-címet manuálisan is megadhatja.

4. Tekintse át az eszközön a Microsoft Authenticator alkalmazás **fiókok** lapját, és győződjön meg arról, hogy a fiók adatai megfelelőek, és hogy van-e hat számjegyű ellenőrző kód.

    A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

5. Válassza a **tovább** lehetőséget a számítógép **Kéttényezős hitelesítés** lapján, majd írja be a Facebook-fiókjához az alkalmazásban megadott hat számjegyű ellenőrző kódot.

    A fiók ellenőrzése megtörtént, és most már használhatja az alkalmazást a fiók ellenőrzéséhez.

    >[!NOTE]
    >A kétfaktoros ellenőrzéssel és a Facebook-fiókkal kapcsolatos további információkért lásd: [Mi az a kétfaktoros hitelesítés és hogyan működik?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>GitHub-fiók hozzáadása

Adja hozzá a GitHub-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépén nyissa meg a GitHub alkalmazást, válassza ki a kívánt képet a jobb felső sarokban, majd válassza a **Beállítások** lehetőséget.

    Megjelenik a **kétfaktoros hitelesítés** lap.

2. A **személyes beállítások** oldalsávon válassza a **Biztonság** lehetőséget, majd válassza a kétfaktoros hitelesítés **engedélyezése** lehetőséget a **kétfaktoros hitelesítési** területen.

### <a name="add-your-github-account-to-the-app"></a>Adja hozzá a GitHub-fiókját az alkalmazáshoz

1. A számítógép **kétfaktoros hitelesítés** lapján válassza a **beállítás egy alkalmazás használatával** lehetőséget.

2. Mentse a helyreállítási kódokat, hogy visszakapja a fiókját, ha elveszti a hozzáférését, majd válassza a **tovább** lehetőséget.

    A kódokat mentheti úgy, hogy letölti őket az eszközre, a nyomtatott másolatok kinyomtatásával vagy a Password Manager-eszközbe való másolásával.

3. A **kétfaktoros hitelesítés** lapon válassza a **beállítás egy alkalmazás használatával** lehetőséget.

    Az oldal úgy változik, hogy megmutassa a QR-kódot. Ne zárjuk be ezt a lapot.

4. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a jobb felső sarokban található **Testreszabás és vezérlés** ikonban, válassza az **egyéb fiók (Google, Facebook stb.)** lehetőséget, majd a lap tetején lévő szövegből válassza a **szöveg megadása** lehetőséget.

    A Microsoft Authenticator alkalmazás nem tudja beolvasni a QR-kódot, ezért manuálisan kell megadnia a kódot.

5. Adja meg a **fiók nevét** (például GitHub), és írja be a **titkos kulcsot** a 4. lépésből, majd válassza a **Befejezés** gombot.

6. A számítógép **kétfaktoros hitelesítő** lapján írja be a GitHub-fiókhoz tartozó alkalmazásban megadott hat számjegyű ellenőrző kódot, majd válassza az **Engedélyezés** lehetőséget.

    Az alkalmazás **fiókok** lapján megjelenik a fiók neve és egy hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

    >[!NOTE]
    >A kétfaktoros ellenőrzéssel és a GitHub-fiókkal kapcsolatos további információkért lásd [a kétfaktoros hitelesítésről szóló](https://help.github.com/articles/about-two-factor-authentication/)témakört.

## <a name="add-your-amazon-account"></a>Amazon-fiók hozzáadása

A kétfaktoros ellenőrzés bekapcsolásával adja hozzá az Amazon-fiókját, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg az Amazon elemet, válassza ki a **fiók & listája** legördülő menüt, majd válassza ki **a fiókját**.

2. Válassza a **bejelentkezés & biztonság** lehetőséget, jelentkezzen be az Amazon-fiókjába, majd válassza a **Szerkesztés** lehetőséget a **speciális biztonsági beállítások** területen.

    Megjelenik a **speciális biztonsági beállítások** lap.

3. Válassza az első **lépések** lehetőséget.

4. Válassza a **hitelesítő alkalmazás** lehetőséget a **válassza ki, hogyan szeretné fogadni a kódokat** lapon.

    Az oldal úgy változik, hogy megmutassa a QR-kódot. Ne zárjuk be ezt a lapot.

5. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a jobb felső sarokban található **Testreszabás és vezérlés** ikonban a **fiók hozzáadása** lehetőséget, majd válassza az **egyéb fiók (Google, Facebook stb.)** lehetőséget.

6. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a számítógépén a **kódok fogadásának kiválasztása** lapon.

    >[!Note]
    >Ha a kamera nem működik megfelelően, a QR-kódot és az URL-címet manuálisan is megadhatja.

7. Tekintse át az eszközön a Microsoft Authenticator alkalmazás **fiókok** lapját, és győződjön meg arról, hogy a fiók adatai megfelelőek, és hogy van-e hat számjegyű ellenőrző kód.

    A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

8. A **válassza ki a kódok fogadása** lapot a számítógépen írja be az Amazon-fiókjához az alkalmazásban megadott hat számjegyű ellenőrző kódot, majd válassza a **kód ellenőrzése és folytatás** lehetőséget.

9. Fejezze be a regisztrációs folyamat hátralévő részét, beleértve a biztonsági mentési ellenőrzési módszer hozzáadását, például egy szöveges üzenetet, majd válassza a **kód küldése** lehetőséget.

10. A számítógép **biztonsági mentése ellenőrzési módszer hozzáadása** lapján írja be az Amazon-fiókjához tartozó biztonsági mentési ellenőrzési módszer által megadott hat számjegyű ellenőrző kódot, majd válassza a **kód ellenőrzése és folytatás** lehetőséget.

11. A **majdnem kész** lapon döntse el, hogy megbízható eszközt kíván-e készíteni a számítógépről, majd válassza a **beolvasás lehetőséget. Two-Step ellenőrzés bekapcsolása**.

    Megjelenik a **speciális biztonsági beállítások** lap, amely a frissített kétfaktoros ellenőrzés részleteit jeleníti meg.

    >[!NOTE]
    >A kétfaktoros ellenőrzéssel és az Amazon-fiókkal kapcsolatos további információkért tekintse meg a [Two-Step ellenőrzése](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) és a [Bejelentkezés Two-Step ellenőrzéssel](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)című témakört.

## <a name="next-steps"></a>Következő lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
