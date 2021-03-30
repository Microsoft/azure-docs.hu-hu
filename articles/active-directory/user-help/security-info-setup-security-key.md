---
title: Biztonsági kulcs beállítása ellenőrzési módszerként – Azure AD
description: A biztonsági adatok (előzetes verzió) oldalának beállítása, amellyel ellenőrizheti, hogy az Ön személyazonossága gyors identitású online (FIDO2) biztonsági kulcsot használ-e ellenőrzési módszerként.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797666"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Biztonsági kulcs beállítása ellenőrzési módszerként

A biztonsági kulcsokat jelszó nélküli bejelentkezési módszerként is használhatja a szervezeten belül. A biztonsági kulcs egy olyan fizikai eszköz, amelyet egyedi PIN-kóddal használ a munkahelyi vagy iskolai fiókjába való bejelentkezéshez. Mivel a biztonsági kulcsok megkövetelik, hogy a fizikai eszköz és a hozzá tartozó eszközök csak akkor legyenek, mint a Felhasználónév és a jelszó erősebb ellenőrzési módszernek minősülnek.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Ha nem jelenik meg a biztonsági kulcs lehetőség, akkor előfordulhat, hogy a szervezet nem engedélyezi, hogy ezt a lehetőséget használja az ellenőrzéshez. Ebben az esetben egy másik módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

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

## <a name="what-is-a-security-key"></a>Mi az a biztonsági kulcs?

Jelenleg a [Fast Identity online (](https://fidoalliance.org/fido2/) FIDO2) jelszóval nem rendelkező hitelesítési protokollok használatával támogatjuk a biztonsági kulcsok számos tervét és szolgáltatóját. Ezek a kulcsok lehetővé teszik a munkahelyi vagy iskolai fiókjába való bejelentkezést, hogy egy támogatott eszközön és böngészőben hozzáférjenek a szervezet felhőalapú erőforrásaihoz.

A rendszergazda vagy a szervezete biztonsági kulcsot fog biztosítani, ha a munkahelyi vagy iskolai fiókjához szükséges. Különböző típusú biztonsági kulcsokat használhat, például egy USB-kulcsot, amelyet csatlakoztathat az eszközhöz vagy egy NFC-kulcshoz, amely egy NFC-olvasóra koppint. A gyári dokumentációban talál további információt a biztonsági kulcsról, beleértve a típus típusát is.

> [!Note]
> Ha nem tud FIDO2 biztonsági kulcsot használni, más jelszó nélküli ellenőrzési módszerek is használhatók, mint például a Microsoft Authenticator alkalmazás vagy a Windows Hello. További információ a Microsoft Authenticator alkalmazásról: [Mi a Microsoft Authenticator alkalmazás?](user-help-auth-app-overview.md). A Windows Hello szolgáltatással kapcsolatos további információkért lásd: a [Windows Hello áttekintése](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Előkészületek

A biztonsági kulcs regisztrálásának megkezdése előtt az alábbiaknak igaznak kell lenniük:

- A rendszergazda bekapcsolta ezt a funkciót a szervezeten belüli használatra.

- Olyan eszközön van, amely a Windows 10 2019-es verzióját futtatja, és egy támogatott böngészőt használ.

- A rendszergazda vagy a szervezete által jóváhagyott fizikai biztonsági kulccsal rendelkezik. A biztonsági kulcsnak FIDO2 és Microsoft-kompatibilisnek kell lennie. Ha bármilyen kérdése van a biztonsági kulccsal kapcsolatban, és hogy kompatibilis-e, lépjen kapcsolatba a szervezet ügyfélszolgálatával.

## <a name="register-your-security-key"></a>A biztonsági kulcs regisztrálása

Létre kell hoznia a biztonsági kulcsot, és egyedi PIN-kódot kell megadnia ahhoz, hogy a kulcs használatával bejelentkezzen a munkahelyi vagy iskolai fiókjába. Előfordulhat, hogy a fiókjában legfeljebb 10 kulcs van regisztrálva. 

1. Lépjen a **saját profil** lapra, https://myaccount.microsoft.com és jelentkezzen be, ha még nem tette meg.

2. Válassza a **biztonsági adatok**, majd a **metódus hozzáadása** lehetőséget, majd válassza a **biztonsági kulcs** lehetőséget a **metódus hozzáadása** legördülő listából.

    ![Hozzáadási mód mező, a biztonsági kulccsal kiválasztva](media/security-info/security-info-security-key-add-method.png)

3. Válassza a **Hozzáadás** lehetőséget, majd válassza ki, hogy milyen típusú biztonsági kulcsot használ, vagy **USB-eszközt** vagy NFC- **eszközt**.

    ![Válassza ki, hogy rendelkezik-e a biztonsági kulcs USB-vagy NFC-típusával](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Ha nem tudja biztosan, hogy milyen típusú biztonsági kulcsot tartalmaz, tekintse meg a gyártói dokumentációt. Ha nem biztos a gyártónál, kérjen segítséget a szervezet ügyfélszolgálatának.

4. A biztonsági kulcsot fizikailag elérhetővé kell tennie, majd a **biztonsági kulcs** mezőben válassza a **tovább** lehetőséget.

    ![Biztonsági kulcs indítási regisztrációs mezője](media/security-info/security-info-security-key-start-setup.png)

    Megjelenik egy új mező, amely segít az új bejelentkezési módszer beállításában.

5. Az **új bejelentkezési módszer beállítása** mezőben válassza a **tovább**, majd a következőket:

    - Ha a biztonsági kulcs egy USB-eszköz, szúrja be a biztonsági kulcsot az eszköz USB-portjába.

    - Ha a biztonsági kulcs egy NFC-eszköz, koppintson a biztonsági kulcsra az olvasója számára.

6. Írja be az egyedi biztonsági kulcs PIN-kódját a **Windows biztonsági** mezőbe, majd kattintson **az OK gombra**.

    Visszatér az **új bejelentkezési módszer beállítása** mezőbe.

7. Kattintson a **Tovább** gombra.

8. Térjen vissza a **biztonsági adatok** lapra, adjon meg egy nevet, amelyet később felismer az új biztonsági kulcshoz, majd kattintson a **tovább** gombra.

    ![Biztonsági adatok lap, a biztonsági kulcs elnevezése](media/security-info/security-info-security-key-name.png)

    A biztonsági kulcs regisztrálva van, és készen áll arra, hogy bejelentkezzen a munkahelyi vagy iskolai fiókjába.

9. A **biztonsági kulcs** mező bezárásához kattintson a **kész** gombra.

    A **biztonsági adatok lap a** biztonsági kulcs adataival frissül.

    ![Biztonsági adatok lap, az összes regisztrált metódussal](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Biztonsági kulcs törlése a biztonsági adatokból

Ha rossz helyre vagy már nem szeretne biztonsági kulcsot használni, törölheti a kulcsot a biztonsági adataiból. Habár ez leállítja a biztonsági kulcsot a munkahelyi vagy iskolai fiókjával, a biztonsági kulcs továbbra is tárolja az adatokat és a hitelesítő adatokat. Az adatok és a hitelesítő adatok biztonsági kulcsból való törléséhez kövesse a jelen cikk [Microsoft-kompatibilis biztonsági kulcs alaphelyzetbe állítása](#reset-your-security-key) című szakaszának utasításait.

1. A **Törlés** hivatkozásra kattintva távolítsa el a biztonsági kulcsot.

2. A **biztonsági kulcs törlése** mezőben kattintson az **OK gombra** .

    A biztonsági kulcsot törli a rendszer, és a továbbiakban nem fogja tudni használni a munkahelyi vagy iskolai fiókjába való bejelentkezéshez.

>[!Important]
>Ha tévedésből töröl egy biztonsági kulcsot, a jelen cikk a [biztonsági kulcs regisztrálása](#register-your-security-key) című szakaszában található utasítások alapján újra regisztrálhatja.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>A biztonsági kulcs beállításainak kezelése a Windows beállításaiból

A biztonsági kulcs beállításait a **Windows beállításai** alkalmazásból kezelheti, beleértve a biztonsági kulcs alaphelyzetbe állítását és az új biztonsági kulcs PIN-kódjának létrehozását is.

### <a name="reset-your-security-key"></a>Biztonsági kulcs alaphelyzetbe állítása

Ha törölni kívánja a fizikai biztonsági kulcsban tárolt összes fiókadatokat, vissza kell adnia a kulcsot a gyári alapértékekre. A biztonsági kulcs alaphelyzetbe állítása törli a kulcs összes adatát, így elkezdheti a használatot.

>[!IMPORTANT]
>A biztonsági kulcs alaphelyzetbe állítása törli a kulcs összes elemét, alaphelyzetbe állítja a gyári beállításokat.
>
> **Minden adat és hitelesítő adat törölve lesz.**

#### <a name="to-reset-your-security-key"></a>A biztonsági kulcs alaphelyzetbe állítása

1. Nyissa meg a Windows beállításai alkalmazást, válassza a **fiókok**, majd a **bejelentkezési beállítások**, majd a **biztonsági kulcs** elemet, és válassza a **kezelés** lehetőséget.

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosságának ellenőrzéséhez.

3. Kövesse a képernyőn megjelenő utasításokat az adott biztonsági kulcs gyártója alapján. Ha a kulcs gyártója nem szerepel a képernyőn megjelenő utasításokban, további információért tekintse meg a gyártó webhelyén.

4. A **kezelés** képernyő bezárásához kattintson a **Bezárás** gombra.

### <a name="create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

Létrehozhat egy új biztonsági kulcsot tartalmazó PIN-kódot a biztonsági kulcshoz.

#### <a name="to-create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

1. Nyissa meg a Windows beállításai alkalmazást, válassza a **fiókok**, majd a **bejelentkezési beállítások**, majd a **biztonsági kulcs** elemet, és válassza a **kezelés** lehetőséget.

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosságának ellenőrzéséhez.
3. Válassza a **Hozzáadás** lehetőséget a **biztonsági kulcs PIN-kódjának** területén, írja be és erősítse meg az új biztonsági kulcs PIN-kódját, majd kattintson **az OK gombra**.

     A biztonsági kulcsot a rendszer az új biztonsági kulcs PIN-kódjával frissíti a munkahelyi vagy iskolai fiókjával való használathoz. Ha úgy dönt, hogy ismét módosítja a PIN-kódot, akkor a **módosítás** gombra kattinthat.
4. A **kezelés** képernyő bezárásához kattintson a **Bezárás** gombra.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

Egy biztonsági kulcs regisztrálásához legalább egy további, a biztonsági ellenőrzési módszer regisztrálása szükséges. További információért tekintse meg az [Áttekintés szakaszt](./security-info-setup-auth-app.md) . 

## <a name="next-steps"></a>Következő lépések

- A jelszóval nem rendelkező ellenőrzési módszerekkel kapcsolatos további információkért olvassa el a [Microsoft Azure ad nyilvános előzetes kiadását a FIDO2 biztonsági kulcsaihoz, amely engedélyezi a jelszóval nem rendelkező bejelentkezések](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blogját, vagy olvassa el a [Mi az Microsoft Authenticator alkalmazás? és a](user-help-auth-app-overview.md) [Windows Hello áttekintő](https://www.microsoft.com/windows/windows-hello) cikkeket.

- Részletesebb információ a [Microsoft-kompatibilis biztonsági kulcsokról](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com/) , vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.