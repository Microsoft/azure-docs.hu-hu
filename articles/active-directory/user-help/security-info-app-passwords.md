---
title: Alkalmazás jelszavának létrehozása a biztonsági adatok (előzetes verzió) oldalról – Azure AD
description: Hozzon létre automatikusan létrehozott jelszavakat (alkalmazás-jelszavakat) a böngészőn kívüli alkalmazásokhoz, illetve bármely olyan alkalmazáshoz, amely nem támogatja a kétfaktoros ellenőrzést a szervezetében. Ez az alkalmazás jelszava különálló a normál jelszótól, és a biztonsági adatok lapról állítható be.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537004"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Alkalmazásjelszavak létrehozása a Biztonsági adatok (előzetes verzió) oldalról

Bizonyos alkalmazások, például az Outlook 2010, nem támogatják a kétlépéses ellenőrzést. Ez a támogatás hiánya azt jelenti, hogy ha kétlépéses ellenőrzést használ a szervezetében, az alkalmazás nem fog működni. A probléma megkerüléséhez létrehozhat egy automatikusan generált jelszót, amelyet az egyes nem böngésző alkalmazásokhoz használhat, a normál jelszótól eltérő módon.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Előfordulhat, hogy a rendszergazda nem teszi lehetővé az alkalmazás jelszavának használatát. Ha nem látja az **alkalmazás jelszavait** lehetőségként, azok nem érhetők el a szervezetben.

Az alkalmazások jelszavainak használatakor fontos megjegyezni:

- Automatikusan létrejönnek az alkalmazások jelszavai, és egy alkalmazásban egyszer kell létrehozni és beírni.

- Felhasználónként legfeljebb 40 jelszó adható meg. Ha a korlát után megpróbál létrehozni egyet, a rendszer arra kéri, hogy töröljön egy meglévő jelszót, mielőtt az újat hozna létre.

    >[!Note]
    >Az Office 2013-ügyfelek (beleértve az Outlookot is) támogatják az új hitelesítési protokollokat, és kétlépéses ellenőrzéssel használhatók. Ez a támogatás azt jelenti, hogy a kétlépéses ellenőrzés bekapcsolása után már nem lesz szükség az Office 2013-ügyfelekhez tartozó alkalmazások jelszavára. További információ: a [modern hitelesítés működése az office 2013 és az office 2016 Client apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) cikkhez.

## <a name="create-new-app-passwords"></a>Új alkalmazás jelszavának létrehozása

Ha kétlépéses ellenőrzést használ a munkahelyi vagy iskolai fiókjával, és a rendszergazda bekapcsolta a biztonsági információk felületét, a **biztonsági adatok** lapon hozhatja létre és törölheti az alkalmazáshoz tartozó jelszavakat.

>[!Note]
>Ha a rendszergazda nem kapcsolta be a biztonsági adatokkal kapcsolatos felhasználói élményt, kövesse az [alkalmazás jelszavainak kezelése kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md) című szakaszban található utasításokat és információkat.

### <a name="to-create-a-new-app-password"></a>Új alkalmazás jelszavának létrehozása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a bal oldali navigációs ablaktábla **biztonsági adatok** elemét, vagy a biztonsági **információ** blokkban található hivatkozásra, majd válassza a **metódus hozzáadása** elemet a **biztonsági adatok** lapon.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza ki az **alkalmazás jelszava** elemet a legördülő listából, majd kattintson a **Hozzáadás** gombra.

    ![Hozzáadás a Method Box-hoz, az alkalmazás jelszava kiválasztva](media/security-info/securityinfo-myprofile-addpassword.png)

4. Írja be annak az alkalmazásnak a nevét, amelyhez az alkalmazás jelszava szükséges, majd kattintson a **tovább** gombra.

    ![Képernyőkép, amely az "alkalmazás jelszava" lapot mutatja a megadott alkalmazás nevével.](media/security-info/securityinfo-myprofile-password-appname.png)

5. Másolja a jelszót a **jelszó** mezőbe, illessze be a jelszót az alkalmazás jelszó területére (ebben a példában az Outlook 2010), majd válassza a **kész** gombot.

    ![Alkalmazás jelszava lap az alkalmazás nevével](media/security-info/securityinfo-myprofile-password-copytext.png)

    A rendszer hozzáadja a jelszót, és sikeresen bejelentkezhet az alkalmazásba.

## <a name="delete-your-app-passwords"></a>Az alkalmazás jelszavainak törlése

Ha már nincs szüksége az alkalmazás jelszavát igénylő alkalmazás használatára, törölheti a társított alkalmazás jelszavát. Az alkalmazás jelszava törlésével felszabadítja az elérhető alkalmazás jelszavas helyeinek egyikét a jövőben való használatra.

>[!Important]
>Ha tévedésből törli az alkalmazás jelszavát, nem vonható vissza. Létre kell hoznia egy új alkalmazás jelszavát, majd újra be kell írnia az alkalmazásba, a jelen cikk [új alkalmazás jelszavainak létrehozása](#create-new-app-passwords) című szakaszában ismertetett lépéseket követve.

### <a name="to-delete-an-app-password"></a>Alkalmazás jelszavának törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást az alkalmazás **jelszava** lehetőség mellett az adott alkalmazáshoz.

    ![Az alkalmazás jelszava módszerének törlésére szolgáló hivatkozás a biztonsági adatokból](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Az **alkalmazás jelszavának** törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. Az alkalmazás jelszavának törlése után a rendszer eltávolítja a biztonsági adatokból, és eltűnik a **biztonsági adatok** lapról.

## <a name="for-more-information"></a>További tudnivalók

- További információ a **biztonsági adatok** lapról és a beállításáról: [biztonsági információk áttekintése](./security-info-setup-signin.md)