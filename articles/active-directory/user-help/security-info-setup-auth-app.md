---
title: Az Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként – Azure AD
description: A biztonsági adatok (előzetes verzió) lapon beállíthatja, hogy az Ön identitását a Microsoft Authenticator alkalmazással ellenőrizze ellenőrzési módszerként.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c947bee0b702797a86d1e038f74c6c10e2b23eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197478"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként

A kétfaktoros ellenőrzési és jelszó-visszaállítási módszerek hozzáadásához kövesse az alábbi lépéseket. Az első beállítás után visszatérhet a **biztonsági** információ lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókjába való bejelentkezés után azonnal beállítja ezt a beállítását, tekintse meg a [biztonsági adatok beállítása a bejelentkezési oldalról](security-info-setup-signin.md) című cikk részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Ha nem látja a hitelesítő alkalmazás lehetőségét, lehetséges, hogy a szervezet nem engedélyezi, hogy ezt a lehetőséget használja az ellenőrzéshez. Ebben az esetben egy másik módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="security-vs-password-reset-verification"></a>Biztonság vs jelszó-visszaállítás ellenőrzése

A biztonsági információ módszereit a kéttényezős biztonsági ellenőrzéshez és a jelszó-visszaállításhoz is használja a rendszer. Azonban nem minden metódus használható egyszerre mindkettőhöz.

| Metódus | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonbeszélgetések | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>A Microsoft Authenticator alkalmazás beállítása a biztonsági adatok lapról

A szervezet beállításaitól függően előfordulhat, hogy egy hitelesítési alkalmazást is használhat a biztonsági adatok egyik módszere. Nem kell a Microsoft Authenticator alkalmazást használnia, és a beállítás során másik alkalmazást is választhat. Ez a cikk azonban az Microsoft Authenticator alkalmazást használja.

> [!IMPORTANT]
> Ha a Microsoft Authenticator alkalmazást öt különböző eszközön állította be, vagy ha öt hardveres tokent használt, akkor nem fog tudni hatodikt beállítani, és a következő hibaüzenet jelenhet meg:
> 
> **Nem állítható be Microsoft Authenticator, mert már rendelkezik öt hitelesítő alkalmazással vagy hardver jogkivonatokkal. Kérje meg a rendszergazdát, hogy törölje az egyik hitelesítő alkalmazást vagy hardver jogkivonatot.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás beállítása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. A bal oldali menüben vagy a **biztonsági adatok** ablaktáblán található hivatkozás használatával válassza a **biztonsági adatok** elemet. Ha már regisztrált, a rendszer a kétfaktoros ellenőrzésre kéri. Ezután válassza a **Hozzáadás metódus** elemet a **biztonsági adatok** ablaktáblán.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza a **hitelesítő alkalmazás** lehetőséget a legördülő listából, majd kattintson a **Hozzáadás** gombra.

    ![A Method Box hozzáadása a hitelesítő alkalmazás kiválasztásával](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Az **alkalmazás beszerzése** lapon válassza a **Letöltés most** lehetőséget, hogy letöltse és telepítse a Microsoft Authenticator alkalmazást a mobileszközön, majd válassza a **tovább** lehetőséget.

    További információ az alkalmazás letöltéséről és telepítéséről: [a Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md).

    ![Első lépésként az alkalmazás oldalának beszerzése](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Ha a Microsoft Authenticator alkalmazástól eltérő hitelesítő alkalmazást szeretne használni, válassza a **másik hitelesítő alkalmazás használata** hivatkozást.
   >
   > Ha a szervezete lehetővé teszi, hogy egy másik módszert válasszon a hitelesítő alkalmazás mellett, akkor válassza a **másik módszer csatolása** lehetőséget.

5. Maradjon a **fiók beállítása** oldalon, miközben beállítja a Microsoft Authenticator alkalmazást a mobileszközön.

    ![A hitelesítő alkalmazás oldalának beállítása](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be az értesítések engedélyezését (ha a rendszer kéri), válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók** lehetőséget.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

7. Térjen vissza a **fiók beállítása** lapra a számítógépen, majd válassza a **tovább** lehetőséget.

    Megjelenik a **QR-kód vizsgálata** lap.

    ![A QR-kód vizsgálata a hitelesítő alkalmazás használatával](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. A 6. lépésben a munkahelyi vagy iskolai fiók létrehozása után ellenőrizze a megadott kódot a mobil eszközön megjelenő Microsoft Authenticator-alkalmazás QR-kódjának olvasójában.

    A hitelesítő alkalmazásnak sikeresen hozzá kell adnia a munkahelyi vagy iskolai fiókját anélkül, hogy további adatokat kellene megadnia. Ha azonban a QR-kód olvasója nem tudja beolvasni a kódot, akkor a **nem tudja beolvasni a QR-kód hivatkozást** , és manuálisan adja meg a kódot és az URL-címet a Microsoft Authenticator alkalmazásban. A kódok manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

9. Kattintson a **Tovább gombra** a **QR-kód vizsgálata** lapon a számítógépen.

    A rendszer értesítést küld a mobileszközön lévő Microsoft Authenticator alkalmazásnak, hogy tesztelje a fiókját.

    ![A fiók tesztelése a hitelesítő alkalmazással](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, majd kattintson a **tovább** gombra.

     ![Sikeres értesítés, az alkalmazás és a fiók csatlakoztatása](media/security-info/securityinfo-myprofile-successauthapp.png)

     A biztonsági adatokat a rendszer úgy frissíti, hogy a Microsoft Authenticator alkalmazás alapértelmezés szerint a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során ellenőrizze az identitást.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>A hitelesítő alkalmazás törlése a biztonsági adatok módszereiből

Ha már nem szeretné használni a hitelesítő alkalmazást biztonsági információs módszerként, akkor a **biztonsági adatok** lapról távolíthatja el. Ez minden hitelesítő alkalmazás esetében működik, nem csak a Microsoft Authenticator alkalmazás. Az alkalmazás törlése után be kell lépnie a mobileszközön a hitelesítő alkalmazásba, és törölnie kell a fiókot.

>[!Important]
>Ha tévedésből törli a hitelesítő alkalmazást, a visszavonás nem vonható vissza. A hitelesítő alkalmazás [beállítása](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) című szakaszban ismertetett lépéseket követve újra hozzá kell adnia a hitelesítő alkalmazást.

### <a name="to-delete-the-authenticator-app"></a>A hitelesítő alkalmazás törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást a hitelesítő alkalmazás mellett.

    ![A hitelesítő alkalmazás biztonsági adatokból való törlésére szolgáló hivatkozás](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. A hitelesítő alkalmazás törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. A hitelesítő alkalmazás törlése után a rendszer eltávolítja a biztonsági adatokat, és eltűnik a **biztonsági adatok** lapról. Ha a hitelesítő alkalmazás az alapértelmezett módszer, akkor az alapértelmezett érték egy másik elérhető metódusra változik.

3. Nyissa meg a hitelesítő alkalmazást a mobileszközön, válassza a **fiókok szerkesztése** lehetőséget, majd törölje munkahelyi vagy iskolai fiókját a hitelesítő alkalmazásból.

    A fiókja teljesen el lett távolítva a hitelesítő alkalmazásból a kétfaktoros ellenőrzés és a jelszó-visszaállítási kérelmek esetében.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a hitelesítő alkalmazás az alapértelmezett módszer legyen, amikor a kétfaktoros ellenőrzés vagy a jelszó-visszaállítási kérelmek használatával bejelentkezik a munkahelyi vagy iskolai fiókjába, a biztonsági **adatok** lapról állíthatja be.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **biztonsági adatok** lapon válassza az **alapértelmezett bejelentkezési módszer** adatainak melletti **módosítás** hivatkozást.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Válassza az **Microsoft Authenticator – értesítés** lehetőséget az elérhető módszerek legördülő listájából. Ha nem használja a Microsoft Authenticator alkalmazást, válassza a **hitelesítő alkalmazás vagy a hardver token** lehetőséget.

    ![Válassza ki a metódust az alapértelmezett bejelentkezéshez](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Válassza a **Confirm (megerősítés**) lehetőséget.

    A Microsoft Authenticator alkalmazás bejelentkezési módosításaihoz használt alapértelmezett módszer.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.

    >[!Note]
    >Ha a lehetőségek némelyike hiányzik, valószínűleg azért, mert a szervezet nem engedélyezi ezeket a metódusokat. Ebben az esetben ki kell választania egy elérhető módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="next-steps"></a>Következő lépések

- Jelentkezzen be a Microsoft Authenticator alkalmazással, a [Bejelentkezés kétlépéses ellenőrzés vagy biztonsági információ használatával](security-info-setup-signin.md) című szakaszának lépéseit követve.

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com/) , vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
