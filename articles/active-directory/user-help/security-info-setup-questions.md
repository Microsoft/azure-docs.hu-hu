---
title: Biztonsági kérdések beállítása ellenőrzési módszerként – Azure AD
description: A biztonsági adatok (előzetes verzió) lapon beállíthatja, hogy az Ön személyazonosságát előre definiált biztonsági kérdésekkel ellenőrizze az ellenőrzési módszer alapján.
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
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83744439"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Biztonsági kérdések beállítása ellenőrzési módszerként

A jelszó-visszaállítási módszer hozzáadásához kövesse az alábbi lépéseket. Az első beállítás után visszatérhet a **biztonsági** információ lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem jelenik meg a biztonsági kérdések lehetőség, akkor előfordulhat, hogy a szervezet nem teszi lehetővé, hogy ezt a beállítást használja a jelszó-visszaállítási módszerhez. Ebben az esetben egy másik módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="security-verification-versus-password-reset-authentication"></a>Biztonsági ellenőrzés és jelszó-visszaállítási hitelesítés

A biztonsági információ módszereit a kéttényezős biztonsági ellenőrzéshez és a jelszó-visszaállításhoz is használja a rendszer. Azonban nem minden metódus használható egyszerre mindkettőhöz.

| Metódus | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonbeszélgetések | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Biztonsági kérdések beállítása a biztonsági adatok lapról

A szervezet beállításaitól függően lehetséges, hogy néhány biztonsági kérdést választ ki és válaszol a biztonsági adatok egyik módszerei közül. A rendszergazda beállítja a kiválasztáshoz és a válaszadáshoz szükséges biztonsági kérdések számát.

Ha biztonsági kérdéseket használ, azt javasoljuk, hogy más módszerekkel együtt használja őket. A biztonsági kérdések kevésbé biztonságosak, mint a többi módszer, mert egyesek tudomásul veszik a kérdéseire adott válaszokat.

> [!Note]
> A biztonsági kérdéseket a címtárban lévő felhasználói objektumon tárolja a rendszer, és a szolgáltatás csak a regisztráció során válaszol. A rendszergazda semmilyen módon nem tudja elolvasni vagy módosítani a kérdéseit és válaszait.
>
> Ha nem jelenik meg a biztonsági kérdések lehetőség, akkor előfordulhat, hogy a szervezet nem teszi lehetővé biztonsági kérdések használatát az ellenőrzéshez. Ebben az esetben másik módszert kell választania, vagy további segítségért forduljon a rendszergazdához.
>
> A rendszergazdai fiókok nem jogosultak biztonsági kérdések használatára jelszó-visszaállítási módszerként. Ha rendszergazdai szintű fiókkal jelentkezett be, ezeket a beállításokat nem fogja látni.

### <a name="to-set-up-your-security-questions"></a>Biztonsági kérdések beállítása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a bal oldali navigációs ablaktábla **biztonsági adatok** elemét, vagy a biztonsági **információ** blokkban található hivatkozásra, majd válassza a **metódus hozzáadása** elemet a **biztonsági adatok** lapon.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza a **biztonsági kérdések** elemet a legördülő listából, majd válassza a **Hozzáadás** lehetőséget.

    ![Hozzáadás a Method Box-hoz, biztonsági kérdésekkel kiválasztva](media/security-info/securityinfo-myprofile-addquestions.png)

4. A **biztonsági kérdések** lapon válassza ki és válaszolja meg a biztonsági kérdéseit, majd válassza a **Mentés** lehetőséget.

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-securityquestions.png)

    A biztonsági adatai frissülnek, és biztonsági kérdéseivel ellenőrizheti, hogy a jelszó-visszaállítást használja-e.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Biztonsági kérdések törlése a biztonsági adatok módszereiből

Ha a biztonsági kérdéseket már nem szeretné biztonsági információs módszerként használni, távolítsa el őket a **biztonsági adatok** lapról.

>[!Important]
>Ha tévedésből törli a biztonsági kérdéseket, a visszavonás nem vonható vissza. A módszert újra fel kell vennie a jelen cikk [biztonsági kérdések beállítása](#set-up-your-security-questions-from-the-security-info-page) című szakaszának lépéseit követve.

### <a name="to-delete-your-security-questions"></a>Biztonsági kérdések törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást a **biztonsági kérdések** lehetőség mellett.

    ![Hivatkozás a telefonos módszer törlésére a biztonsági adatokból](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. A **biztonsági kérdések** törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. A biztonsági kérdések törlése után a rendszer eltávolítja a metódust a biztonsági adatokból, és eltűnik a **biztonsági adatok** lapról.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

    >[!Note]
    >Ha a lehetőségek némelyike hiányzik, valószínűleg azért, mert a szervezet nem engedélyezi ezeket a metódusokat. Ebben az esetben ki kell választania egy elérhető módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="next-steps"></a>Következő lépések

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com/) , vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
