---
title: Szöveges üzenetküldés beállítása ellenőrzési módszerként – Azure AD
description: A biztonsági adatok (előzetes verzió) lapon beállíthatja, hogy az Ön személyazonosságát SMS-ként ellenőrizze az ellenőrzési módszerként.
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
ms.openlocfilehash: c68e01e0eb7c926f47c99b16efa87d23a10b6711
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537033"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Szöveges üzenetküldés beállítása ellenőrzési módszerként

A kétfaktoros ellenőrzési és jelszó-visszaállítási módszerek hozzáadásához kövesse az alábbi lépéseket. Az első beállítás után visszatérhet a **biztonsági** információ lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókjába való bejelentkezés után azonnal beállítja ezt a beállítását, tekintse meg a [biztonsági adatok beállítása a bejelentkezési oldalról](security-info-setup-signin.md) című cikk részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem jelenik meg a telefonos lehetőség, akkor előfordulhat, hogy a szervezet nem engedélyezi, hogy ezt a lehetőséget használja az ellenőrzéshez. Ebben az esetben egy másik módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Szöveges üzenetek beállítása a biztonsági adatok lapról

A szervezet beállításaitól függően előfordulhat, hogy a szöveges üzenetküldést a biztonsági adatok egyik módszerének megfelelően használhatja. A szöveges üzenet lehetőség a telefonos lehetőség része, így minden ugyanúgy beállítható, ahogy a telefonszáma is, de ahelyett, hogy a Microsoft meghívja Önt, szöveges üzenetet fog használni.

>[!Note]
>Ha szöveges üzenet helyett telefonhívást szeretne kapni, kövesse a [biztonsági adatok beállítása a telefonhívások használatára](security-info-setup-phone-number.md) című cikk lépéseit.

### <a name="to-set-up-text-messages"></a>Szöveges üzenetek beállítása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a bal oldali navigációs ablaktábla **biztonsági adatok** elemét, vagy a biztonsági **információ** blokkban található hivatkozásra, majd válassza a **metódus hozzáadása** elemet a **biztonsági adatok** lapon.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza a **telefon** lehetőséget a legördülő listából, majd kattintson a **Hozzáadás** gombra.

    ![Hozzáadás a Method Box-hoz, a kiválasztott telefonnal](media/security-info/securityinfo-myprofile-addphonetext.png)

4. A **Phone (telefon** ) lapon írja be a mobileszköz telefonszámát, válassza ki a **kód szövegét**, majd kattintson a **tovább** gombra.

    ![A "Phone" (telefonos) lapot megjelenítő képernyőkép, amely a "szöveg nekem kód" lehetőséget választotta.](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Írja be a mobileszközön a szöveges üzenetben küldött kódot, majd válassza a **tovább** lehetőséget.

    ![Telefonszám hozzáadása és szöveges üzenetek kiválasztása](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A lap úgy változik, hogy megmutassa a sikert.

    ![Sikeres értesítés, a telefonszám csatlakoztatása, a szöveges üzenetek fogadásának lehetősége és a fiók](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A biztonsági adatok frissülnek, és szöveges üzenetküldéssel igazolhatja személyazonosságát a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során. Ha azt szeretné, hogy a szöveges üzenetküldés az alapértelmezett módszert alkalmazza, tekintse meg a jelen cikk [alapértelmezett biztonsági adatok módosítása módszerét](#change-your-default-security-info-method) ismertető szakaszát.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Szöveges üzenetküldés törlése a biztonsági adatok módszereiből

Ha a továbbiakban nem szeretne szöveges üzeneteket használni biztonsági információs módszerként, akkor a **biztonsági adatok** lapról távolíthatja el.

>[!Important]
>Ha tévedésből törli a szöveges üzeneteket, a visszavonás nem vonható vissza. A metódust újra fel kell vennie a jelen cikk [szöveges üzenetek beállítása](#set-up-text-messages-from-the-security-info-page) című szakaszának lépéseit követve.

### <a name="to-delete-text-messaging"></a>Szöveges üzenetek törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást a **telefonos** beállítás mellett.

    ![Hivatkozás a telefonos és szöveges üzenetküldési módszer törlésére a biztonsági adatokból](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. A **telefonszám** törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. A telefonszám törlése után a rendszer eltávolítja a biztonsági adatokból, és eltűnik a **biztonsági adatok** lapról. Ha a **telefon** az alapértelmezett módszer, akkor az alapértelmezett érték egy másik elérhető metódusra változik.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a szöveges üzenetkezelés alapértelmezett módszer legyen a munkahelyi vagy iskolai fiókjába való bejelentkezéskor a kétfaktoros ellenőrzés vagy a jelszó-átállítási kérelmek esetében, a **biztonsági adatok** lapon állíthatja be.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **biztonsági adatok** lapon válassza az **alapértelmezett bejelentkezési módszer** adatainak melletti **módosítás** hivatkozást.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Válassza a **telefonos szöveg (*_your_phone_number_*)** lehetőséget az elérhető módszerek legördülő listájában, majd válassza a **Confirm (megerősítés**) lehetőséget.

    ![Válassza ki a metódust az alapértelmezett bejelentkezéshez](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    A bejelentkezéshez használt alapértelmezett módszer a **telefonos szöveg (*_your_phone_number_*)** módosításaihoz.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.

    >[!Note]
    >Ha a lehetőségek némelyike hiányzik, valószínűleg azért, mert a szervezet nem engedélyezi ezeket a metódusokat. Ebben az esetben ki kell választania egy elérhető módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="next-steps"></a>Következő lépések

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com/) , vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
